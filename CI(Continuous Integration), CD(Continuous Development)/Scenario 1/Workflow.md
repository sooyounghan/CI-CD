-----
### Workflow 구성
-----
1. cicd-1.yaml
```yaml
name: cicd-1
on:
  pull_request:
    types: [opened, synchronize, closed] # closed를 정의해야 Merge될 때 동작 제어 가능
    branches: [dev]
    paths:
    - 'my-app/**'

jobs:
   test: # CI
    if: github.event.action == 'opened' || github.event.action == 'synchronize'
    runs-on: ubuntu-latest
    steps:
    - name: checkout
      uses: actions/checkout@v4
  
  image-build:
    if: github.event.pull_request.merged == true
    runs-on: ubuntu-latest
    steps:
    - name: checkout 
      uses: actions/checkout@v4
  
  deploy: # CD
    runs-on: ubuntu-latest
    needs: [image-build]
    steps:
    - name: checkout
      uses: actions/checkout@v4
```

2. PR 생성을 위해 feature-cicd1 이라는 Branch 생성 후, my-app 디렉토리 내 app.js 파일 수정
3. feature-cicd1 Branch에서 dev Branch로 PR 생성 : PR이 트리거 되어 CI 프로세스 실행
<div align="center">
<img src="https://github.com/user-attachments/assets/f37790c5-4660-4a39-91c1-d91be09457d9">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/5daaf310-9861-46bc-aecb-c70540aee907">
</div>

4. 생성된 #27 PR에 Commit을 추가해 PR이 Synchronize, 즉 동기화될 때도 CI 프로세스를 트리거하는지 확인
  - feature-cicd1 Branch에서 my-app 디렉토리 내 app.js 파일 수정
  - Commit이 한 개 추가됨 확인
<div align="center">
<img src="https://github.com/user-attachments/assets/0528d6d9-93d9-4c8b-81c7-567288c3ac26">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/27e571bc-0acd-40a2-9c11-fb2b9486b6cc">
</div>

5. PR Merge
<div align="center">
<img src="https://github.com/user-attachments/assets/cd31eada-be13-47cb-bfd7-d084e0ea0e5d">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/09ec72af-43a1-42b0-afe2-9c6e38a4a424">
</div>

6. 각 job의 세부 구성
```yaml
name: cicd-1
on:
  pull_request:
    types: [opened, synchronize, closed] # closed를 정의해야 Merge될 때 동작 제어 가능
    branches: [dev]
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
        
  image-build:
    if: github.event.pull_request.merged == true
    runs-on: ubuntu-latest
    permissions: # Action들이 수행할 수 있는 작업의 범위나 권한을 지정하는 역할(workflow-level, job-level에서도 지정 가능)
      id-token: write 
      contents: read
    steps:
      - name: checkout the code
        uses: actions/checkout@v4
      - name: Configure AWS Credentials
        id: credentials
        users: aws-actions/configure-aws-credential@v4 # image-build job에서 AWS에 접근하기 위해 AWS action 사용하기 위해(OIDC 설정 시, permission 설정 필요) job에서 permssion 설정
        with: # AWS에 액세스 하기 위한 정보 (Region 정보(환경변수)와 역할(Secret) 정보)
          aws-region: ${{ vars.AWS_REGION }}
          role-to-assume: ${{ secrets.AWS_ROLE_TO_ASSUME }}
      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v2
        with:
          mask-password: 'true'
      - name: docker build & push
        run: | # ECR이 push하기 위한 REGISTRY는 민감한 정보이므로 secret우로 저장, REPOSITORY는 환경 변수로 지정 
          docker build -f Dockerfile --tag ${{ secrets.REGISTRY }}/${{ vars.REPOSITORY }}:${{ github.sha }}
          docker push ${{ secrets.REGISTRY }}/${{ vars.REPOSITORY }}:${{ github.sha }}
        # Commit을 이미지 태그로 사용하면 해당 이미지가 어떤 코드 변경에 기반한 것인지 명확하게 알 수 있으므로, 버전 관리 용이 및 문제 원인 추적 및 롤백에 편리 (Commit을 이미지 태그로 사용하는 것이 일반적) 

  deploy: # CD 
    runs-on: ubuntu-latest
    needs: [image-build]
    permissions: # AWS에 접근해야 하므로, permission 설정 필요
      id-token: write
      contents: read
    steps:
      - name: checkout the code
        uses: actions/checkout@v4
      - name: Configure AWS Credentials
        id: credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-regions: ${{ vars.AWS_REGION }}
          role-to-assume: ${{ secrets.AWS_ROLE_TO_ASSUME}}
      - name: setup kubectl # Kubernetest 커맨드 사용
        uses: azure/setup-kubectl@v3
        with: 
          version: latest
      - name: setup helm # Helm 커맨드 사용
        uses: azure/setup-helm@v3
        with:
          version: v3.11.1
      - name: access Kubernetes # AWS EKS에 접근하는 커맨드
        run: | # Kubernetes 클러스터 이름이 필요하므로 환경 변수로 지정
          aws eks update-kubeconfig --name ${{ vars.CLUSTER_NAME }}
      - name: deploy # Helm 커맨드를 사용 : 접근한 쿠버네티스 클러스터에 배포
        id: status # id를 지정 : Slack Action 사용을 위함 (성공, 실패)
        run: | # helm upgrade --install : 쿠버네티스 클러스터 내 my-app이라는 이름으로 애플리케이션 배포 (이미 존재하면 업그레이드, 아니면 새로 설치)
          helm upgrade --install my-app kubernetes/my-app --create-namespace --namespace my-app-${{ vars.SUFFIX }} \ 
          --set image.tag-${{ github.sha }} \
          --set image.repository=${{ secrets.REGISTRY }}/${{ var.REPOSITORY }}
        # kubernetes/my-app : 해당 Helm 차트의 위치 (Helm 커맨드 사용을 위함이며, 쿠버네티스에 배포할 리소스들의 집합을 의미)
        # --create-namespace : 지정된 네임 스페이스가 존재하지 않으면 새로운 네임스페이스를 생성하도록 지시 (처음 배포할 때는 이 옵션을 통해 생성)
        # --namespace my-app-${{ vars.SUFFIX }} : 배포할 쿠버네티스 네임스페이스 지정 (실제 값은 dev)
        # --set image.tag-${{ github.sha }} : 이미지의 태그를 현재 커밋으로 지정
        # --set image.repository : 이미지의 저장소를 지정
        # 파라미터 값으로 이미지 태그와 레포지태그 값을 전달하면 ECR에 Push 되었던 이미지를 바탕으로 애플리케이션이 배포

      - name: notify
        if: always()
        uses: slackapi/slack-github-action@v1.24.0
        with:
          payload: |
            {
              "text": "message",
              "block": [
                {
                  "type": "section", 
                  "text": {
                    "type": "mkrdwn"
                    "text": "Environment : dev, Deploy Result : ${{ steps.status.outcome }}, Repository : ${{ github.repository }}"
                  }
                }
              ]
            }
    env:
      SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
      SLACK_WEBHOOK_TYPE: INCOMING_WEBHOOK
```
