-----
### 키워드 기반 이슈 알림 자동화 
-----
1. 워크 플로우 구성
   - 이슈가 생성될 때 실행되어야 함
   - 특정 키워드가 포함되면 슬랙으로 공유해야 함
   - 키워드는 변동사항이 생길 수 있음 (추가, 삭제 등) : 변동사항이 생기더라도 Github Action Workflow의 복잡성이 커지면 안 되고, 관리하기 편한 구성이어야 함
    
2. 트리거 구성 : 이슈가 생성될 때 실행
   - issue event
   - types: [opened]

3. job 구성
   - jobs (단일 job) (키워드는 변동사항이 생길 수 있음 (추가, 삭제 등) - 추가적인 요구사항으로, 단일 job의 복잡성 증가)
     + 해당 방식은 키워드가 추가되면, step-level에서 if condition을 사용해 계속 스텝을 추가해야 하는 문제 발생
     + 첫 번쨰 step : if critical (Slack의 critical-issue 채널로 공유) - Issue에 critical이 포함되면, Slack의 critical-issue 채널로 공유
     + 두 번쨰 step : if normal (Slack의 normal-issue 채널로 공유) - Issue에 normal이 포함되면, Slack의 normal-issue 채널로 공유
     + 세 번쨰 step : if high (필요할 시, 추가해야 함)
     + 네 번째 step : if low (필요할 시, 추가해야 함)
<div align="center">
<img src="https://github.com/user-attachments/assets/f7bde848-cc8d-428e-9ee4-864702f4e43e">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/ab5ebf23-4d17-4078-a634-b3a50dfbba57">
</div>

   - 💡 결정 : jobs (2개 사용)
     + 첫 번째 job : get-keyword
       * keyworkd-list.txt 사용
       * Issue 제목이 키워드 리스트에 있다면, 그 값을 output으로 구성
     + 두 번째 job : slack
       * get-keyword의 output을 받아서 environment로 사용 (예) environment: critical)
       * Slack으로 메세지 전송

4. 복잡성과 관리하게 편하게 구성하는 방법
   - 키워드 관리
     + Github Actions Workflow에서 관리한다면, 키워드에 변동사항이 생길 때, Github Actions 코드를 수정해야 함
     + 따라서, 외부에서 관리해야 함 (예) keywordlist.txt : 따라서, Github Actions Workflow 로직 유지 가능 (코드 수정 필요 없음))
<div align="center">
<img src="https://github.com/user-attachments/assets/2684099d-0496-4bf3-b044-a8d14c8f1154">
</div>

   - Slack step 사용 구조
     + 키워드 개수만큼 Slack Step을 사용하는 구조 : 예) 키워드가 5개 : Slack Step 5개
       * if keyword == 'critical' : Slack : critical-issue
       * if keyword == 'normal' : Slack : normal-issue
       * 해당 구조를 사용한다면, 키워드가 추가되거나 삭제될 때, if-condition을 사용한 Slack Step 추가 및 삭제, 
       * 동일한 수정 사항 반영을 위해 5개 Step 업데이트(즉, Slack Payload를 동시에 업데이트)하므로, 관리하기 어려워짐
     + 💡 따라서, 키워드 개수와 상관없이 Slack Step 1개 사용 : Github Actions Workflow 로직 유지 가능 (코드 수정 필요 없음)
   - 동일한 Slack Webhook 이름 사용 (Slack Webhook URL은 secret으로 저장)
     + 사용 : Slack Webhook URL = 특정 채널이므로 값이 같을 수 없음
       * critical-issue의 Slack Webhook URL : ${{ secrets.critical-issue }}
       * normal-issue의 Slack Webhook URL : ${{ secrets.normal-issue }}
       * high-issue의 Slack Webhook URL : ${{ secrets.high-issue }}
       * 즉, Github Actions Workflow 코드를 지속적으로 변경해야 함
     + 하지만, 키워드가 무엇이든 간에 secret 이름을 사용할 수 있다면, 따로 Github Actions Workflow 코드를 업데이트 할 필요가 없음 : ${{ secrets.SLACK_WEBHOOK }}
       * environment 사용
       * environment: critical - ${{ secrets.SLACK_WEBHOOK }}
       * environment: normal - ${{ secrets.SLACK_WEBHOOK }}
         
5. 키워드가 추가 될 떄 해야되는 것
   - Github Actions 로직 수정 없이, keyword-list에 키워드 추가
   - environment 생성 및 Slack Webhook 설정
  
6. 키워드가 삭제 될 때 해야되는 것
   - Github Actions 로직 수정 없이, keyword-list에 키워드 제거
   - environment 제거

7. 실습
   - Slack에 criticla-issue 채널과 normal-issue 채널 생
   - Slack Webhook 구성 : Slack API - Incoming Webhooks - Add New Webhook - 채널 검색에서 두 채널 허용
   - environment 구성 : 이름은 critical과 normal로 구성
      + environment 구성 완료 후, secret 구성 : 해당 environment에 접근 후 Environment secrets - Add Environment Secret 선택 (이름 : SLACK_WEBHOOK_URL)
   - 외부에서 키워드 관리를 위한 keywordlist.txt 생성 후 normal, critical 키워드 추
   - Github Actions Workflow 구성 (main branch)
```yaml
name: issue-notify

on:
  issues:
    types: [opened]

jobs:
  get-keyword:
    runs-on: ubuntu-latest
    outputs:
      level: ${{ steps.get-keyword.outputs.level }}

    steps:
      - name: checkout
        uses: actions/checkout@v4

      - name: get keyword
        id: get-keyword
        run: |
          echo level=Undefined >> $GITHUB_OUTPUT

          keywords=$(cat keyword-list.txt)
          for keyword in $keywords; do
            if [[ "${{ github.event.issue.title }}" =~ "$keyword" ]]; then
              echo level=$keyword >> $GITHUB_OUTPUT
            fi
          done

      - name: get output
        run: |
          echo ${{ steps.get-keyword.outputs.level }}
```
   - Issue 생성 : 특정 키워드를 사용하지 않는 경우
<div align="center">
<img src="https://github.com/user-attachments/assets/d90da88c-7e29-43b9-9e8f-33611906f0ba">
</div>

<img width="931" height="529" alt="image" src="" />

   - Issue 생성 : 특정 키워드 사용하는 경우
<div align="center">
<img src="https://github.com/user-attachments/assets/7ef0ad42-2aa5-41cf-9dc7-f65760bac546">
</div>

   - Slack job 추가
```yaml
name: issue-notify

on:
  issues:
    types: [opened]

jobs:
  get-keyword:
    runs-on: ubuntu-latest
    outputs:
      level: ${{ steps.get-keyword.outputs.level }}

    steps:
      - name: checkout
        uses: actions/checkout@v4

      - name: get keyword
        id: get-keyword
        run: |
          echo level=Undefined >> $GITHUB_OUTPUT

          keywords=$(cat keyword-list.txt)
          for keyword in $keywords; do
            if [[ "${{ github.event.issue.title }}" =~ "$keyword" ]]; then
              echo level=$keyword >> $GITHUB_OUTPUT
            fi
          done

      - name: get output
        run: |
          echo ${{ steps.get-keyword.outputs.level }}

  slack:
    needs: [get-keyword]
    if: needs.get-keyword.outputs.level != 'Undefined'
    runs-on: ubuntu-latest
    environment: ${{ needs.get-keyword.outputs.level }}
    steps:
      - name: slack
        uses: slackapi/slack-github-action@v1.24.0 # 안정적인 버전
        with:
          payload: |
            {
              "attachments": [
                {
                  "pretext": "issue alert message",
                  "color": "28a745",
                  "fields": [
                    {
                      "title": "Level : ${{ needs.get-keyword.outputs.level }}", 
                      "short": true,
                      "value": "issue url : ${{ github.event.issue.html_url }}"
                    }
                  ]
                }
              ]
            }
    env:
      SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
      SLACK_WEBHOOK_TYPE: INCOMING_WEBHOOK
```
   - Issue 생성 : 특정 키워드를 사용하지 않는 경우
<div align="center">
<img src="https://github.com/user-attachments/assets/61a2dc34-41dd-4adb-a4b4-6f294f84deff">
</div>

   - Issue 생성 : 특정 키워드 사용하는 경우
<div align="center">
<img src="https://github.com/user-attachments/assets/c3ca9e7b-e4fc-4f7d-aac7-789d28aedf70">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/8a83bb05-1695-431c-95c0-bcaa3355613d">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/3484eb3e-ab12-424a-bf42-4aaccb260de6">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/d03e96be-4cf4-4468-89c1-6cb697df7f36">
</div>

   - 시각적으로 어떤 환경으로 사용했는지 Github Actions 변경
```yaml
name: issue-notify

on:
  issues:
    types: [opened]

jobs:
  get-keyword:
    runs-on: ubuntu-latest
    outputs:
      level: ${{ steps.get-keyword.outputs.level }}

    steps:
      - name: checkout
        uses: actions/checkout@v4

      - name: get keyword
        id: get-keyword
        run: |
          echo level=Undefined >> $GITHUB_OUTPUT

          keywords=$(cat keyword-list.txt)
          for keyword in $keywords; do
            if [[ "${{ github.event.issue.title }}" =~ "$keyword" ]]; then
              echo level=$keyword >> $GITHUB_OUTPUT
            fi
          done

      - name: get output
        run: |
          echo ${{ steps.get-keyword.outputs.level }}

  slack:
    needs: [get-keyword]
    if: needs.get-keyword.outputs.level != 'Undefined'
    runs-on: ubuntu-latest
    # environment: ${{ needs.get-keyword.outputs.level }}
    strategy: # 시각적으로 어떤 환경을 사용했는지 파악 가능 (matrix 사용)
      matrix: # slack-critical, slack-normal로 표현
        environment: ${{ needs.get-keyword.outputs.level }}
    environment: ${{ matrix.environment }}
    steps:
      - name: slack
        uses: slackapi/slack-github-action@v1.24.0 # 안정적인 버전
        with:
          payload: |
            {
              "attachments": [
                {
                  "pretext": "issue alert message",
                  "color": "28a745",
                  "fields": [
                    {
                      "title": "Level : ${{ needs.get-keyword.outputs.level }}", 
                      "short": true,
                      "value": "issue url : ${{ github.event.issue.html_url }}"
                    }
                  ]
                }
              ]
            }
    env:
      SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
      SLACK_WEBHOOK_TYPE: INCOMING_WEBHOOK
```
<div align="center">
<img src="https://github.com/user-attachments/assets/492cd381-260c-42f0-b9a3-a2e1f8a52a13">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/9beb9a25-d0ed-463f-be0d-945502739502">
</div>

  - 키워드를 새로 추가해 Workflow를 트리거 (Hi 추가)
    + keyword-list.txt에 추가
    + Slack 채널 생성 후 Webhook 설정 및 Environment secret 설정
    + issue 발행
<div align="center">
<img src="https://github.com/user-attachments/assets/06a0c06a-d764-4db9-ae26-c2265dc8f5d8">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/5c4caa3d-6e66-4823-a0bf-9653c0fbbadb">
</div>
