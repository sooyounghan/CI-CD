-----
### 시나리오 1 : CI / CD를 통해 EKS에 배포하기
-----
1. Github 구성
   - Github Actions을 실행할 Github Repository 환경 변수 및 시크릿 구성
   - Github Repository의 Secrets and Variables - Actions에 Secret 및 Variable 추가
     + Variable : AWS_REGION, REPOSITORY, CLUSTER_NAME (/kubernetest/my-app/create-cluster.yaml의 name), SUFFIX(dev)
     + secrets : AWS_ROLL_TO_ASSUME (IAM Role - github-actions), REGISTRY(AWS ECR의 URL에서 뒤의 /my-app-dev 제외), SLACK_WEBHOOK_URL(SLACK deploy-history 채널 생성)

2. Kubernetes 환경에 배포 (💡 Kunbernetes 클러스터 사전 생성 필요)
   - 테스트를 위해, feature-cicd1-aws1 Branch 생성
   - my-app/src/app.js 변경
   - dev Branch로 PR 생성 : 이 때, dev Branch로 PR이 생성되었으므로 CI 프로세스 실행
   - 해당 Pull Request를 Merge하면, 이미지를 빌드하는 작업을 진행하고, 그 작업이 완료되면 Kubernetes에 배포하는 작업 진행
     + PR Merge 후, Actions 확인 및 CloudShell에서 kubectl get ns (현재 네임스페이스 조회) / kubectl get pods -n my-app-dev / kubectl get services -n my-app-dev : 아무것도 조회되지 않음
     + ECR에는 해당 이미지 추가됨 확인
