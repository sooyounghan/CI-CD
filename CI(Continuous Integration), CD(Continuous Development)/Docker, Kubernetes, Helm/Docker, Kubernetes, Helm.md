-----
### Docker, Kubernetes, Helm
-----
1. Docker
   - 컨테이너 기술을 사용해 애플리케이션과 그 환경을 이미지라는 형태로 패키징하는 플랫폼
   - 어떤 환경에서든 동일한 실행 보장

2. Kubernetes
   - 컨테이너화 된 애플리케이션 배포, 확장, 관리 플랫폼

3. Helm
   - Kubernetes 패키지 매니저
   - Kubernetes 애플리케이션 설정, 배포 관리 간편화

4. CD 프로세스
   - 이미지 생성 : Docker + AWS ECR
   - 배포 : Helm  + AWS EKS(Kubernetes) 
<div align="center">
<img src="https://github.com/user-attachments/assets/590d4409-a636-436b-8062-7036260777ac">
</div>

5. AWS
   - 아마존이 제공하는 클라우드 서비스
   - 손쉽게 인프라 구축 및 확장, 실행 가능
   - AWS ECR : Docker 이미지를 쉽게 저장 및 관리 가능하며, 관리형 컨테이너 레지스트리 서비스
   - AWS EKS : 관리형 Kubernetes 서비스로, Kubernetes의 고가용성 / 신뢰성 / 확장성 등을 클라우드 환경에서 제공
   - ECR을 사용해 CD 프로세스에서 생성된 도커 이미지를 관리 및 사용하고, 이미지를 바탕으로 EKS에 배포
