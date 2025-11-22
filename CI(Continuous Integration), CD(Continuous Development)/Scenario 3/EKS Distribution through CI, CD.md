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

7. 같은 방법으로 feature-cicd3-aws2 및 v6.0.0으로 재확인
8. v6.0.0 버전에서 v5.0.0 버전으로 Rollback
   - Actions - Filter workflow runs - branch: 해당 Rollback을 원하는 버전 선택
   - 해당 Workflow History 결과로 출력
     + open : pull request open
     + close : pull request close
     + PR이 Merge되면, close되므로 해당 History 선택해서 deploy job을 재실행하면, v5.0.0 버전으로 Rollback
