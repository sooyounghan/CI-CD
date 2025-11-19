-----
### needs
-----
1. 하나의 워크플로우에서 여러 개의 job을 정의해서 사용하는 경우가 많음
   - 여러 개의 job을 정의하는 방법
   - 기본적으로 job들은 병렬적으로 실행
<div align="center">
<img src="https://github.com/user-attachments/assets/b5ac844f-b26c-479d-81f3-eb903224ad10">
</div>

2. needs : Github Actions job 간에 종속성을 생성
   - 하나의 job이 다른 job 또는 여러 job이 완료될 때 실행되도록 사용, 즉, 여러 job 실행 순서 조절
   - 테스트 job이 실행되고, 그 job이 성공할 때, 배포 job이 실행되도록 구성

3. 실습
```yaml
name: needs
on: push

jobs:
  job1:
    runs-on: ubuntu-latest
    steps:
      - name: echo
        run: echo "job1 done"

  job2:
    runs-on: ubuntu-latest
    needs: [job1] # job1이 성공한 이후, 해당 job이 실행되도록 needs 키워드 사용
    steps:
      - name: echo
        run: echo "job2 done"

  job3:
    runs-on: ubuntu-latest
    steps:
      - name: echo
        run: | # exit1 : Github job이 강제 실패
          echo "job3 failed"
          exit1

  job4:
    runs-on: ubuntu-latest
    needs: [job3]
    steps: # job3이 실패하면, job4은 실행되지 않고 스킵
      - name: echo
        run: echo "job4 done" 
```
<div align="center">
<img src="https://github.com/user-attachments/assets/5cb2ebe4-aee2-4c4b-9319-8666b79b2ec7">
</div>

   - job4를 실행시킬 수 있는 방법 : Github Actions에서는 이런 상황을 위해서 종속성에 있는 job이 실패하더라도 실행할 수 있는 방법을 제공 (if condition)
