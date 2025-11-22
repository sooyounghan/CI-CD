-----
### 시나리오 3 : CI / CD를 통해 EKS에 배포하기 (개발, QA, 운영환경)
-----
1. ECR 레포지토리 생성 (my-app-qa)  
2. Github 구성 (Environment)
   - qa 내 환경변수 구성
   - Variable 정의 : REPOSITORY(my-app-qa) / SUFFIX(qa)
3. 개발 환경 배포
   - feature-cicd3-aws1 Branch 생성 후, App.js 변경
   - dev Branch로 PR 생성 : CI 프로세스 실행 (test job 실행)
   - PR을 dev Branch로 Merge : image-build(dev) 및 deploy(dev) 실행 되어 EKS, EC2에 적재

4. 태그를 통해 QA 배포
   - git pull origin dev
   - git tag v5.0.1
   - git push origin v5.0.1

5. 확인하면 dev / qa 환경 모두 적용
6. create-pr 생성 완료 : release-master PR 생성
   - Open 되었으므로, test job 실행
   - master Branch에 Merge하면, prod 환경 배포로 진행
