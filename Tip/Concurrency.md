-----
### Concurrency
-----
1. Workflow 실행의 동시성 제어
2. group
   - 서로 충돌하지 않게 하려는 작업 그룹을 식별하는 문자열
   - 두 개의 Workflow 실행이 동일한 group 값이라면, concurrency 설정에 따라 관리

3. cancel-in-progress
   - 동일한 그룹에 속한 새 Workflow가 시작될 때, 아직 실행 중인 Workflow를 취소할 지 여부를 결정
   - cancel-in-progress: true - 새 Workflow가 실행될 때, 이전 Workflow 취소
   - cancel-in-progress: false - 새 Workflow가 실행될 때, 이전 Workflow가 종료될 때까지 대기 상태

4. job-level, workflow-level에서 정의 가능
5. 즉, 이 기능은 Workflow 혹은 job이 동시에 여러 개 실행되는 것에 대해, 중복 실행을 방지하기 위함
