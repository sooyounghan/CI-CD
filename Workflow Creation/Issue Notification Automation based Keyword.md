-----
### 키워드 기반 이슈 알림 자동화
-----
1. 상황 가정
   - Issue들의 키워드에 따라 Slack으로 팀원들에게 알리는 일 담당
   - 수동으로 Issue를 각 채널에 공유 (번거롭고, 시간 소모적이므로 자동화하려고 시도)

2. Issue 생성 시, Issue 이름에 대한 규칙
   - critical 키워드가 존재 : Slack의 critical-issue 채널로 공유
   - normal 키워드가 존재 : Slack의 normal-issue 채널로 공유

3. 반영 사항 : Issue에 critical이 포함되면, critical-issue 채널로 공유, Issue에 normal이 포함되면 normal-issue 채널로 공유
