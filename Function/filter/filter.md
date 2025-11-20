-----
### filter
-----
1. 이벤트가 특정 조건에 부합할 때만 워크플로우가 되도록 트리거되도록 실행 : 따라서, 워크플로우 실행을 더 효과적으로 제어 가능
2. branch filter : 특정 branch에서 실행 (예) dev, master branch 중에서 master branch로 push 해야만 실행)
3. 실습
```yaml
name: branch-filter
on:
  push:
    branches: ["dev"] # 특정 branch에서만 동작하도록 filter 설정 (branch filter에 원하는 branch를 넣으면 해당 branch에 push event가 발생했을 때만 workflow 실행)

jobs:
  branch-filter:
    runs-on: ubuntu-latest
    steps:
      - name: echo
        run: echo hello
```
<div align="center">
<img src="https://github.com/user-attachments/assets/83081794-12f3-4c58-a9e4-eba71af72832">
</div>

   - branch filter를 dev-test로 변경하면, branch filtering을 dev-test로 설정하였으므로, dev branch에서는 실행되지 않
```yaml
name: branch-filter
on:
  push:
    branches: ["dev-test"] # 특정 branch에서만 동작하도록 filter 설정 (branch filter에 원하는 branch를 넣으면 해당 branch에 push event가 발생했을 때만 workflow 실행)

jobs:
  branch-filter:
    runs-on: ubuntu-latest
    steps:
      - name: echo
        run: echo hello
```

4. 즉, brnach filter를 통해 원하는 branch event가 발생할 때만 Github Actions을 실행하도록 할 수 있음
