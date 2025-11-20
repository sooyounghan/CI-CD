-----
### if condition
-----
1. 특정 조건이 충족될 때, 실행되도록 하는 데 사용 : 작업의 흐름을 세밀하게 제어 가능
2. if Operator
<div align="center">
<img src="https://github.com/user-attachments/assets/abba7830-1b8b-44d8-aacd-84a9d8452222">
</div>

  - if: github.evnt_name == 'push' : push일 때만 실행
  - if: github.evnt_name != 'push' : push가 아닐 때 실행

2. 정의 범위
   - job-level에서 if 사용 : job 실행 여부 결정
   - step-level에서 if 사용 : step 실행 여부 결정

3. 예시
   - 해당 워크플로우는 push, workflow_dispatch event 적용
     + push event가 발생하면, job1 실행
     + push event가 발생하지 않으면, job2 실행
<div align="center">
<img src="https://github.com/user-attachments/assets/be20e1e2-b919-4ce3-bfaf-ed50d0414c1c">
</div>

   - push evnet
<div align="center">
<img src="https://github.com/user-attachments/assets/bf3ddffb-6386-488c-ae37-847ad2f87cd7">
</div>

   - push event가 아님
<div align="center">
<img src="https://github.com/user-attachments/assets/a98831cd-08b5-4e6c-a0c9-db0eaa6377dd">
</div>

   - step-level에서의 if문 사용 
<div align="center">
<img src="https://github.com/user-attachments/assets/9977bab8-b0f0-4adf-89c1-616efb2d4b8a">
</div>

4. 💡 filter와 if condition 비교
   - filter : workflow 트리거를 더 세밀하게 제어 (예) push event, branch filter: [dev, master] : dev / master branch 일 때만 트리거)
   - if condition : workflow가 트리거 된 이후 job과 step을 세밀하게 제어 (예) dev branch일 때, 첫 번쨰 job만 실행 / master branch 일 때 두 번쨰 job의 세 번쨰 step은 skip)

5. 실습 (main branch로 이동)
```yaml
name: if-1
on:
  push:
  workflow-dispatch:

jobs:
  job1:
    runs-on: ubuntu-latest
    if: github.event_name == 'push'
    steps:
      - name: get event name
        run: echo ${{ github.event_name }}

  job2:
    runs-on: ubuntu-latest
    if: github.event_name != 'push'
    steps:
      - name: get event name
        run: echo ${{ github.event_name }}
```
<div align="center">
<img src="https://github.com/user-attachments/assets/ce223977-611f-4ac6-92b3-79867d68daa2">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/d500f9d6-9f3e-479a-8265-92971006e274">
</div>

   - step-level에서의 if condition
```yaml
name: if-1
on:
  push:
  workflow_dispatch:

jobs:
  job1:
    runs-on: ubuntu-latest
    if: github.event_name == 'push'
    steps:
      - name: get event name
        run: echo ${{ github.event_name }}

  job2:
    runs-on: ubuntu-latest
    if: github.event_name != 'push'
    steps:
      - name: get event name
        run: echo ${{ github.event_name }}

  job3:
    runs-on: ubuntu-latest
    steps:
      - name: get event name
        if: github.event_name == 'push'
        run: echo "PUSH"

      - name: get event name
        if: github.event_name != 'push'
        run: echo "Workflow_DISPATCH"
```
<div align="center">
<img src="https://github.com/user-attachments/assets/2d586b6f-9f27-443d-8107-f99c03e09f68">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/807bf7a5-e560-4ca1-979b-ab2fc6142b6a">
</div>

6. if condition은 특정 job과 step을 강제로 실행 가능 - if: always()
   - job1은 강제 종료 설정을 했으므로, job2는 실행되지 않음 (종속성 문제)
<div align="center">
<img src="https://github.com/user-attachments/assets/f005c49d-526a-42df-971e-4de96cb4ffa5">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/ebcbcd82-f876-4b56-8c40-85b496c4fbf2">
</div>

   - job2 job-level에서 if: always()를 설정하면 강제 job2가 실행
<div align="center">
<img src="https://github.com/user-attachments/assets/e0a47219-279f-4d78-a1e3-dde73db6aa33">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/7a160f28-e623-457b-863a-fd29cc768c81">
</div>

   - step-level에서의 사용 : 첫 번쨰 스텝은 강제 종료되어, 두 번째 스텝은 Skip
<div align="center">
<img src="https://github.com/user-attachments/assets/b4e52bb1-556a-421d-b3be-87d9877ef980">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/a6ac1835-fda4-40d4-9170-878b5ae9920e">
</div>

   - job1 step-level에서 if: always()를 설정하면 강제 두 번쨰 스텝이 실행
<div align="center">
<img src="https://github.com/user-attachments/assets/4ea7b236-f575-4ab0-9f4c-597dcf9ae166">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/2502dae6-681b-45ba-959f-adb48eeb8bd4">
</div>

7. 실습
```yaml
name: if-2
on: push

jobs:
  job1:
    runs-on: ubuntu-latest
    steps:
      - name: exit 1
        run: exit 1
      - name: echo
        run: echo hello

  job2:
    needs: [job1]
    runs-on: ubuntu-latest
    steps:
      - name: echo
        run: echo hello
```
<div align="center">
<img src="https://github.com/user-attachments/assets/b7583921-dad8-485b-ad7f-0c381a851405">
</div>


```yaml
name: if-2
on: push

jobs:
  job1:
    runs-on: ubuntu-latest
    steps:
      - name: exit 1
        run: exit 1
      - name: echo
        run: echo hello

  job2:
    needs: [job1]
    runs-on: ubuntu-latest
    if: always()
    steps:
      - name: echo
        run: echo hello
```
<div align="center">
<img src="https://github.com/user-attachments/assets/e4c1b0c8-6d09-415e-8c49-b79385d61808">
</div>

  - step-level의 if: always()
```yaml
name: if-2
on: push

jobs:
  job1:
    runs-on: ubuntu-latest
    steps:
      - name: exit 1
        run: exit 1
      - name: echo
        if: always()
        run: echo hello

  job2:
    needs: [job1]
    runs-on: ubuntu-latest
    if: always()
    steps:
      - name: echo
        run: echo hello
```
<div align="center">
<img src="https://github.com/user-attachments/assets/2f91af6c-f447-4857-a683-72cba28b0b57">
</div>
