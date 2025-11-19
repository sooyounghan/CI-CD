-----
### push
-----
1. push
<div align="center">
<img src="https://github.com/user-attachments/assets/fa1c86fa-cf18-4216-bd02-b8e12f5e0763">
</div>

   - push 이벤트가 발생할 때 트리거가 되는 Github Action Workflow를 구성
     + Github Actions이 실행되려면 .github/workflow 디렉토리 내 yaml로 정의된 Github Action 파일이 존재해야함
     + VS Code 이용 또는 Add File을 통해 .github/workflows 생성
     + VS Code 이용 : Repository Clone - ./github/workflow/push.yaml 생성
```yaml
name: push-workflow # 액션창에서 표현될 시각화 이름
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

   - Actions 확인
<div align="center">
<img src="https://github.com/user-attachments/assets/cf3cb456-7ba3-4d27-87ab-83d7210d51ed">
</div>

2. .github/workflows 디렉토리에 존재해야지 트리거가 되며, 안에 디렉토리 내에 저장하면 트리거가 되지 않음
