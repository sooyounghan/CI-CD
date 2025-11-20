-----
### workflow dispatch
-----
1. event와 다르게 사용자가 원할 때, 수동으로 트리거할 수 있는 Github Actions의 Event
2. Default Branch에서만 동작
<div align="center">
<img src="https://github.com/user-attachments/assets/de2f44bb-75bf-465d-b449-fb5d10a39476">
</div>

3. 사용자가 Actions 탭에서 Run Workflow라는 버튼을 클릭하면 트리거 가능
<div align="center">
<img src="https://github.com/user-attachments/assets/5704f3d4-5b4c-4ce6-bc20-05d86b8aa7b9">
</div>

3. Input 값을 넣을 수 있음
   - Input 값을 넣으면 그 값을 Github에서 받아올 수 있음
<div align="center">
<img src="https://github.com/user-attachments/assets/018101a9-a618-4637-acad-7781c681e7e8">
</div>

   - Input 값으로는 다양한 데이터 타입이 존재 : string, number, boolean, choice(Github Action에서 제공해주는 데이터 타입) 등 존재
<div align="center">
<img src="https://github.com/user-attachments/assets/81dc77dd-23c1-47c4-bd54-8caaf7be652e">
</div>

   - Choice 데이터 타입 : 미리 옵션으로 저장해둔 dev나 prod들 중 클릭해서 선택 가능
<div align="center">
<img src="https://github.com/user-attachments/assets/4c1d78c3-356d-41a8-98ce-6a4729999458">
</div>

   - Workflow Dispatch 이벤트에서 Input 값을 정의하려면 이벤트에 inputs이라는 키워드 사용
     + Input 이라는 키워드를 사용하고 Github Actions에서 사용할 Input 값의 이름을 지정한 다음 description, required, default, type이라는 값 설정 가능
     + description : name 이라 input에 대한 설명을 작성
     + required : Input 값이 반드시 필요한지, 아닌건지 지정 (필요하다면 true, 생략해도 된다면 false)
     + default : Input 값의 기본 (name의 기본값은 github-actions, enviornment의 기본값은 dev)
     + type : 어떤 데이터 타입을 쓸 것인지 지정
<div align="center">
<img src="https://github.com/user-attachments/assets/75ff53e4-bc18-4eb4-a245-887a8499a9a6">
</div>

   - Input 값 설정을 완료한 이후 사용자가 Input 값을 넣어서 트리거하면, Github Actions Job에서 이 Input 값을 받아올 수 있음
     + name과 environment를 input 이름으로 지정했으므로, Github Actions Job에 inputs.name, inputs.environment으로 표현하면 해당 Input 값 사용 가능
<div align="center">
<img src="https://github.com/user-attachments/assets/ccbe5597-dbf1-4cae-b624-559a6badc537">
</div>

4. 실습
```yaml
name: workflow-dispatch
on:
  workflow_dispatch:
    # input 작업
    inputs:
      name:
        description: "set name"
        required: true
        default: "github-actions"
        type: string

      environment:
        description: "set env"
        required: true
        default: "dev"
        type: choice
        options: # choice 데이터 타입을 사용하려면, options라는 키워드를 사용해 사용자가 고를 수 있도록 설정
          - dev
          - qa
          - prod # 옵션 지정

# job 작업
jobs:
  workflow-dispatch-job:
    runs-on: ubuntu-latest # job을 실행시킬 runner 지정
    steps: # 실행할 step 지정하는 step 집합
      - name: step1 # 실행시킬 step 이름 지정 (각 step 마다 명시적 지정 또는 생략 가능)
        run: echo hello world # 이 워크플로우는 push 이벤트 발생 시, 트리거가 되어 push job이라는 이름의 job 한 개가 실행되고, 이를 출력하고 종료
      - name: step2
        run: | # | : 멀티라인 커맨드
          echo hello world
          echo github action
      - name: echo inputs
        run: |
          echo ${{ inputs.name }}
          echo ${{ inputs.environment }}
```
   - Actions - workflow-dispatch - Run workflow 클릭 - Input 값 설정 가능
<div align="center">
<img src="https://github.com/user-attachments/assets/2fc429fb-92e8-4696-b5aa-8e5d1c27af60">
</div>

   - Run Workflow를 누르면, 수동으로 트리거 발동하고, 확인하면 Input 값을 받아옴
<div align="center">
<img src="https://github.com/user-attachments/assets/af784af3-5199-42a6-bdd9-be1a9a45143f">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/7d7db70a-9b27-4757-afc1-22bad57529e9">
</div>

   - set name을 inflearn으로 설정하고, set env는 prod로 설정한 후 Run Workflow
<div align="center">
<img src="https://github.com/user-attachments/assets/4e4d527e-0f76-4f21-91be-3667ac891a70">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/51a3e8f0-e586-43cb-a919-f4dabd93d833">
</div>
