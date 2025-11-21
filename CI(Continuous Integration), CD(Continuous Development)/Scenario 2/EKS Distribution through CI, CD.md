-----
### 시나리오 2 : CI / CD를 통해 EKS에 배포하기 (개발, 운영환경)
-----
1. EKS에 운영 환경 레포지토리 생성
   - my-app-prod로 설정
  
2. Github 구성
   - Environment 추가 : prod 추가
   - Environment에서 environment-dev와 environment-prod에서 사용할 환경 변수 정
     + dev : REPOSITORY(my-app-dev) / SUFFX(dev)
     + prod : REPOSITORY(my-app-prod) / SUFFX(prod)
   - create-pr에서 PR을 생성하는데, PR을 생성하기 위해 PAT(PERSONAL_ACCESS_TOKEN) 사용 : Repository-level에서 정의
   - Workflow 코드
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
          helm upgrade --install my-app kubernetes/my-app --create-namespace --namespace my-app-${{ vars.SUFFIX }} \
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
                    "text": "Environment : ${{ matrix.environment }}, Deploy Result : ${{ steps.status.outcome }}, Repository : ${{ github.repository }}."
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

3. 개발 환경 배포
  - feature-cicd2-aws1 Branch 생성 후, App.js 값 변경
  - dev Branch로 Pull Request 생성하면, CI 프로세스 실행되어 통과
  - Cloud Sehll에서 kubectl get ns로 확인
  
4. 개발 환경 배포가 완료되면, release/run-id -> master PR 생성되며, open 시에, test 통과
5. master Branch로 Merge
<div align="center">
<img src="https://github.com/user-attachments/assets/b959c5dd-c3a5-484c-b91a-29527fd309a0">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/d2695f6b-fbf9-4f36-926d-6b8f7f26148c">
</div>
