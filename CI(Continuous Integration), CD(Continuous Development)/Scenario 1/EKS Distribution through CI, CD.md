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
     + deploy 처리가 완료되면 정상적 확인
<div align="center">
<img src="https://github.com/user-attachments/assets/3da3cc50-4c75-4969-86ce-0c501fa39df0">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/d77523df-ba0e-4a78-9519-fe8d9680545b">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/91a8097d-f04b-4e50-953e-82cc60aff248">
</div>

   - EC2 로드밸런서 확인 - DNS 이름 정보를 통해 접속하면 접속
<div align="center">
<img src="https://github.com/user-attachments/assets/23618183-9946-4f0f-981d-2fd24cf6a510">
</div>

   - Slack deploy-history 채널로 성공 메세지 확인 가능
<div align="center">
<img src="https://github.com/user-attachments/assets/44e481ae-0ce4-4872-8fdc-3446dd8bab60">
</div>

   - feature-cicd1-aws2 Branch 생성 후, app.js 수정 후 확인
     + PR Merge 후, Actions 확인 및 CloudShell에서 kubectl get ns (현재 네임스페이스 조회) / kubectl get pods -n my-app-dev / kubectl get services -n my-app-dev : 아무것도 조회되지 않음
<div align="center">
<img src="https://github.com/user-attachments/assets/392c664b-8324-4a71-8110-1c8b44137bc0">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/33d93fe1-f4d2-42b4-ac65-7a6b8a7229bc">
</div>

3. Kubernetes는 Rolling-Update 전략을 사용해 애플리케이션을 업데이트
   - Kubernetes에 배포하는 애플리케이션을 Pod라고 하며, 이 전략을 사용하면 새로운 버전의 Pod를 생성하면서, 동시에 이전 버전의 Pod를 종료
   - 처음 배포를 진행했을 때, Revision은 1이었고, 두 번째 배포가 진행되어 이때는 Revision 2가 됨 (무중단 배포)
   
