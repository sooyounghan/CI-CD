-----
### GitHub Marketplace
-----
1. action 저장소
2. Custom action을 Publish해서 업로드
3. 이미 있는 액션을 사용 : 필요한 기능이 있다면 사용하는 게 편리
<div align="center">
<img src="https://github.com/user-attachments/assets/771777e5-6333-42b3-9566-e59bb5cfe66c">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/b9225c2c-d270-48cd-9e3e-84905a28ec13">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/74dbea7b-f8bf-4d52-aeef-c0f8e1fa6acc">
</div>

   - 예) checkout 검색
<div align="center">
<img src="https://github.com/user-attachments/assets/6d3dc105-5852-4594-8a0b-bf735fb5fe2f">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/0d12eec0-8f85-4778-964f-e4219c3902ed">
</div>

   - 제공되는 예제 코드도 확인 가능
<div align="center">
<img src="https://github.com/user-attachments/assets/f41b916e-c153-4120-ac26-49a1c00bef73">
</div>

   - step에서 checkout action을 사용할 때, uses 키워드로 action을 load
     + checkout과 같은 일부 action은 input 값을 전달하기 위해 with 키워드 사용
     + 기본으로 checkout 액션은 현재 Github Actions Workflow 파일이 있는 Github 레포지토리의 코드를 Github Actions Runner로 가져오게 됨
     + 하지만, 다른 레포지토리 코드나 특정 브랜치 혹은 태그에 대한 코드를 Github Actions에서 사용하려고 한다면, 레포지토리나 ref 값을 적절히 설정 (Github Action Markplace의 Checkout 문서에서 확인 가능)
<div align="center">
<img src="https://github.com/user-attachments/assets/f912c025-3f50-43ce-bbcb-f0540bab3ef4">
</div>
