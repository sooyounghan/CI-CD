-----
### timeout
-----
1. 특정 시간 이상 실행되면, 자동으로 중단되도록 설정하는 기능
2. 특정 job 또는 특정 step 무한 루프에 빠지는 문제에 대해 불필요한 자원 소모 방지 가능
3. Default 값 : 360분
   - 워크플로우 내 특정 job 또는 특정 step이 정상적으로 종료되지 않고, 정상적 종료가 아닌 계속 실행되면 6시간 동안 지속 가능

4. timeout은 job level과 step level에서 정의해서 사용할 수 있고, 사용을 위해서는 timeout-minutes 키워드 사용
   - job-level에서 timeout-minutes의 경우 2로 설정 : 즉, job이 실행되고 2분 안에 자동으로 중단됨을 의미
     + 첫 번쨰 루프 스텝에서 1초에 한 번씩 카운트 값을 계속 올리는 무한 루프를 실행 : 타임아 시간이 2분이므로 2분 뒤에 루프 step 강제 종료
<div align="center">
<img src="https://github.com/user-attachments/assets/ba4a0579-aeae-4a54-9f28-d988dcb04d7a">
</div>

   - job-level과 step-level에서 tiemout-minutes를 설정
     + 해당 job은 3분 이내 자동 종료
     + 루프 step의 경우에는 1분 이내 자동 중단
<div align="center">
<img src="https://github.com/user-attachments/assets/044e9e7f-e4c1-4197-999f-52909b486202">
</div>

5. 실습 (job level)
```yaml
name: timeout
on: push

jobs:
  timeout:
    runs-on: ubuntu-latest
    timeout-minutes: 2 # 해당 타임아웃 값으로 인해 무한 루프만 2분간 실행되다가 종료
    steps:
    - name : loop
      run: | # 1초에 카운트가 1씩 올라가는 무한 루프 Step 스크립트
        count = 0
        while true; do
          echo "seconds: $count"
          count=$((count+1))
          sleep 1
        done
    
    - name: echo # echo hello 출력 (두 번쨰 실행은 실행되지 않음)
      run: echo hello
```
<div align="center">
<img src="https://github.com/user-attachments/assets/a1a22940-4cb6-45ff-8143-6ffd6db2aac5">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/97996505-c18e-4568-a6b9-4b63ea5af1ad">
</div>

   - 설정한 timeout으로 인해 첫 번째 step이 실패했고, 이후 step(echo step)도 실패

6. 실습 (step-level)
```yaml
name: timeout
on: push

jobs:
  timeout:
    runs-on: ubuntu-latest
    timeout-minutes: 2 # 해당 타임아웃 값으로 인해 무한 루프만 2분간 실행되다가 종료
    steps:
      - name: loop
        run: | # 1초에 카운트가 1씩 올라가는 무한 루프 Step 스크립트
          count=0
          while true; do
            echo "seconds: $count"
            count=$((count+1))
            sleep 1
          done
        timeout-minutes: 1 # 첫 번쨰 스텝이 1분 안에 완료되지 않으면, 두 번쨰 스텝을 실행하지 않고 바로 종료

      - name: echo # echo hello 출력 (두 번쨰 실행은 실행되지 않음)
        run: echo hello
```
