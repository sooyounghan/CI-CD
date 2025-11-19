-----
### Github Actions 컴포넌트
-----
1. workflow
   - 하나 이상의 작업을 실행하는 구성 가능한 자동화 프로세스
   - .github/workflows 경로에 파일이 있어야 실행
   - 여러 워크플로우 생성 및 사용 가능
   - 예) Event 내에 트리거 된 워크플로우
<div align="center">
<img src="https://github.com/user-attachments/assets/dce473e0-1842-437b-a9f0-a8e33752caab">
</div>

2. event
   - 워크플로우 실행을 트리거하는 깃허브 레포지토리 내 특정 활동
   - 트리거 방식
     + 레포지토리 내 이벤트 기반 (Push, Pull Request 등)
     + 수동으로 설정 가능
     + 스케줄링 설정을 통해 원하는 시간에 설정 가능
     + 레포지토리 일부에서도 가능
<div align="center">
<img src="https://github.com/user-attachments/assets/347ee598-bb91-41d2-aa1c-711bc0f2828f">
</div>

3. runner
   - job을 실행할 수 있는 서버로, 각 runner는 하나의 job을 실행
   - 지원되는 Runner : Linux, Windows, MacOS
<div align="center">
<img src="https://github.com/user-attachments/assets/d0460c13-b364-4b35-acef-860d947d57b7">
</div>

4. job
   - 러너에서 실행되는 step의 집합
   - 액션과 스크립트 등을 step에 정의해서 사용
   - 기본적으로 각 job은 병렬로 실행
   - 예) 테스트 Job 실행 후, 배포 Job을 실행해야 하는 경우 (종속성이 필요한 경우) : needs
<div align="center">
<img src="https://github.com/user-attachments/assets/79ef685d-9225-4d73-afbb-fe4f8f43e51a">
</div>

5. step
   - job이 실행하는 개별 명령
   - 순차적으로 실행되며, 한 step이 실패하면 그 다음 step부터 실행되지 않음
<div align="center">
<img src="https://github.com/user-attachments/assets/6a49f6b4-8846-4b13-80fb-778c6f2e5c10">
</div>

6. action
   - 특정 작업을 수행하는 코드 조각
   - uses라는 키워드를 이용해서, action을 Load
   - 하나의 step에서 하나의 action만 가능
   - 종류
     + Github 공식 제공 action
     + Github Community에서 만든 action
     + 사용자가 직접 만든 action
<div align="center">
<img src="https://github.com/user-attachments/assets/043cc811-cade-4d33-b56c-2856492dcc99">
</div>
