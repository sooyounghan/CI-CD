-----
### startsWith, endsWith, contains
-----
1. 문자열 처리 함수로, 문자열에 대한 조건 검사 수행 : job 또는 step의 실행 여부 결정
   - startsWith(searchString, searchValue) = startsWith('github actions', 'git') : return true / startsWith('github actions', 'test') : return false
   - endsWith(searchString, searchValue) = endsWith('github actions', 'ions') : return true / endsWith('github actions', 'test') : return false
   - contains(search, item) = contains('github actions', 'act') : return true / contains('github, actions', 'git') : return true
      + 💡 contains 함수에서 배열을 사용할 때는 실제로 배열로 작성하지 않고 콤마로 구분된 문자열 리스트로 작성해야 함

2. 실습
```yaml
name: string-function
on: push

jobs:
  string-function:
    runs-on: ubuntu-latest
    steps:
    - name: startsWith
      if: startsWith('github actions', 'git')
      run: echo "git"
    - name: startsWith # 실행 X
      if: startsWith('github actions', 'test')
      run: echo "test"
  
    - name: endsWith
      if: endsWith('github actions', 'ions')
      run: echo "ions"
    - name: endsWith # 실행 X
      if: endsWith('github actions', 'test')
      run: echo "test"
    
    - name: contains
      if: contains('github actions', 'act')
      run: echo "contains act"
    - name: contains
      if: contains('github, actions', 'git')
      run: echo "contains git"
```
<div align="center">
<img src="https://github.com/user-attachments/assets/9c9c9654-e6e1-4611-8d38-3dece7c6575b">
</div>
