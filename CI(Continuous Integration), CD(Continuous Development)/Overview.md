-----
### 시나리오 기반의 CI / CD
-----
1. Kubernetes 환경 배포
2. 시나리오 4개 : 간단한 시나리오에서 시작해서 요구사항 추가
   - 각 시나리오의 Github Action Workflow 구성 : 단순한 구성 ~ 복잡한 구성
<div align="center">
<img src="https://github.com/user-attachments/assets/fc20dac0-a9c1-4225-8b3d-362bad514cf9">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/8024ebc0-b70f-4b38-b3cc-dfbb4c32c837">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/5d81602e-2176-4d43-ada8-2a13bd1709d3">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/d860f995-f39e-4013-96b0-405ec8f87e74">
</div>

3. 구성 : CI(테스트) / CD(배포)
<div align="center">
<img src="https://github.com/user-attachments/assets/178620c7-0c66-492f-92cd-6eedc17df39d">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/18b6a222-5545-4336-88df-b9967982568c">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/71a0048f-54aa-4643-870f-d4e993b695dc">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/7853cc31-9027-4602-ab0f-531901837fcb">
</div>

4. 시나리오 구성
   - 시나리오 소개
   - Github Actions Workflow 구성 1 (설명)
   - Github Actions Workflow 구성 2 (코드 작성 + 프로세스 점검)
   - AWS EKS에 적용 (실제 배포)

5. 배포 환경 : AWS를 이용한 클라우드 환경
6. AWS EKS : 각 시나리오 실행
   + 비용적 측면 문제로 각 시나리오마다 호나경 구성 후, 실습 후 빠른 삭제 권장

7. Github Actions로 CI/CD 파이프라인 구성에 대한 이해와 실제 구축 능력을 기르는 것
