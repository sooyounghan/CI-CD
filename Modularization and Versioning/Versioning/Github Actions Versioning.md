-----
### Github Actions에서 Versioning
-----
1. 중앙화된 하나의 Repository에서 모듈 관리
   - branch
   - tag
   - commit
<div align="center">
<img src="https://github.com/user-attachments/assets/b7da35de-2f2a-492f-8a6b-ec0e695710bc">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/ac857da3-d6b1-4786-bfc3-7db0d90ba8ff">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/ebea2b50-241d-4085-895a-c2be2b46a276">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/3528f240-945d-4dfd-bb1e-2c01c81b631a">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/dd1925b1-57f7-40ca-8f4a-32aeb5ec4e78">
</div>


<div align="center">
<img src="https://github.com/user-attachments/assets/f358e431-80ca-4ab0-86bb-db241ff6f777">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/28335772-9230-47df-a8f6-572497b4c7df">
</div>

2. checkout : ref 활용
   - branch
   - tag
   - commit

3. branch를 사용한 Versioning
   - branch name : v1.0.0
   - branch name : v2.0.0
   - branch name : v3.0.0-rc.1
   - 유연하고 쉽게 업데이트가 가능하며, Commit이 계속 업데이트 되므로, 모듈 업데이트 시 의도하지 않은 반영사항이 적용될 수 있음
   - 즉, 간단하지만 실수할 가능성이 존재

4. tag를 사용한 Versioning
   - tag는 특정 커밋으로 고정
   - tag가 삭제되지 않는다면, tag가 찍힌 Commit 기준의 파일로 모듈 사용
   - 만약 tag가 삭제되고, 재생성된다면 (동일 이름으로) Commit이 달라짐
   - 유연한 업데이트가 어려움
   - 유지만 된다면, 실수할 가능성이 없음

5. Commit을 사용한 Versioning
   - 가장 안전하게 원하는 Module 사용 가능
   - Commit Hash 값은 유니크하며, 동일한 Commit Hash 값을 가질 확률이 거의 없음
   - 그러나 Versioning할 수 없음 (아래와 같은 포맷 불가)
     + v1.0.0
     + v2.0.0
   - Versioning 하기 어려운 포맷이지만, 가장 안전

6. 업데이트가 필요할 때 마다 ref 값 수정
   - Workflow가 100개라면, ref: v1.0.0에서 v2.0.0으로 업데이트를 100번 해야하는가?
<div align="center">
<img src="https://github.com/user-attachments/assets/3767a60e-bbd4-4416-a2bd-cb42d4330fe8">
</div>

   - 환경 변수로 Version 관리
     + Organization-Level 업데이트 : 동시에 모든 워크플로우 버전 변경
     + Repository-Level 업데이트 : 일부 Repository 변경
     + Environment-Level 업데이트 : 일부 환경만 변경
<div align="center">
<img src="https://github.com/user-attachments/assets/521b9fdd-1c23-492b-9791-8bb153bc7c62">
</div>

   - checkout (ref: ${{ vars.VERSION }})
