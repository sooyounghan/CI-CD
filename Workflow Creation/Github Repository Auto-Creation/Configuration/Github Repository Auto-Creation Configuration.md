-----
### Github Repository 생성 자동화 구성
-----
1. Workflow 구성
   - Trigger 구성
   - job 구성
   - 고려 사항 1 : 정해진 Prefix를 사용하는 Repository 이름
     + Repository 이름을 어떻게 정할 것인지, 어떤 Prefix를 사용할 것인지 구성
     + inputs 기능 사용
   - 고려 사항 2 : Slack 알림 전송 (성공, 실패)

2. Trigger 구성 (input값 전달을 위해 github event : workflow dispatch 트리거 사용)
3. job 구성 (성격에 따라 job을 분리하는 것이 일반적)
   - inputs 값 기반의 Repository 생성 (성공, 실패 : Slack으로 전달)
   - 방법 1 : jobs (job 2개 사용)
     + 첫 번째 job : create-repo
       * inputs 값 기반 Repository 생성 : gh cli 사용
       * gh : Github Cli로서, 터미널에서 Github 기능을 커맨드로 제어가 가능 (Repository, Issue, Pull-Request, Github Actions 등 생성 / 삭제 / 관리 가능)하며, Github Runner에서 기본적으로 사용 가능
     + 두 번째 job : slack (Action 사용)
       * Repository 생성에 성공일 때, 문제 없음
       * Repository 생성에 실패하면, if: always()

   - 방법 2 : steps (단일 job)
     + 첫 번쨰 step : create-repo (Input 값 받아서 gh cli로 Repository 생성)
     + 두 번째 step : slack (if: always())

4. 실습
   - Secret 구성 : Github Reository Settings - Secret and Variables - Repository secrets 정의 및 Slack Webhook URI도 Repository secrets로 지정
   - Workflow 구성
```yaml
name: create-repo
on:
  workflow_dispatch:
    inputs:
      prefix: # 레포지토리 Prefix
        description: "set repo prefix"
        required: true
        default: "service"
        type: choice
        options:
          - example
          - service

      name: # 레포지토리 이름
        description: "set repo name"
        required: true
        default: "github-actions"
        type: string

jobs:
  create-repo-autimation:
    runs-on: ubuntu-latest
    steps:
      - name: gh auth login # Github 권한 받아오기
        run: |
          echo ${{ secrets.PERSONAL_ACCESS_TOKEN }} | gh auth login --with-token

      - name: create-repo # input 값으로 Repository 생성
        run: |
          gh repo create sooyoung-action/${{ inputs.prefix }}-${{ inputs.name }} --public --add-readme
```
<div align="center">
<img src="https://github.com/user-attachments/assets/0394789a-befe-42e3-a527-f9797ee69a49">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/0d90b42a-f424-4352-83b4-07c17cd9ec70">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/0c5bf18b-b230-47ff-bf18-a436e12465de">
</div>

   - slack action : Github Marketplace에서 확인 가능
<img width="1067" height="446" alt="image" src="https://github.com/user-attachments/assets/4c457809-67e4-4fa2-a2e9-7f656bde0407" />

```yaml
name: create-repo
on:
  workflow_dispatch:
    inputs:
      prefix: # 레포지토리 Prefix
        description: "set repo prefix"
        required: true
        default: "service"
        type: choice
        options:
          - example
          - service

      name: # 레포지토리 이름
        description: "set repo name"
        required: true
        default: "github-actions"
        type: string

jobs:
  create-repo-automation:
    runs-on: ubuntu-latest
    steps:
      - name: gh auth login # Github 권한 받아오기
        run: |
          echo ${{ secrets.PERSONAL_ACCESS_TOKEN }} | gh auth login --with-token

      - name: create-repo # input 값으로 Repository 생성
        id: create-repo # Slack 알람에 사용하기 위한 id 지정
        run: |
          gh repo create sooyoung-action/${{ inputs.prefix }}-${{ inputs.name }} --public --add-readme

      - name: slack # 슬랙 성공, 실패 알림
        if: always()
        uses: slackapi/slack-github-action@v1.24.0 # 안정적인 버전
        with:
          payload: |
            {
              "attachments": [
                {
                  "pretext": "create repo result",
                  "color": "28a745",
                  "fields": [
                    {
                      "title": "create repo result ${{ steps.create-repo.outcome }}", 
                      "short": true,
                      "value": "${{ inputs.prefix }}-${{ inputs.name }}"
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
<img src="https://github.com/user-attachments/assets/30c6c225-16e0-4e7c-9605-caa6fa6d2409">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/8ff1b79b-ed30-452e-a51f-f993aca3a474">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/b3a3ed9e-af01-48d0-b0c4-29b9d8cf9095">
</div>

  - 중복 Repository 생성 시, 슬랙 실패 알림
<div align="center">
<img src="https://github.com/user-attachments/assets/1b0f3ede-30a6-4dc6-a7fe-c7c2544207d0">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/6f25cd01-d15e-40c6-8505-1cbba1d818b6">
</div>
