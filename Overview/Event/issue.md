-----
### issue
-----
1. 소프트웨어 개발에서 발견한 버그, 기능 요청, 질문, 논의 등 추적 및 관리하는 도구
2. 프로젝트의 버그나 기능 개발 등의 진행 상황 파악 가능
3. Github 상단의 Issue 탭에서 생성 가능 (Issue - New Issue - Submit New Issue)
<div align="center">
<img src="https://github.com/user-attachments/assets/8028ebf3-a4d6-4069-8437-77a05256d809">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/f4d3f1a8-8166-4030-a9f5-ff47deb5a327">
</div>

4. Activity Type
<div align="center">
<img src="https://github.com/user-attachments/assets/96e2a691-3f0d-4339-94f8-6cc277da0412">
</div>

   - Default : 모든 Activity Type에 의해 트리거

5. Issue가 생성될 때만 트리거 되도록, Activity 타입을 open만 넣는 예제
   - open.yaml
```yaml
name: issue-workflow
on:
  issues:
    types: [opened]

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
<div align="center">
<img src="https://github.com/user-attachments/assets/a9418ed9-872c-40f2-9c34-775bd849a0dd">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/ba2bdaa2-dd76-43ad-8d19-5ba508fd2f05">
</div>

6. issue 이벤트는 push와 pull request와 다르게 기본 Branch에서만 트리거 된다는 특징을 가짐
