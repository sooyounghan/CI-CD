-----
### 시나리오 기반의 CI / CD
-----
1. Kubernetes 환경 배포
2. 시나리오 4개 : 간단한 시나리오에서 시작해서 요구사항 추가
   - 각 시나리오의 Github Action Workflow 구성 : 단순한 구성 ~ 복잡한 구성을 통한 CI / CD 파이프라인 구
   - 시나리오 1 : 개발 환경에만 적용되는 CI / CD 파이프라인 구축
<div align="center">
<img src="https://github.com/user-attachments/assets/fc20dac0-a9c1-4225-8b3d-362bad514cf9">
</div>

   - 시나리오 2 : 개발 환경과 운영 환경에 적용되는 CI / CD 파이프라인 구축
<div align="center">
<img src="https://github.com/user-attachments/assets/8024ebc0-b70f-4b38-b3cc-dfbb4c32c837">
</div>

   - 시나리오 3 : 개발 환경, 운영 환경, QA 환경에 적용되는 CI / CD 파이프라인 구축
<div align="center">
<img src="https://github.com/user-attachments/assets/5d81602e-2176-4d43-ada8-2a13bd1709d3">
</div>

   - 시나리오 4 : 개발 환경, 운영 환경, QA 환경, 스테이징 환경에 적용되는 CI / CD 파이프라인 구축
<div align="center">
<img src="https://github.com/user-attachments/assets/d860f995-f39e-4013-96b0-405ec8f87e74">
</div>

3. 구성 : CI 프로세스(테스트) / CD 프로세스(배포)
<div align="center">
<img src="https://github.com/user-attachments/assets/178620c7-0c66-492f-92cd-6eedc17df39d">
</div>

   - CD 프로세스 : 코드 변경이 pull request를 통해 Merge 되면, CD 프로세스가 실행되어 이미지 빌드 작업을 거쳐, 성공하면 개발 환경으로 배포
<div align="center">
<img src="https://github.com/user-attachments/assets/18b6a222-5545-4336-88df-b9967982568c">
</div>

   - QA 환경에 배포하는 CD 프로세스 : 특정 태그를 푸시하면 이미지 빌드 작업을 거쳐 QA 환경에 배포한 후, Pull Request를 생성하는 흐
<div align="center">
<img src="https://github.com/user-attachments/assets/71a0048f-54aa-4643-870f-d4e993b695dc">
</div>

   - 운영 환경에 배포하는 CD 프로세스 : Pull Request를 통해 Merge 되면, 이미지 빌드 작업이 수행되고 성공하면, 스테이징 환경에 배포한 후에 승인 절차를 거쳐서 최종적으로 운영 환경에 배포하는 흐름으로 진행
<div align="center">
<img src="https://github.com/user-attachments/assets/7853cc31-9027-4602-ab0f-531901837fcb">
</div>

4. 시나리오 구성
   - 시나리오 소개
   - Github Actions Workflow 구성 1 (설명)
   - Github Actions Workflow 구성 2 (코드 작성 + 프로세스(AWS에 배포하기 전 원하는 흐름으로 Github Actions Workflow가 동작하는 지 점검)
   - AWS EKS(Managed Kubernetes 환경)에 적용 (실제 배포)

5. 배포 환경 : AWS를 이용한 클라우드 환경
6. AWS EKS : 각 시나리오 실행
   + 비용적 측면 문제로 각 시나리오마다 환경 구성 후, 실습 후 빠른 삭제 권장

7. Github Actions로 CI/CD 파이프라인 구성에 대한 이해와 실제 구축 능력을 기르는 것
