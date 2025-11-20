-----
### artifact
-----
1. 워크플로우 실행 중 생성된 파일 또는 파일 모음
2. 동일한 워크플로우 내에서 job 사이에 데이터 공유
3. 워크플로우가 종료된 후에도, 일정 기간 데이터 유지 (Default : 90일)
4. 필요 시, 다운로드 가능
5. Github Marketplace에 정의된 공식 액션
   - upload-artifact
   - download-artifact
  
6. 실습
```yaml
name: artifact
on: push

jobs:
  upload-artifact:
    runs-on: ubuntu-latest
    steps:
      - name: echo
        run: echo hello-world > hello.txt
      - name: upload artifact
        uses: actions/upload-artifact@v4
        with:
          name: artifact-test # artifact 이름
          path: ./hello.txt # 해당 파일을 Root Path에 upload

  download-artifact:
    runs-on: ubuntu-latest
    needs: [upload-artifact] # 업로드 이후 가능하므로, 종속성 부여
    steps:
      - name: download artifact
        uses: actions/download-artifact@v4
        with:
          name: artifact-test
          path: ./
      - name: check
        run: cat hello.txt
```
<div align="center">
<img src="https://github.com/user-attachments/assets/7a6acfb8-ca65-4c1d-97fb-bbf0f7c05385">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/00f50812-6e6d-4061-8460-72a0c9f7f9e4">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/46e6060a-8cd0-48fe-94cf-71ebc048a910">
</div>
