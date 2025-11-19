-----
### re-run
-----
1. 과거에 실행된 워크플로우를 재실행하는 것 (성공, 실패 여부와 상관없이 재실행 가능)
2. 💡 re-run으로 워크플로우를 실행하면, 트리거 된 그 시점을 다시 실행
   - 현재 이 레포지토리에 README.md라는 파일이 있고, 내용은 github-actions
<div align="center">
<img src="https://github.com/user-attachments/assets/f534868c-20d9-420f-874f-26b9f64c5ae5">
</div>

   - 구성한 워크플로우
     + push 이벤트가 발생하면 test job이 실행되는데, 이 job은 checkout이라는 actions을 사용하고, 그 다음 README.md 파일을 읽음
     + checkout : Github Repository 코드를 Github Actions job에서 받아올 수 있도록 하는 Action
<div align="center">
<img src="https://github.com/user-attachments/assets/d81cacc8-e7ee-4a46-b843-278a7b378e70">
</div>

   - 이 워크플로우가 트리거 되어, test job이 실행되면, checkout step을 실행해 Github Repository 코드를 받아오고, 그 후 cat README.md step이 실행되어서 README.md 파일을 읽어 값 출력
<div align="center">
<img src="https://github.com/user-attachments/assets/c69b015d-f9ae-46dd-8b1a-71bf4a4f3b99">
</div>

   - 이 워크플로우의 실행이 완료되면, 다시 실행할 수 있는 Re-run all jobs 버튼 활성화
<div align="center">
<img src="https://github.com/user-attachments/assets/07c07fa8-dbfe-4143-bee6-3f9da11c1197">
</div>

   - Re-Run jobs 버튼을 누르면 다시 test job 실행 : 다시 실행되어도 동일한 값 출력
<div align="center">
<img src="https://github.com/user-attachments/assets/8205aa31-b9cd-4889-b188-3bafc1696df7">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/41bba2f5-efd2-472d-b138-5eeb5689becd">
</div>

   - README.md 파일 내용을 변경하고, 다시 push하여 워크플로우를 트리거하면, 내용이 변경
<div align="center">
<img src="https://github.com/user-attachments/assets/03f30fc2-b0df-4814-9b42-b4508e7c490b">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/5f17649f-9d98-4c62-aa26-0aaa771355c9">
</div>

   - 즉, 각 워크플로우가 트리거된 시점이 차이가 나므로 README.md 파일의 내용도 다름 (커밋도 다름)
<div align="center">
<img src="https://github.com/user-attachments/assets/d11313a8-d12c-4b50-bf31-8be34ff4dbb1">
</div>


4. 오해
   - 과게이 이미 실행된 워크플로우가 존재
   - 파일을 수정
   - 💡 과거에 실행된 워크플로우를 re-run하면, 수정한 내용이 반영

5. 실제
   - 과게이 이미 실행된 워크플로우가 존재
   - 파일을 수정
   - 💡 과거에 실행된 워크플로우를 re-run해도, 수정된 내용은 반영되지 않음
   - 💡 수정한 파일이 포함된 후에 워크플로우가 트리거되어야 반영

6. 처음에 실행했던 Commit이 CF7이었던 워크플로우는 그 시점을 기준으로 re-run하므로, 그 시점의 결과값만 나옴 (60 Commit도 동일)
<div align="center">
<img src="https://github.com/user-attachments/assets/8c15b00d-64dc-4025-9d09-c4a53c3bd574">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/c1288d48-802a-4951-a5df-004043774191">
</div>
