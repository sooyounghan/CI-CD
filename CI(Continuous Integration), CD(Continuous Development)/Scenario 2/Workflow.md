-----
### Workflow
-----
1. Workflow
```yaml
name: cicd-2
on:
  pull_request:
    types: [opened, synchronize, closed]
    branches: [dev, master] # master Branch 추가
    paths:
      - "my-app/**"

jobs:
  test: # CI
    if: github.event.action == 'opened' || github.event.action == 'synchronize'
    runs-on: ubuntu-latest
    steps:
      - name: checkout the code
        uses: actions/checkout@v4
      - name: setup-node
        uses: actions/setup-node@v3
        with:
          node-version: 18
      - name: Cache Node.js modules
        uses: actions/cache@v3
        with:
          path: ~/.npm
          key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
          restore-keys: |
            ${{ runner.os }}-node-
      - name: Install dependencies
        run: |
          cd my-app
          npm ci
      - name: npm build
        run: |
          cd my-app
          npm run build

  set-environment: # set-environment job
    if: github.event.pull_request.merged == true # PR Merge 시 실행
    runs-on: ubuntu-latest
    outputs: # 또 다른 job에서 사용하도록 job-level에서 output 설정
      environment: ${{ steps.set-env.outputs.environment }}
    steps:
      - name: set env
        id: set-env # output 사용을 위한 id 설정
        run:
          | # base_ref : Github Context로 Pull Request의 Target Branch 이름 의미 (feature -> dev 일 경우, dev)
          echo ${{ github.base_ref }} 
          echo "environment=dev" >> $GITHUB_OUTPUT

          if [[ ${{ github.base_ref }} == "master" ]]; then
            echo "environment=prod" >> $GITHUB_OUTPUT
          fi
      - name: check env
        run: echo ${{ steps.set-env.outputs.environment }}

  image-build:
    needs: [set-environment]
    runs-on: ubuntu-latest
    permissions:
      id-token: write
      contents: read
    strategy:
      matrix: # 받아온 output 시각화
        environment: ["${{ needs.set-environment.outputs.environment }}"]
    environment: ${{ matrix.environment }} # environment에 output 전달
    steps:
      - name: checkout the code
        uses: actions/checkout@v4
      - name: Configure AWS Credentials
        id: credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-region: ${{ vars.AWS_REGION }}
          role-to-assume: ${{ secrets.AWS_ROLE_TO_ASSUME }}
      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v2
        with:
          mask-password: "true"
      - name: docker build & push
        run: |
          docker build -f Dockerfile --tag ${{ secrets.REGISTRY }}/${{ vars.REPOSITORY }}:${{ github.sha }} .
          docker push ${{ secrets.REGISTRY }}/${{ vars.REPOSITORY }}:${{ github.sha }}

  deploy: # CD
    runs-on: ubuntu-latest
    needs: [set-environment, image-build]
    permissions:
      id-token: write
      contents: read
    strategy:
      matrix:
        environment: ["${{ needs.set-environment.outputs.environment }}"]
    steps:
      - name: checkout the code
        uses: actions/checkout@v4
      - name: Configure AWS Credentials
        id: credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-region: ${{ vars.AWS_REGION }}
          role-to-assume: ${{ secrets.AWS_ROLE_TO_ASSUME}}
      - name: setup kubectl
        uses: azure/setup-kubectl@v3
        with:
          version: latest
      - name: setup helm
        uses: azure/setup-helm@v3
        with:
          version: v3.11.1
      - name: access Kubernetes
        run: |
          aws eks update-kubeconfig --name ${{ vars.CLUSTER_NAME }}
      - name: deploy
        id: status
        run: |
          helm upgrade --install my-app kubernetes/my-app --create-namespace --namespace my-app-${{ matrix.environment }} \
          --set image.tag=${{ github.sha }} \
          --set image.repository=${{ secrets.REGISTRY }}/${{ vars.REPOSITORY }}

      - name: notify
        if: always()
        uses: slackapi/slack-github-action@v1.24.0
        with:
          payload: |
            {
              "text": "message",
              "blocks": [
                {
                  "type": "section",
                  "text": {
                    "type": "mrkdwn",
                    "text": "Environment : ${{ needs.set-environment.outputs.environment }}, Deploy Result : ${{ steps.status.outcome }}, Repository : ${{ github.repository }}."
                  }
                }
              ]
            }
    env:
      SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
      SLACK_WEBHOOK_TYPE: INCOMING_WEBHOOK

  create-pr: # 의도하지 않은 동기화 문제를 피하기 위해 개발 환경 배포에 성공하면 release Branch를 만드는 job
    if: needs.set-environment.outputs.environment == 'dev' # 개발 환경
    runs-on: ubuntu-latest
    needs: [set-environment, deploy] # 배포 job 이후
    steps:
      - name: checkout
        uses: actions/checkout@v4
      - name: gh auth login # Git 권한 받아오기
        run: |
          echo ${{ secrets.PERSONAL_ACCESS_TOKEN }} | gh auth login --with-token
      - name: create branch # 개발 환경 배포에 성공한 Commit History와 동일한 Commit History를 가지는 release/run-id Branch 생성하고 Remote에 Push
        run: |
          git checkout -b release/${{ github.run_id }}
          git push origin release/${{ github.run_id }}
      - name: create pr # PR 생성 (release (head) -> master (base))
        run: |
          gh pr create --base master --head release/${{ github.run_id }} --title "release/${{ github.run_id }} -> master" --body "release pr"
```

2. master Branch 생성
3. Environment - prod, dev 생성
4. dev Branch 기반으로 feature-cicd2 Branch 생성 후, App.js 변경)
   - feature-cicd2 Branch에서 dev Branch로 Pull Request
   - 해당 PR을 dev Branch로 Merge
<div align="center">
<img src="https://github.com/user-attachments/assets/e1b4f70b-2c26-46e4-8da8-9c02af0fe889">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/6835ee04-d3c5-48d5-9888-5984aa8b766b">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/35dc290d-52b9-424b-9a37-0047ec12f0d3">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/14eb8a0c-5647-4f38-8dac-50b417d6af0d">
</div>

  - 해당 PR을 Open 할 떄, test 실행
<div align="center">
<img src="https://github.com/user-attachments/assets/2849fa40-e2ff-4947-b839-2145488181ce">
</div>

  - release/run-id에서 master Branch로 Merge 실행 (create-pr은 if 조건에 의해 Skip)
<div align="center">
<img src="https://github.com/user-attachments/assets/c9962622-4e81-4db3-a665-ca78389b176c">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/c90b9003-99dc-4ee9-8ac7-ec3ac207f7ba">
</div>

  - dev와 prod라는 environment를 사용하였는데, AWS에 배포하는 작업을 진행할 때 Github Repository에서 이 설정도 같이 진행해 환경마다 사용할 환경 변수를 만들 예정
    
