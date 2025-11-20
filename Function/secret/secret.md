-----
### secret
-----
1. 민감한 데이터를 안전하게 저장해서 워크플로우에서 사용
2. 민감 정보를 코드와 분리하여 노출되는 것을 방지 (예) API Key, 암호, 인증 토큰)
3. 주요 특징
   - 안전한 저장 : Github에서 안전하게 암호화되어 저장
   - 로깅 방지 : 로그에 기록되지 않고, 출력 시 자동으로 마스킹
   - 접근 제한 : 워크플로우 실행 중에만 접근 가능
4. 정의된 시크릿을 바꾸면 워크플로우 결과가 달라질 수 있음
5. 사용 방법 : ```${{ secrets.<secret-name> }} = ${{ secrets.LEVEL }}```
6. 실습
   - Settings - Secrets and Variables - Actions - Secrets - New Repository Secret으로 secret 정보 생성
```yaml
name: secrets
on: push

jobs:
  get-secrets:
    runs-on: ubuntu-latest
    steps:
      - name: get secrets
        run: echo ${{ secrets.level }}
```

<div align="center">
<img src="https://github.com/user-attachments/assets/898c9f05-f0b6-4ad3-8f92-e110a53248fb">
</div>
