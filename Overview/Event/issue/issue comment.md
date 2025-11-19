-----
### issue comment
-----
1. Comment 
   - issue comment : issue에 Comment를 작성하는 Event
   - pull request comment : pull reqeust에 Comment를 작성하는 Event로, 이 또한 issue Event

2. Default Branch에서만 동작하며, 모든 Activity Type에 대해 트리거가 발
<div align="center">
<img src="https://github.com/user-attachments/assets/7752121c-8d83-4de5-8e4d-4844f35c4171">
</div>

3. issue comment
   - pull request에 Comment를 작성할 때, 트리거되어 실행되는 job
<img width="2048" height="1066" alt="image" src="https://github.com/user-attachments/assets/2f73916a-2502-4111-9acd-b37e7c0ad79f" />

   - issue에 Comment를 작성할 때, 트리거 되어 실행되는 job
<img width="2048" height="1050" alt="image" src="https://github.com/user-attachments/assets/3fa81a37-6d8b-4771-8856-b8f8bec6af9f" />

   - pr-comment-test Branch를 통해 수정 후, PR 생성 후 Comment 변경 후, Github Actions 확인
     + pr-comment 트리거 발생 해 동작
     + issue-comment는 조건에 해당하지 않으므로 동작하지 않음
<div align="center">
<img src="https://github.com/user-attachments/assets/87ddfc15-c21d-471e-a8b6-3cdea5c15657">
</div>

   - issue 생성 후, issue comment 작성 후 확인
     + pr-comment는 조건에 해당하지 않아 동작하지 않음
     + issue-comment는 조건에 해당하므로 동작
<div align="center">
<img src="https://github.com/user-attachments/assets/665f3827-b36c-4068-8d70-1f1e70b3d6c2">
</div>

