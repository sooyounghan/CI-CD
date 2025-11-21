-----
### 워크플로우 구성
-----
1. 트리거 구성
   - github event : pull request
   - path filter : my-app
   - branch filter : dev, master

2. job 구성
   -개발 환경(dev)에 배포할 때 사용하는 job
   - 운영 환경(production)에 배포할 때 사용하는 job
   - 배포 환경마다 다른 job을 사용해야 하는가?
     + dev
       * image-build-dev
       * deploy-dev

     + prod
       * image-build-prod
       * deploy-prod
<div align="center">
<img src="https://github.com/user-attachments/assets/4ff827b7-8db7-4f9c-a631-654b93ba11a7">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/49a09560-14dc-489a-a321-0ad4ace9d73f">
</div>

  - 위처럼 구성하면 배포할 환경마다 계속 job이 추가되는 구조 : 복잡성이 커지고 관리가 어려워짐

  - 일반적으로 CI/CD를 구성할 때, 사용하는 job은 환경 변수나 secret을 제외하고 구성이 유사 (image-build-dev / image-build-prod, deploy-dev / deploy-prod 끼리 거의 유사)
  - 따라서, 배포 환경마다 다른 환경 변수나 secret을 사용하도록 구성 : 단, 여러 개의 job이 아닌, 단일 job으로 구성
  - 환경마다 다른 job을 사용하는 것이 아닌 environment를 이용해 각 환경마다 환경변수, secret 설정 및 사용
    + 개발 / 운영 환경에 배포할 때 사용하는 job : image-build와 deploy
    + environment 기능을 통해 job을 재사용
      * environment: dev
        * 환경 변수
        * secret
      * environment: prod
        * 환경 변수
        * secret
<div align="center">
<img src="https://github.com/user-attachments/assets/5d049e19-c482-4c36-bd04-24f72627beae">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/dffd8452-0d44-4072-a479-7cd75ee59644">
</div>

   - 이러한 구성을 통해 output으로 환경을 받아온다면, 개발 환경 및 운영 환경 뿐만 아니라 환경이 더 늘어난다하더라도 단일 job을 재사용 가능
     
4. 최종 job 구성
   - test
   - set-environment
     + 현재 배포 환경 판단
       * dev Branch로 Merge 되면 개발 환경(dev)
       * master Branch로 Merge 되면 운영 환경(prod)
       * output 구성(set-env)
   - image-build : 시나리오 1 코드와 거의 유사하나 set-environment의 output(set-env)를 받아올 수 있도록 업데이트
   - deploy : 시나리오 1 코드와 거의 유사하나 set-environment의 output(set-env)를 받아올 수 있도록 업데이트
     + set-environment의 output이 dev
       * environment: dev
       * image-build(dev), deploy(dev)
     + set-environment의 output이 prod
       * environment: prod
       * image-build(prod), deploy(prod)
   - create-pr : release Branch 생성
     + checkout action 사용
     + Github 권한 받아오기
     + reelase / run-id Branch 생성
     + release Branch에서 master Branch로 PR 생성 (gh cli 이용)
     + 개발 환경 배포에 성공
       * 그 시점의 release Branch 생성 : 개발 환경 배포에 성공한 Commit History
       * release Branch - master Branch 사이 PR 생성
       * 해당 PR을 Merge하면 운영 환경으로 배포

5. 정리
   - Add Branch Filter : master
   - Update jjob
     + image-build
     + deploy
   - Create job
     + set-environment
     + create-pr

6. 시나리오 2 미리보기
   - CI (dev Branch)
<div align="center">
<img src="https://github.com/user-attachments/assets/c3de81b5-b0d8-46e4-aead-bacdf6881e20">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/9b85795a-b6a2-4205-ae67-9dab9908f8ac">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/42fe6076-b373-4f03-a1c3-424b7ee8e4b5">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/2a6da729-75c4-4a77-b8c4-c0ffaf6ccdcc">
</div>

   - CD (dev Branch)
<div align="center">
<img src="https://github.com/user-attachments/assets/dd442fb0-0b3c-4d59-8410-7ce6812b9765">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/930e51f8-44dc-4eff-80a4-bde1f8a9ad2f">
</div>

   - CI (master Branch)
<div align="center">
<img src="https://github.com/user-attachments/assets/45b21d29-039a-41ad-914f-90e6a3ef1979">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/ab085c68-d73d-44dd-b407-610a21cdc3b2">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/e9c995c4-2371-4538-a389-93117445cd48">
</div>

   - CD (master Branch)
<div align="center">
<img src="https://github.com/user-attachments/assets/9ce0c727-6824-430b-84ae-08d7d9438199">
</div>
