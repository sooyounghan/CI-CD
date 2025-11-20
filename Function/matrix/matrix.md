-----
### matrix
-----
1. 변수 기반으로 여러 개의 job을 실행하는 기능
2. matrix를 사용해서 하나의 job을 구성하면, 여러 개의 잡을 실행하도록 할 수 있음
3. 변수로 Windows, Linux, MacOS 설정 : 하나의 job을 구성해서 Runner가 다른 job을 3개 실행
   - Windows Runner에서 실행
   - Linux Runner에서 실행
   - MacOS Runner에서 실행

4. 변수로 Python 3.7 ~ 3.9 : 하나의 job을 구성해서 같은 코드 베이스에서 각 버전별로 테스트 구성
   - Python 3.7에서 테스트
   - Python 3.8에서 테스트
   - Python 3.9에서 테스트

5. matrix 기능이 없으면, 하나의 job 구성이 아니라, 여러 개의 job을 구성해야 할 수 있음
     - matrix 기능을 사용하지 않을 경우 : 각 job에서 Python 버전을 지정해 스크립트 실행 (job 3개 생성 후, 실행)
     - matrix 기능 사용 : job을 1개 구성했지만 job 3개가 실행
<div align="center">
<img src="https://github.com/user-attachments/assets/29e6823e-f52b-4063-af41-e3fd7ab4d762">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/2cc58f6e-39df-47af-adb9-27a1ab61ea51">
</div>

6. matrix 정의 : ```job.strategy.matrix.<변수명> = job.strategy.matrix.version ```
7. matrix 값 사용 : ```${{ matrix.<변수명> }} = ${{ matrix.version }}```


<div align="center">
<img src="https://github.com/user-attachments/assets/71134eda-da8e-4755-a488-dd13b1141dd6">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/a2f4aef4-5b2a-4e97-b0fa-719e30701d79">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/fa845e20-53d7-40d1-a2ef-dc62926b7a73">
</div>

8. matrix에서는 여러 개의 OS와 Version 지정 가능
<div align="center">
<img src="https://github.com/user-attachments/assets/8387712f-f5f0-40d8-bc27-840fe9c8077a">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/e3d32b13-5dd6-4719-a23a-d87bcb316d20">
</div>

   - OS : Mac일 때, job 3개 실행
     + OS : Mac, Version : 12
     + OS : Mac, Version : 14
     + OS : Mac, Version : 16
     
   - OS : Windows일 때, job 3개 실행
     + OS : Windows, Version : 12
     + OS : Windows, Version : 14
     + OS : Windows, Version : 16

   - OS : Ubuntu일 때, job 3개 실행
     + OS : Ubuntu, Version : 12
     + OS : Ubuntu, Version : 14
     + OS : Ubuntu, Version : 16

   - 총 9개의 job이 실행

9. 변수의 모든 가능한 조합에 대해 별도의 job 생성 : 즉, 다른 조합으로 각각 실행되어 9개 job 실행
<div align="center">
<img src="https://github.com/user-attachments/assets/4fb441eb-f00b-4b4b-ad45-977c6ce9efcb">
</div>

10. 실습
```yaml
name: matrix
on: push

jobs:
  get-matrix:
    strategy:
      matrix:
        os: [windows-latest, ubuntu-latest]
        version: [12, 14]
    runs-on: ${{ matrix.os }}
    steps:
      - name: check matrix
        run: |
          echo ${{ matrix.os }}
          echo ${{ matrix.version }}
```
<div align="center">
<img src="https://github.com/user-attachments/assets/75d724f3-dddc-452c-be36-b74335689743">
</div>
