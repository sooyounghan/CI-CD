-----
### 다양한 이벤트
-----
1. 다양한 이벤트로, 하나의 워크플로우를 트리거
   - on 키워드를 이용해 Event 하나만 지정
<div align="center">
<img src="https://github.com/user-attachments/assets/354ac324-22e9-4bec-99d1-13b241061290">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/65652af4-9908-477f-913e-7a20b432b5d0">
</div>

2. Github Actions을 사용하다 보면, 하나의 워크플로우를 트리거시킬 수 있는 다양한 이벤트 정의가 필요할 수 있음
<div align="center">
<img src="https://github.com/user-attachments/assets/b5f7d480-2c10-4b00-9272-4adc28af10ee">
</div>

   - 워크플로우 파일 안에, 여러 개의 이벤트를 지정하면 됨
   - 이 워크플로우는 push 이벤트가 발생, issue 발생, 수동으로 워크플로우를 실행할 때 동작
```yaml
name: multiple-event-workflow
on:
  push:
  issues:
    types: [opened]
  workflow_dispatch:

jobs:
  multiple-event-job:
    runs-on: ubuntu-latest # job을 실행시킬 runner 지정
    steps: # 실행할 step 지정하는 step 집합
      - name: step1 # 실행시킬 step 이름 지정 (각 step 마다 명시적 지정 또는 생략 가능)
        run: echo hello world # 이 워크플로우는 push 이벤트 발생 시, 트리거가 되어 push job이라는 이름의 job 한 개가 실행되고, 이를 출력하고 종료
      - name: step2
        run: | # | : 멀티라인 커맨드
          echo hello world
          echo github action
```
<div align="center">
<img src="https://github.com/user-attachments/assets/024401e1-2795-439b-919e-4f1ff013d983">
</div>

