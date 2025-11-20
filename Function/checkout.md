-----
### checkout
-----
1. Github Repository를 가져와 작업 수행
2. Github Marketplace에 정의된 공식 액션
3. checkout을 이용해 Github Repository에서 파일을 가져오면, 필요한 테스트 및 빌드 작업 수행 가능
4. Repository 내용을 CI / CD 파이프라인 Workflow에서 사용
5. 실습
```yaml
name: checkout
on: workflow_dispatch

jobs:
  no-checkout: # checkout 미사용
    runs-on: ubuntu-latest
    steps:
      - name: check file list
        run: cat README.md

  checkout: # checkout 사용
    runs-on: ubuntu-latest
    steps:
      - name: use checkout action
        uses: actions/checkout@v4
      - name: check file list
        run: cat README.md
```

   - no-checkout Log : checkout action을 사용하지 않아 Github Repository를 가져오지 않았기 때문임
<div align="center">
<img src="https://github.com/user-attachments/assets/9a820011-6958-4f99-b9f5-6b91bbd879c6">
</div>

   - checkout Log
<div align="center">
<img src="https://github.com/user-attachments/assets/4911871d-dc30-414e-af91-4b90c3e0c674">
</div>
