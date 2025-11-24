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

<img width="1028" height="610" alt="image" src="" />
