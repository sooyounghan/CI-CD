-----
### context
-----
1. Workflow를 실행하는 환경에 대한 정보
<div align="center">
<img src="https://github.com/user-attachments/assets/526b9d10-1a69-42e6-902b-91c183d1e6e6">
</div>

   - 예) Github context 내 다양한 Property가 존재
<div align="center">
<img src="https://github.com/user-attachments/assets/31e81e6d-ce77-4360-9eff-6e6585adcdf3">
</div>

2. 사용 이유 : 더 유연한 워크플로우를 만들기 위해 사용
3. 예) Github context의 Property를 사용해 조건 설정 가능
   - dev Branch에 push 이벤트가 발생 : workflow에서 1번 job 실행
   - master Branch에 push 이벤트가 발생 : workflow에서 2번 job 실행

4. 실습
```yaml
name: context
on: workflow_dispatch

jobs:
  context:
    runs-on: ubuntu-latest
    steps:
      - name: github context
        run: echo '${{ toJSON(github) }}' # cotext의 Log 확인 (각 Context는 자바스크립트 객체이므로 출력을 하려면, 객체를 문자열로 반환하기 위해 toJSON()이라는 기능을 통해 Object 문자를 JSON 문자열로 변환 가능)
```
<div align="center">
<img src="https://github.com/user-attachments/assets/620cdbf6-a76a-4da8-88f0-19e3e59d4032">
</div>

   - Github context 안에 있는 다양한 Object 정보들을 저장
   - repository와 event_name 출력
```yaml
name: context
on: workflow_dispatch

jobs:
  context:
    runs-on: ubuntu-latest
    steps:
      - name: github context
        run: echo '${{ toJSON(github) }}' # cotext의 Log 확인 (각 Context는 자바스크립트 객체이므로 출력을 하려면, 객체를 문자열로 반환하기 위해 toJSON()이라는 기능을 통해 Object 문자를 JSON 문자열로 변환 가능)

      - name: check github context
        run: |
          echo ${{ github.repository }}
          echo ${{ github.event_name }}
```
<div align="center">
<img src="https://github.com/user-attachments/assets/eb240583-ecf7-409f-a53a-81d18e1574f">
</div>
