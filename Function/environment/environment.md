-----
### environment
-----
1. 특정 환경에서만 사용 가능한 환경변수와 시크릿을 관리할 수 있는 기능 (Repository에서 정의할 수 있음)
2. 환경변수와 시크릿은 다양한 레벨에서 정의 가능
   - organziation-level (Public Repository로 설정해야 가능)
   - repository-level
   - environment-level
<div align="center">
<img src="https://github.com/user-attachments/assets/7e6bd6b9-0eb3-4b5f-8e7a-6e2e0a8a6107">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/eb0418fb-e0f1-4434-91f0-edfb78673af8">
</div>

3. 그림
   - organization-level에서만 환경 변수와 시크릿이 정의
<div align="center">
<img src="https://github.com/user-attachments/assets/7a8ba66e-c031-4d73-a9df-e40d9edccae2">
</div>

   - organization-level와 repository-level에 환경 변수와 시크릿이 정의
<div align="center">
<img src="https://github.com/user-attachments/assets/8e373cb8-7bc0-42af-9e3d-155ef87c1029">
</div>

   - organization-level와 repository-level, environment-level에 환경 변수와 시크릿이 정의
<div align="center">
<img src="https://github.com/user-attachments/assets/acf2303b-5d8e-4d30-87e6-e22e8b71ae5d">
</div>

4. 우선순위 : environment-level > repository-level > organization-level
5. 실습
   - organization-level에서만 환경 변수와 시크릿
```yaml
name: environment
on: push

jobs:
  get-env:
    runs-on: ubuntu-latest
    steps:
      - name: check env & secret
        run: |
          echo ${{ vars.level }}
          echo ${{ secrets.key }}
```
<div align="center">
<img src="https://github.com/user-attachments/assets/17a0c968-6580-4d29-8389-604884ca8122">
</div>

   - organization-level와 repository-level에 환경 변수와 시크릿이 정의 (지정 후, 다시 Re-run)
<div align="center">
<img src="https://github.com/user-attachments/assets/1129bf23-56c1-4e02-9104-f33f30990304">
</div>

   - organization-level와 repository-level, environment-level에 환경 변수와 시크릿이 정의 (Settings - Environments - New Environments 생성 후, secret과 variable 설정)
```yaml
name: environment
on: push

jobs:
  get-env:
    runs-on: ubuntu-latest
    steps:
      - name: check env & secret
        run: |
          echo ${{ vars.level }}
          echo ${{ secrets.key }}

  get-env-dev:
    runs-on: ubuntu-latest
    environment: dev
    steps:
      - name: check env & secret
        run: |
          echo ${{ vars.level }}
          echo ${{ secrets.key }}
```
<div align="center">
<img src="https://github.com/user-attachments/assets/81a0f928-4dc1-4c88-ac33-1d707d358c5f">
</div>
