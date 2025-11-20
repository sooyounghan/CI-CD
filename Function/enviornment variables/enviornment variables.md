-----
### enviornment variables
-----
1. step이나 job에서 사용할 수 있는 환경 변수로, key-value 형태로 데이터 저장
2. output과 env
   - output : 같은 job, 다른 job 간에 데이터 공유
   - env : 동일한 job에서만 데이터 공유

3. 환경 변수 구성 방법
   - env 키워드를 사용하는 방법
   - 미리 정의하여 사용하는 방법

4. env 사용 방법
   - 워크플로우 내에서 정의
     + workflow-level에서 정의해 사용 가능
     + job-level에서 정의해 사용 가능
     + step-level에서 정의해 사용 가능

   - env 키워드를 사용해 level을 workflow로 구성 (workflow 레벨에서의 환경 변수 정의 가능)
   - env 키워드를 사용해 level을 job으로 구성 (job 레벨에서의 환경 변수 정의 가능)
   - env 키워드를 사용해 level을 step으로 구성 (step 레벨에서의 환경 변수 정의 가능)
<div align="center">
<img src="https://github.com/user-attachments/assets/abcc8281-0ba4-4bf9-a936-433a1e4dd7ad">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/9ac287f9-a96f-4f95-b58a-a0e823b57c10">
</div>

   - 출력값
     + get-env-1 : env.level이 workflow이므로 LEVEL-orkflow 출력
     + get-env-2 : env.level이 job이므로 LEVEL-job 출력
     + get-env-3 : env.level이 step이므로 LEVEL-step 출력
<div align="center">
<img src="https://github.com/user-attachments/assets/0241451a-3471-49f8-bf71-5d043e7a73fa">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/9f4a68ef-6491-4fb2-84c4-01775ab6d28a">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/b8919aae-b149-4618-971e-293c3bbbb54d">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/0febdb41-85a1-43e9-a5fe-c1f69932b9fb">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/6cdee05a-f11d-4081-b522-e02927e1f589">
</div>

   - 우선 순위 : step-level > job-level > workflow-level
   - 사용 방법 : ```echo "{key}={value}" >> $GITHUB_ENV : echo "test=hello" >> $GITHUB_ENV (${{ env.test}})```)
<div align="center">
<img src="https://github.com/user-attachments/assets/6807bd0d-0d25-4b21-86db-6bea7afe5aea">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/af862df7-eebc-4797-8073-4b0d79a3f2d2">
</div>

5. 실습
```yaml
name: var-1
on: push

env:
  level: workflow

jobs:
  get-env-1:
    runs-on: ubuntu-latest
    steps:
      - name: check env
        run: echo "LEVEL ${{ env.level }}"

  get-env-2:
    runs-on: ubuntu-latest
    env:
      level: job
    steps:
      - name: check env
        run: echo "LEVEL ${{ env.level }}"

  get-env-3:
    runs-on: ubuntu-latest
    env:
      level: job
    steps:
      - name: check env
        run: echo "LEVEL ${{ env.level }}"
        env:
          level: step

  get-env:
    runs-on: ubuntu-latest
    steps:
      - name: create env
        run: echo "level=job" >> $GITHUB_OUTPUT
      - name: check env
        run: echo "LEVEL ${{ env.level }}"
```

6. 미리 정의
   - 워크플로우 밖에서 정의
   - 미리 환경변수에 정의 : 이후 ```${{ vars.<environment-variables> }} : ${{ vars.LEVEL }}```
   - 워크플로우 밖에서 정의되므로, 정의된 환경변수를 바꾸면, 워크플로우 결과가 달라질 수 있음

7. 실습
   - Settings - Secret and Variables - Actions - Variables - New Repsoistory Variable - Name: level, Value: job으로 설정 (워크플로우 밖에서 환경 변수 작업)
```yaml
name: var-2
on: push

jobs:
  get-var:
    runs-on: ubuntu-latest
    steps:
      - name: get var
        run: echo ${{ vars.level }}
```
<div align="center">
<img src="https://github.com/user-attachments/assets/e94a0e9e-bf36-47ca-9d62-2dbf7081d86e">
</div>

   - 워크플로우 밖에서 정의되므로, 정의된 환경변수를 바꾸면, 워크플로우 결과가 달라질 수 있음 (job에서 job2로 변경 후, Actions Re-run)
<div align="center">
<img src="https://github.com/user-attachments/assets/33150b1a-9a88-4b4c-b371-b4a2211d7798">
</div>
