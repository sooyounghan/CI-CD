-----
### Workflow
-----
1. 코드
```yaml
name: cicd-3
on:
  push:
    paths:
      - "my-app/**"
    tags:
      - "v[0-9]+.[0-9]+.[0-9]+"
  pull_request:
    types: [opened, synchronize, closed]
    branches: [dev, master]
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

  set-environment:
    if: github.event.pull_request.merged == true || github.ref_type == 'tag' # ref_type : tag, brnach 확인
    runs-on: ubuntu-latest
    outputs:
      environment: ${{ steps.set-env.outputs.environment }}
    steps:
      - name: set env
        id: set-env
        run: |
          if [[ ${{ github.ref_type }} == "tag" ]]; then
            echo "environment=qa" >> $GITHUB_OUTPUT 
            exit 0
          fi

          if [[ ${{ github.ref_type }} == "branch" ]]; then
            echo "environment=dev" >> $GITHUB_OUTPUT
            if [[ ${{ github.base_ref }} == "master" ]]; then
              echo "environment=prod" >> $GITHUB_OUTPUT 
            fi
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
      matrix:
        environment: ["${{ needs.set-environment.outputs.environment }}"]
    environment: ${{ matrix.environment }}
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

  create-pr:
    if: needs.set-environment.outputs.environment == 'qa' # QA 환경 배포에 성공한 코드를 운영 환경에 반영할 수 있도록 PR 생성
    runs-on: ubuntu-latest
    needs: [set-environment, deploy]
    steps:
      - name: checkout
        uses: actions/checkout@v4
      - name: gh auth login
        run: |
          echo ${{ secrets.PERSONAL_ACCESS_TOKEN }} | gh auth login --with-token
      - name: create branch
        run: |
          git checkout -b release/${{ github.ref_name }}
          git push origin release/${{ github.ref_name }}
      - name: create pr
        run: |
          gh pr create --base master --head release/${{ github.ref_name }} --title "release/${{ github.ref_name }} -> master" --body "release pr"
```

2. Environment : QA 생성
3. feature-cicd3 Branch 생성 후, App.js 변경
4. feature Branch에서 dev Branch로 PR 생성 후, Actions 확인
<div align="center">
<img src="https://github.com/user-attachments/assets/31aeede8-b7c0-438a-9746-3d7d8acf9f94">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/95b24c9c-6047-4fcd-8a77-d8ba292df750">
</div>

5. 해당 PR Merge
<div align="center">
<img src="https://github.com/user-attachments/assets/337dc415-76fa-4563-9029-cf0a49a7804a">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/b1dbd49a-de3a-452b-8bda-7fbd524ffe45">
</div>

6. QA 환경 배포를 위해 Tag를 설정해 Github Actions Workflow에 Trigger
   - 터미널에 최신 dev Branch의 Commit을 받아옴 : git pull origin dev
   - 태그 설정 : git tag v1.0.0
   - 태그 전송 : git push origin v1.0.0 - tag event로 Github Actions event Trigger
     + 💡 참고 : Github Actions에서 Tag로 Trigger를 걸면, 해당 Tag가 어떤 Commit을 가리키느냐에 따라 그 Commit을 실행하므로 변경
     + 바꾸려고 하는 Commit으로 Checkout : git checkout <원하는 커밋해시 or branch>
     + tag 덮어쓰기 : git tag -f v1.0.0
     + 강제 덮어쓰기 push : git push origin v1.0.0 -f
<div align="center">
<img src="https://github.com/user-attachments/assets/b8495dfe-81ff-492f-89b1-cdb5bd21df3d">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/644034b2-76e5-4121-9ae4-ee4c83310f24">
</div>

   - 해당 PR이 Open 됨에 따라 Github Actions 실행
<div align="center">
<img src="https://github.com/user-attachments/assets/332deb44-9160-443d-a924-a4c01f23e9cc">
</div>

   - 해당 PR을 master Branch로 Merge
   - CD 프로세스 Trigger가 되어 실행되어 prod 진행
<div align="center">
<img src="https://github.com/user-attachments/assets/039296b4-37a7-4759-b48f-98a8fcb42fca">
</div>
