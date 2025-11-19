-----
### schedule
-----
1. 특정 시간에 Github Actions Workflow를 실행
<div align="center">
<img src="https://github.com/user-attachments/assets/9b4260cb-8c2b-4e58-a4d7-de03d72f73c5">
</div>

2. schedule 사용할 때는, cron이란 키워드를 사용해 트리거된 시간 지정 가능
   - cron Syntax
<div align="center">
<img src="https://github.com/user-attachments/assets/2e05cd6a-59cf-4964-ae10-618d5fa92fa4">
</div>

3. Default Branch에 Github Actions Workflow가 존재해야만 동작
<div align="center">
<img src="https://github.com/user-attachments/assets/1d9bbf68-37a0-402f-9505-5aee1b01e402">
</div>

4. schedule 설정을 통해 Github Action Workflow를 구성해도, 지정한 시간에 정확히 동작하지 않을 때 존재
    - Github Action 사용에 대한 높은 부하가 있으면, Delay 또는 아예 실행되지 않을 수 있음
    - 5분 간격, 30분 간격의 작업은 드랍될 가능성이 존재
    - 정해진 시간에 꼭 실행되어야 할 작업이라면, Github Action Scheduling을 통한 작업은 적합하지 않음
