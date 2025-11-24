-----
### CI / CD를 통해 EKS에 배포하기 (모듈화 + 버저닝)
-----
1. feature-cicd6-aws1 Branch 생성 후, App.js 변경
2. dev Branch로 PR 생성 후, 확인하면 test job 성공
   - v1.0.0으로 checkout하면, 다음과 같은 정보 확인 가능 (이후 모든 job도 v1.0.0과 각 module로 설정한 값들에 맞춰 진행)
<div align="center">
<img src="https://github.com/user-attachments/assets/7042461e-c359-437b-aefe-38feb0d14dfd">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/3b0c89b2-42b1-4e6a-ae78-9e2920fff14f">
</div>

3. 해당 PR Merge하면, my-app-dev를 Module을 사용해 생성 (dev)
4. 최신 dev Branch를 받아온 뒤, tag v8.0.0로 Push (qa)
5. 생성된 PR을 Open하면, test job 실행되며, 이후 Merge (staging)
6. approve process가 진행되고, Review deployments로 Approve (prod)
   - 네임스페이스 조회 및 pod, 서비스 확인 (Cloudshell)
7. Versioning 방법
   - Module Repository v2.0.0 Branch 생성 후, Module 내 코드 수정 (v2.0.0)
   - Github-actions Repository - Settings - Actions - Variables - REF의 값을 v2.0.0로 설정, VERSION도 v2.0.0으로 지정
   - 따라서, Organziation Level은 v1.0.0로 지정했지만, 특정 Repository는 v2.0.0로 진행되어 버저닝 가
