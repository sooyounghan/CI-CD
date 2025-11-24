-----
### 디버깅하는 방법
-----
1. Enable Debug Logging
   - Runner의 동작과 실행 정보를 상세하게 Logging하여, Workflow의 문제점을 상세하게 파악 가
   - step의 Log 상세도 증가
   - Github Actions의 Debug Log 기능 : 실행했던 job을 Re-run할 때, 이 기능을 활성화시킬 수도 있고, 혹은 설정을 통해 Default로 이 기능을 활성화하게 더 상세하게 표현 
<div align="center">
<img src="https://github.com/user-attachments/assets/1cba4fe3-f2b4-4597-a961-40199a6a65a2">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/660f82e7-800d-4dd5-a832-a214f6503d6c">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/a7de279e-96c3-438a-a74e-39b1957f734b">
</div>

2. Use tmate action
   - 로컬 혹은 온라인을 통해서 Github Action Runner에 접근 가능
   - Github Action Marketplace에서 많이 사용되는 액션 중 하나
   - 사용 방법
<div align="center">
<img src="https://github.com/user-attachments/assets/12330c4e-e451-42de-9036-7719809dc154">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/5053349e-e73f-4eec-9d54-3baff60a5a46">
</div>

   - 주의할 점 : timeout
     + 기본적으로 tmate가 실행되고 나서 종료하지 않는다면, Workflow 시간이 초과될 때까지 실행
     + 즉, Default timeout 시간까지 실행될 수 있음
     + 예) tmate action을 사용하는 step-level에서 timeout을 통해 특정 시간이 지나면, 종료가 되도록 설정
<div align="center">
<img src="https://github.com/user-attachments/assets/15db26d3-d6ca-4a2a-bfc7-66e240661387">
</div>

3. 실습
```yaml
name: debug
on: push

jobs:
  debug:
    runs-on: ubuntu-latest
    steps:
      - name: checkout
        uses: actions/checkout@v4
      - name: force fail # 의도적으로 실패하는 상황을 만듬
        run: | # 존재하지 않는 파일 읽기
          cat test.txt
      - name: uses tmate
        if: always() # 강제 실행
        uses: mxschmitt/action-tmate@v3
        timeout-minutes: 10
```
<div align="center">
<img src="https://github.com/user-attachments/assets/1e5a061d-2f82-4208-9b22-ca6c37fd3752">
</div>

  - tmate 사용 : ssh Command를 이용해 Github Action Runner에 접근 가
<div align="center">
<img src="https://github.com/user-attachments/assets/2bf9ecd9-9069-4445-9e62-775140465679">
</div>

 ```bash
ssh sshcode.tmate.io
```
 - Command를 통해 접속
   + 파일 확인(ls)하면 chekcout해서 가져온 파일 확인 가능
   + 만약, 디버깅이 필요하다면 직접 Github Actions Runner에서 작업을 진행 가능
   + 타임아웃 전에 작업을 종료하면 exit Command로 탈출 가능
<div align="center">
<img src="https://github.com/user-attachments/assets/235e1bcb-b971-4c0f-b18a-8d3bee59bc8d">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/9e84b78c-01b5-4049-ba23-446bf63aff8b">
</div>

4. tmate action을 사용해 원하는 시점에 Github Actions job에 접근해서 디버깅 가능
   - 현재 사용하는 방식은 SSH Command로 누구나 사용 가능하므로, 타인이 해당 SSH 값만 알면 접근 가능
   - 접근할 수 있는 사용자를 제한하려면 Warning 메세지에 나오는 것처럼 No Public SSH Key를 사용
<div align="center">
<img src="https://github.com/user-attachments/assets/fa106648-9d52-4697-8e01-8cf4a3f927e8">
</div>
