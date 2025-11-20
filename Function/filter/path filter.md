-----
### path filter
-----
1. path filter : 특정 경로의 파일이 변경될 때, Workflow에서 실행하고 싶다면 유용하게 사용 가능 (예) my-app이라는 디렉토리 내 파일이 업데이트 될 때만 실행)
2. 실습
```yaml
name: path-filter
on:
  push: # push event
    paths: # path filter 사용 : path에 변경사항이 생길 떄, 워크플로우가 트리거
      - ".github/workflows/part1/*" # .github/workflow/part1/ 내 모든 파일이 변경 될 때, 동작하는 Workflow

jobs:
  path-filter:
    runs-on: ubuntu-latest
    steps:
      - name: echo hello
        run: echo hello
```

   - .github/workflow/part1/push.yaml 내용 수정
```yaml
name: push-workflow 1 # 액션창에서 표현될 시각화 이름 변경
on: push # 어떤 이벤트로 GitHub Actions 시킬 지 지정하는 타입

jobs: # 실행시킬 jobs의 집합 (필수)
  push-job: # job의 이름
    runs-on: ubuntu-latest # job을 실행시킬 runner 지정
    steps: # 실행할 step 지정하는 step 집합
      - name: step1 # 실행시킬 step 이름 지정 (각 step 마다 명시적 지정 또는 생략 가능)
        run: echo hello world # 이 워크플로우는 push 이벤트 발생 시, 트리거가 되어 push job이라는 이름의 job 한 개가 실행되고, 이를 출력하고 종료
      - name: step2
        run: | # | : 멀티라인 커맨드
          echo hello world
          echo github action
```

<img width="941" height="454" alt="image" src="https://github.com/user-attachments/assets/7bf56d52-2571-4c55-81ee-a4ec2a3fa0b7" />

   - push.yaml이 변경될 때, Github Actions Workflow가 트리거 되지 않게 하도록 하려면 명시적 표시 필요 (! 기호 사용 : 제외 의미)
```yaml
name: path-filter
on:
  push: # push event
    paths: # path filter 사용 : path에 변경사항이 생길 떄, 워크플로우가 트리거
      - ".github/workflows/part1/*" # .github/workflow/part1/ 내 모든 파일이 변경 될 때, 동작하는 Workflow
      - "!.github/workflows/part1/push.yaml" # 해당 디렉토리내 push.yaml은 제외 (특정 파일 또는 디렉토리를 워크플로우 트리거에서 제외하는 기호 : !)

jobs:
  path-filter:
    runs-on: ubuntu-latest
    steps:
      - name: echo hello
        run: echo hello
```
   - .github/workflow/part1/push.yaml 내용 수정 : Workflow 트리거 미 발생
```yaml
name: push-workflow 2 # 액션창에서 표현될 시각화 이름 변경
on: push # 어떤 이벤트로 GitHub Actions 시킬 지 지정하는 타입

jobs: # 실행시킬 jobs의 집합 (필수)
  push-job: # job의 이름
    runs-on: ubuntu-latest # job을 실행시킬 runner 지정
    steps: # 실행할 step 지정하는 step 집합
      - name: step1 # 실행시킬 step 이름 지정 (각 step 마다 명시적 지정 또는 생략 가능)
        run: echo hello world # 이 워크플로우는 push 이벤트 발생 시, 트리거가 되어 push job이라는 이름의 job 한 개가 실행되고, 이를 출력하고 종료
      - name: step2
        run: | # | : 멀티라인 커맨드
          echo hello world
          echo github action
```
   - .github/workflow/part1/pull_request.yaml 내용 수정
```yaml
name: pull-request-workflow 1 # 변경
on:
  pull_request:
    types: [opened] # pull_request의 Activity Type 명시적 지정

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
<div align="center">
<img src="https://github.com/user-attachments/assets/9f874712-8b03-41f3-bc70-29d75fdbde3f"">
</div>

3. path filter를 통해서 원하는 path에 변경사항이 생길 때만 Github Actions 실행 가능
