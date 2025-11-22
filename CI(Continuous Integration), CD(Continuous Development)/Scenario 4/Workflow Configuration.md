-----
### 워크플로우 구성
-----
1. Trigger 구성
   - path filter : my-app
   - branch filter : dev, master
   - github event : pull request, push
   - tag filter : Vx.y.z (v1.0.0)

2. job 구성
   - test
   - set-environment
     + 현재 배포 환경 판단
     + dev branch로 Merge 되면 개발 환경(dev)
     + master branch로 Merge 되면 스테이징 환경(staging)
     + 태그 (Vx.y.z)로 push하면, QA환경(qa)
     + 아웃풋 구성(set-env)
   - image-build
   - deploy
   - create-pr
   - approve
     + deploy(staging) job이 성공한 이후에 실행되는 Staging 환경에 배포 job이 성공한 이후 대기 상태가 됨
     + envrionment Protection Rule 이라는 기능을 활용해 특정 환경에 대해서 권한을 가진 팀이나 멤버가 승인을 통해 대기 상태가 된 job을 실행하도록 할 수 있음
   - prod-deploy
     + approve job 성공한 이후 실행
     + deploy와 동일한 코드

3. 정리
   - Update job : set-environment
   - Create job : approve, prod-deploy

4. 미리보기
<div align="center">
<img src="https://github.com/user-attachments/assets/d4664bdd-9bbb-4e2d-b458-f47d29d8172d">
</div>

   - CI (dev Branch)
<div align="center">
<img src="https://github.com/user-attachments/assets/f8495d9d-59bb-4a3a-ad8f-82f93c4b04db">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/758a46c0-0080-469a-8596-27a60e23b4af">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/c3c8a852-11c0-449d-85c4-6674bc60a259">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/8533f1bc-45c8-4c57-9393-8979849b3b5c">
</div>

   - CD (dev Branch)
<div align="center">
<img src="https://github.com/user-attachments/assets/b99b77d4-9566-4ba8-9186-ba0a3586f47a">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/1b1fbd3e-08b1-4a88-801c-e59279d3eb5d">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/4010a4d1-7364-45cf-b057-852390290efc">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/08f92891-25a7-4b68-8af1-488f1900c934">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/6136a447-26c2-49c8-94b5-7c2a9982f21d">
</div>

   - CD (QA Tagging)
<div align="center">
<img src="https://github.com/user-attachments/assets/64ba7a2d-c704-4562-9902-d29306d3ee67">
</div>

   - CI (master Branch)
<div align="center">
<img src="https://github.com/user-attachments/assets/0a73198f-65c2-4baf-b040-32dabf66e0eb">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/f9729186-0734-49df-84d9-c96ff7fdbdc1">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/2384994a-8060-4bb2-8aa5-44f0b8ba997a">
</div>

   - CD (master Branch)
<div align="center">
<img src="https://github.com/user-attachments/assets/92eed0bf-f663-4524-9673-cf287d07c121">
</div>

   - approve Process
<div align="center">
<img src="https://github.com/user-attachments/assets/4b579d0e-04dc-419d-ad90-acfb26263e0d">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/a8ec57c5-2b29-41e4-bfe6-182c8b102c2a">
</div>
