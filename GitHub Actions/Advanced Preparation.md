-----
### 사전 준비
-----
1. Github Organization 생성 및 업데이트, Repository 생성
   - Organziation 생성 후, Settings - Actions - General - Workflow permissions - Read and write permissions 선택)
   - Github 레포지토리 Fork : ```https://github.com/sangwondevops/github-actions-setting``` - 생성한 Organization 선택 - 
2. Github Personal Access Token (PAT) 생성 : PAT는 Github Actions이 Gituhub의 권한을 사용하기 위해 필요한 것
   - 우측 상단 Settings - Developer Settings - Personal access tokens - Tokens (classic) 선택 - Generate new token (classic) 선택 - 예제이므로 모두 활성화 - PAT 저장
3. Slack Webhook 설정
   - Slack : 협업을 위한 메세징 플랫폼 (실무에서 굉장히 많이 사용)
   - 사용 이유 : Slack으로 Github Actions이 메세지를 보내기 위함 
   - Slack 가입 후, 워크스페이스 생성
     + Slack Webhook 검색 - Your Apps - Create an App - From a Scratch - 이름 설정 - Workplace는 위에 생성한 워크스페이스 설정
     + 메뉴의 Incoming Webhooks 선택 후, Activate Incoming Webhooks 활성화
   - 메세지 발신 / 수신 지정 : Github 액션이 보낸 메세지를 받을 메세지를 Slack에서 받기 위한 채널 생성
     + 메뉴의 Incoming Webhooks - Webhook URLs for Your Workspace - Add New Webhooks - 워크스페이스와 채널 연결
     + 확인 : Command 이용 (Windows PowerShell에서 사용법)
       * curl.exe 명시적으로 사용 (PowerShell에서 curl.exe 를 명시하면 실제 cURL이 실행)
```
curl.exe -X POST -H "Content-Type: application/json" --data "{\"text\":\"Hello, World!\"}" "https://hooks.slack.com/services/XXX/YYY/ZZZ"
```

   - Invoke-WebRequest 형식으로 쓰기 (PowerShell 방식으로 보내려면 Headers를 딕셔너리로 전달)
```
Invoke-WebRequest -Uri "https://hooks.slack.com/services/XXX/YYY/ZZZ" `
  -Method POST `
  -Headers @{ "Content-Type" = "application/json" } `
  -Body '{"text": "Hello, World!"}'
``` 
       
