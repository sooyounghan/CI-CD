-----
### 시나리오 1 소개
-----
1. 배포하는 Application : React 기반 my-app
2. 가정 : 개발 환경을 위한 CI / CD 구성
3. Branch : dev (개발 환경)
4. 요구사항
   - 특정 path에 대해서만 실행 (my-app)
   - dev Branch로 Pull Request이 생성 및 동기화 될 때, 테스트 작업 실행 (CI)
   - PR이 dev Branch로 Merge되면, 이미지를 빌드하고 개발 환경에 배포 (CD)
   - 배포 성공 여부를 Slack으로 전송

5. 흐름도
<div align="center">
<img src="https://github.com/user-attachments/assets/12dc15ed-76d8-46c2-9b17-d40ab9fd2588">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/724f8380-d6dd-4aed-9186-2185cb40524b">
</div>
