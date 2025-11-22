-----
### 워크플로우 구성
-----
1. 트리거 구성
   - path filter : my-app
   - branch filter : dev, master
   - Github event : pull request, push
   - tag filter: Vx.y.z
     + V1.0.0 (O)
     + 1.0.0 (X)
       
2. job 구성
   - test
   - set-environment
     + 현재 배포 환경 판단
     + dev Branch로 Merge 되면 개발 환경 (dev)
     + master Branch로 Merge 되면 운영 환경 (prod)
     + 태그 (Vx.y.z)로 push하면, QA 환경 (qa)
     + output 구성 (set-env)
   - image-build
   - deploy
   - create-pr
     + 검증된 QA 환경의 코드를 운영 환경으로 반영하기 위해 PR 생성
     + QA 환경 배포에 성공하면 release/tag Branch 생성
     + release Branch에서 master Branch로 PR 생성
     + 개발 환경 배포에 성공한 다음, QA 배포에 원할 때 Tag
       * QA 배포 진행
       * QA 배포에 성공했다면, 그 시점의 release Branch 생성 : QA 환경 배포에 성공한 Commit History
       * release Branch - master Branch 사이 PR 생성
       * 이 PR을 Merge하면 운영 환경으로 배포

3. 정리
   - Update Github event : push
   - Add tag filter : Vx.y.z
   - Update job
     + set-environment
     + create-pr

4. 미리 보기
<div align="center">
<img src="https://github.com/user-attachments/assets/e7bb0c67-3315-4e6e-8bf7-5d4bd1f5b7bc">
</div>

   - CI (dev Branch)
<div align="center">
<img src="https://github.com/user-attachments/assets/0f72838a-1074-437b-a27a-4f0c209ddf56">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/54a0cc73-c08c-466a-8874-2bb7358d4e53">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/6a4ab404-7c4f-41fd-a520-74030a89beb7">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/0ca46614-0c40-40b3-83f6-54ba63b851ae">
</div>

   - CD (dev Branch)
<div align="center">
<img src="https://github.com/user-attachments/assets/1e804360-7cb4-49bb-9cc2-645221af089f">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/58dd846f-be44-4d7a-87cd-22c65b9c83a7">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/a4075f0a-3fc4-492a-a5aa-5649a894c19e">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/9f6b69c4-b49c-4e7b-a411-5a4968873d49">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/d19cc769-620d-4e01-a312-a140c4b7f676">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/08c1a904-691e-4549-aad5-49b2f63f93da">
</div>

   - CD (QA Tagging)
<div align="center">
<img src="https://github.com/user-attachments/assets/02448a72-9c69-4a1b-806c-9f529c1d9a48">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/41966d38-b474-47fb-9d0f-60c3d32a1da3">
</div>

   - CI (master Branch)
<div align="center">
<img src="https://github.com/user-attachments/assets/f06c7169-a516-49e5-8591-7f06dad447bc">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/63916d9d-cbbc-48c0-88be-deb63f276e91">
</div>

   - CD (master Branch)
<div align="center">
<img src="https://github.com/user-attachments/assets/2fdd91b1-7064-42ad-bb2c-5427d8911911">
</div>
