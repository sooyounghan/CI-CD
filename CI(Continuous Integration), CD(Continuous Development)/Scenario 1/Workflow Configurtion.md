-----
### 시나리오 1 워크플로우 구성
-----
1. Workflow 구성
   - 트리거 구성
      + Github event : pull request (PR이 생성 및 동기화(CI), Merge(CD)될 경우에만 트리거)
      + path filter : my-app (my-app이라는 디렉토리가 변경될 때만 트리거)
      + branch filter : dev (개발 환경으로 사용하는 dev Branch에서만 트리거)
   - job 구성
      + test (CI)
         * cache action 사용
         * npm build
      + image-build
         * aws action
         * aws ecr action
         * docker
      + deploy
         * aws action
         * kubectl action
         * helm action
         * slack action

2. 미리보기
   - CI (dev Branch)
<div align="center">
<img src="https://github.com/user-attachments/assets/3fcead06-ba94-407f-a2c8-5f7548ffa717">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/87253298-c9e2-4a1a-bc56-667fdfa19b03">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/283c7c0e-35a6-4284-ab8d-30118d0a16f8">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/c0421b07-c5ae-48d2-8617-1c0665b9f004">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/67a39620-7abb-4f2c-84f4-e5256588eb44">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/e49d48a0-22f2-4bfd-b749-6e4234df700d">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/23222bb1-11b5-4cbb-9a32-89c7f136ddf9">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/25c8dfef-ce0b-4c6e-b683-5c00ea81f65d">
</div>

   - CD (dev Branch)
<div align="center">
<img src="https://github.com/user-attachments/assets/d1969949-cdc8-4675-ac41-e1fcf50992b9">
</div>
