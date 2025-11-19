-----
### pull request
-----
1. 변경사항 코드 저장소에 합병시키려고 할 때 사용
   - feature A Branch에서 코드를 수정하고 dev Branch로 pull reqeust를 생성될 때, 실행되는 Github Actions 생성
<div align="center">
<img src="https://github.com/user-attachments/assets/53eade64-07f7-41e9-9dd1-7fc4acfd2721">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/9e71596f-1736-46e7-a968-f95715462892">
</div>

2. pull_request.yaml
```yaml
name: pull-request-workflow
on: pull_request # pull request 동작

jobs: # 실행시킬 jobs의 집합 (필수)
  pull-request-job: # job의 이름
    runs-on: ubuntu-latest # job을 실행시킬 runner 지정
    steps: # 실행할 step 지정하는 step 집합
      - name: step1 # 실행시킬 step 이름 지정 (각 step 마다 명시적 지정 또는 생략 가능)
        run: echo hello world # 이 워크플로우는 push 이벤트 발생 시, 트리거가 되어 push job이라는 이름의 job 한 개가 실행되고, 이를 출력하고 종료
      - name: step2
        run: | # | : 멀티라인 커맨드
          echo hello world
          echo github action
```

3. Branch(pr-test) 생성 후 pull request 실행
   - part1의 push.yaml 일부 수정
   - dev Branch를 기반으로 만든 pr-test에 새로운 Commit 하나를 추가했으므로, Pull Request 하나 생성 (Compare pull & request를 통해 생성)
<div align="center">
<img src="https://github.com/user-attachments/assets/60719703-db46-4566-a866-6c14a387b338">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/8c1eda2e-0ba4-41a6-a77c-dfdea983ddd5">
</div>

   - pr-test Branch 에서 한 번 더 업데이트하면, Pull Requests - Commits에 하나의 커밋 추가되면서, Github Actions Workflow가 또 실행
     + 즉, pull reqeust가 Open될 때 그리고 동기화 될 때 Github Actions이 실행
     + 이유 : on: pull_request로만 작성하면, pull request와 관련된 Default Activity Type이 적용됨 (즉, 세부적으로 지정하지 않아, 기본값인 opened, synchronize, reopened 이벤트가 발생했을 때, 트리거)
     + 따라서, 세밀한 이벤트 제어를 하기 위해, 명시적으로 지정해줘야 함 
<div align="center">
<img src="https://github.com/user-attachments/assets/b31c4395-7f1c-43c8-b444-6a5b8237e364">
</div>

   - pull_request 타입 지정
```yaml
name: pull-request-workflow
on: 
  pull_request:
    types: [opened] # pull_request의 Activity Type 명시적 지정 > Types: []

jobs: # 실행시킬 jobs의 집합 (필수)
  pull-request-job: # job의 이름
    runs-on: ubuntu-latest # job을 실행시킬 runner 지정
    steps: # 실행할 step 지정하는 step 집합
      - name: step1 # 실행시킬 step 이름 지정 (각 step 마다 명시적 지정 또는 생략 가능)
        run: echo hello world # 이 워크플로우는 push 이벤트 발생 시, 트리거가 되어 push job이라는 이름의 job 한 개가 실행되고, 이를 출력하고 종료
      - name: step2
        run: | # | : 멀티라인 커맨드
          echo hello world
          echo github action
```

   - pr-test-2 Branch 생성 후 확인
     + 위의 과정 동일하게 적용 (Compare pull & request를 통해 생성)
   - Pull request Open 후, 수정하면 해당 pr에는 2개의 커밋이 존재하지만, Github Actions은 트리되지 않음
   - 물론, 모든 이벤트가 다양한 Activity Type이 있는 것은 아님 (Push의 경우에는 없음)
<div align="center">
<img src="https://github.com/user-attachments/assets/d73e46da-eeb4-4041-a5d9-2be606b3bfc1">
</div>

3. 따라서, 필요에 따라 어떤 이벤트들 경우에는 Activity Type을 이용해 더 세부적인 제어를 할 수 있
