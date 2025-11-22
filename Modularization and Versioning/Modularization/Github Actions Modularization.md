-----
### Github Actions에서 모듈화 방법 : Composite action
-----
1. 코드 재사용성을 높이는 것
2. 여러 step을 하나의 재사용 가능한 action으로 조합
3. 공통으로 사용되는 일련의 작업을 하나의 단위로 묶는 기능
4. 모듈을 사용하지 않는 test job
<div align="center">
<img src="https://github.com/user-attachments/assets/6e637b8a-1ba8-435d-bdec-b010a3049d6a">
</div>

5. 모듈을 사용하는 test job
<div align="center">
<img src="https://github.com/user-attachments/assets/9d4b588a-1445-4f65-a41c-a34e4ecf1dad">
</div>

6. 실제 모듈 구성 : action.yaml(yml)로 고정되어야 Composite Action 사용 가능
<div align="center">
<img src="https://github.com/user-attachments/assets/ec89d51e-ff2a-4dc8-81d4-37e46fae7123">
</div>

   - name : action 이름
   - author : 작성한 사람
   - description : 이 액션에 대한 설명
   - inputs : 만들어진 module에 input 값을 전달할 수 있는 방법
     + 예) inputs의 이름을 NODE_VERSION과 WORKING_DIRECTORY 디렉토리로 지정하고 이를 통해, 값을 전달 가능
<div align="center">
<img src="https://github.com/user-attachments/assets/f357e52e-6cc2-48d9-b70a-c14e397429f4">
</div>

   - inputs
     + description : 필수로 정의해야하는 값으로, 해당 input에 대한 설명
     + requried, default는 Optional 값
       * required가 true라면 with 키워드를 사용해 input 값 전달, false라면 with 키워드를 사용해서 전달하는 것은 선택
       * default는 기본 값 : 만약 required가 false라면 with 키워드를 통해 input 값을 전달하지 않는다면 default 값으로 NODE VERSION은 18, WORKING_DIRECTORY는 my-app으로 설정 
<div align="center">
<img src="https://github.com/user-attachments/assets/2972634c-d5dc-4c9e-9e3c-54f09c82a20f">
</div>

   - using : "compsite" 키워드 사용 (이 키워드가 있어야만 Composite 액션 사용 가능)
   - 이후, steps을 정의 (test job의 주요 로직 그대로 사용)
     + 앞서 설정한 input을 사용하기 위해 inputs.NODE_VERSION, intputs.WORKING_DIRECTORY 디렉토리로 정의
     + 이렇게 구성한 다음, 모둘을 사용할 때 inputs 값을 넣어주게 되면, 그 값을 받아와서 그대로 실행
   - 💡 composite Action은 각 step에서 run 키워드를 사용한다면, shell: bash라는 값을 넣어줘야 제대로 실행
<div align="center">
<img src="https://github.com/user-attachments/assets/78fde49b-7e51-4b76-bcc9-373201c0bedd">
</div>

   - 모듈을 사용하지 않는 test job과 사용하는 test job : 4개의 step (setup-node, cache, Install Dependencies, npm build)를 하나의 action으로 만듬
<div align="center">
<img src="https://github.com/user-attachments/assets/48697e4a-2223-4470-94b4-036d517b1dfd">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/1a8ed1be-0d47-496b-a513-596736a904f0">
</div>

   - 이 모듈을 사용해 최종적으로 모듈을 사용하는 test job 구성 가능
   - use test module step은 실제로 하나의 step이지만, setup-node Action과 cache Action, Install Dependencies, npm build 총 4개의 step이 Composite action을 통해 하나의 재사용이 가능한 action으로 만들어지고, 적절한 input 값 설정을 통해 다양한 워크플로우에서 test라는 Module로 사용할 수 있도록 함
<div align="center">
<img src="https://github.com/user-attachments/assets/d8c1f433-2e64-41ad-8005-fce51a9ce8be">
</div>

   - set-environment job (모듈을 사용하지 않음)
     + github.ref_type, github.base_ref라는 Github Context 조건을 사용해서 Tag인지, Branch인지 확인하고 조건에 따라 환경을 output으로 구성
<div align="center">
<img src="https://github.com/user-attachments/assets/76dca201-f116-4000-b218-99438b647fe7">
</div>

   - set-environment job (모듈을 사용)
<div align="center">
<img src="https://github.com/user-attachments/assets/cddce3df-4fd3-4e6b-b556-39198751b7e7">
</div>

   - github-actions-moudle이라는 Path에서 github-actions-moudle이라는 checkout해서 가져옴 (test job과 달리 module Repository만 checkout)
   - actions-module/common/set0-environment Path에 존재하는 action을 사용하고, ref_type과 base_ref를 input 값으로 전달
<div align="center">
<img src="https://github.com/user-attachments/assets/d189380f-97f5-4098-84dc-ae0871ecf48f">
</div>

   - 실제 모듈 구성
<div align="center">
<img src="https://github.com/user-attachments/assets/ea304528-e0d7-43ec-a11f-51650973f85b">
</div>

   - inputs에는 REF_TYPE과 BASE_REF 타입 정의
   - outputs에는 environment가 정의
   - composite action을 사용할 때도 output을 구성할 수 있음 : step에서 outputs을 정의하고, 이 outputs을 composite의 output으로 설정 가능
     + 단, description(output에 대한 설명)과 value(output 값)라는 파라미터가 있고, 둘 다 필수로 정의해야 함
<div align="center">
<img src="https://github.com/user-attachments/assets/a9437632-6e07-47f7-9e47-4c53cc4c84bf">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/0f2731c2-58fe-4e3a-8e79-979842d9e45c">
</div>

   - intput 값으로 설정한 REF_TYPE과 BASE_REF를 inputs Context를 사용해 inputs.REF_TYPE, inputs.BASE_REF로 사용
   - run 키워드를 사용하기 위해 shell: bash라는 값을 추가
<div align="center">
<img src="https://github.com/user-attachments/assets/4ef8d017-b647-4d2d-8cdd-bfef832aefe0">
</div>

4. 모듈 사용 job
   - test
   - set-environment
   - image-build
   - deploy
   - create-pr
   - prod-deploy
