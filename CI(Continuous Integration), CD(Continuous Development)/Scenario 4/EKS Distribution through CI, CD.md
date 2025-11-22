-----
### 시나리오 4 : CI / CD를 통해 EKS에 배포하기 (개발, QA, Staging, 운영환경)
-----
1. Settings - Environment로, staging 환경 변수 추가
   - 운영 환경과 사용하는 REPOSITORY가 동일하므로 my-app-prod 설정, SUFFIX는 staging 설정

2. feature-cicd4-aws1 생성 후, App.js 변경
3. feature-cicd4-aws1에서 dev Branch로 PR 생성 후, Merge (dev) 후, AWS 확인
4. tag v7.0.0을 설정해 Commit (qa) 후, AWS 확인
5. release/v7.0.0 -> master PR 오픈 시점에 test job 실행 확인 후, Merge를 통해 master Branch에 반영 (staging)
   - image-build를 통해 my-app-prod에 이미지 추가되고, deploy 진행 (namespace가 my-app-staging으로 저장)
<div align="center">
<img src="https://github.com/user-attachments/assets/d7169950-acf1-43b3-989c-56d8f8627313">
</div>

   - approve 승인 진행 (prod) 
