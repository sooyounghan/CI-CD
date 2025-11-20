-----
### tag filter
-----
1. tag filter : 특정 tag 패턴으로 tag가 push될 때, 워크플로우를 실행하고 싶을 때 유용하게 사용 가능 (예) v1.0.0으로 태깅해야 실행)
   - 💡 push event에서만 사용 가능 (branch filter, path filter는 push 외에 pull request event에서도 사용 가능)
   - tag를 사용하는 방법 : git tag ```<tag-name>```

2. 실습
```yaml
name: tag-filter
on:
  push:
    tags: # 특정 태그 패턴이 맞는 경우에만 워크플로우가 실행되도록 일반적으로 버전 정보를 나타내는 태그 패턴 사용
      - "v[0-9]+.[0-9]+.[0-9]+" # v1.0.0 or v2.2.2 / v1.0 은 제외

jobs:
  tag-filter:
    runs-on: ubuntu-latest
    steps:
      - name: echo
        run: echo hello
```

   - Git Tag 생성 : git tag v0.0.0 (태그 패턴과 일치)
   - GitHub Push : git push origin v0.0.0
<div align="center">
<img src="https://github.com/user-attachments/assets/64456d7b-5336-4bb6-b8d9-358852bc7298">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/77c7d2ee-dbf0-489d-901e-69f0fa73d0aa">
</div>

   - Git Tag 생성 : git tag 0.0.0 (태그 패턴과 불일치)
   - GitHub Push : git push origin 0.0.0
<div align="center">
<img src="https://github.com/user-attachments/assets/ca0f4cf9-dc28-41f1-a312-41089936e428">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/77c7d2ee-dbf0-489d-901e-69f0fa73d0aa">
</div>
