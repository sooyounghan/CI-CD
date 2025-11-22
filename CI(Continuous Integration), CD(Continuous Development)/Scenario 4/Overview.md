-----
### 시나리오 4
-----
1. 시나리오 1 : 개발 환경을 위한 CI / CD
2. 시나리오 2 : 개발 & 운영 환경을 위한 CI / CD
3. 시나리오 3 : 개발 & QA & 운영 환경을 위한 CI / CD
4. 시나리오 4 가정
   - 개발 환경과 QA, Staging, 운영 환경을 위한 CI / CD 구성
   - Staging 환경 배포 후, 승인 받으면 운영 환경 배포
   - branch
     + dev : 개발 환경
     + master : 운영 환경

5. Staging
   - 운영 환경과 매우 유사한 환경
   - 운영 환경 배포 전 마지막 테스트 수행

6. QA 환경과 Staging 환경의 차이
   - QA 환경 : 개발된 기능 검증 및 버그를 찾는 것을 목적
   - Staging 환경 : 전체 시스템의 통합성 검증을 통해 운영 환경의 안정성을 높일 수 있음
   - Staging 환경과 운영 환경은 인프라 구성이 거의 동일하며, 배포할 애플리케이션도 동일 (운영 환경에 반영하기 전, 먼저 스테이징 환경에 애플리케이션을 배포한 다음, 마지막 테스트를 수행하고, 이 과정에서 검증이 완료된 다음 운영 환경으로 배포)
   - Staging 환경을 사용하므로 운영 환경의 안정성을 높일 수 있음

7. Staging
   - master Branch 사용
   - 배포 시
     + image-build (staging)
     + deploy (staging)
   - Staging 단계에서 만들어진 컨테이너 이미지는 운영 환경(prod)에서도 사용 (같은 이미지를 사용해야 배포할 애플리케이션이 동일해지기 떄문임)

8. 배포 순서
   - 개발 환경 (dev Branch)
   - QA 환경 (tag)
   - Staging 환경 (master Branch)
   - 운영 환경 (master Branch)

9. 요구사항
   - 특정 path에 대해서만 실행 (my-app)
   - dev, master branch로 PR이 생성 & 동기화 될 때, 테스트 작업 실행 (CI)
   - PR이 dev branch로 Merge 되면, 이미지 빌드하고 개발 환경에 배포 (CD)
   - 필요한 시점에 QA 환경에 배포 (CD)
   - PR이 master branch로 Merge 되면, 이미지 빌드하고 Staging 환경에 배포 (CD)
   - Staging 환경에 배포 후, 승인 받으면 운영 환경에 배포 (CD)
   - 배포 성공 여부 슬랙으로 전송

10. 흐름도
<div align="center">
<img src="https://github.com/user-attachments/assets/912e0047-b745-4496-b0a3-f585c6b6e33d">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/556186b5-5010-4aa7-8727-b944cb18524f">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/7764ba91-2a08-43f3-8326-8feba6037f0b">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/8148badd-b1ae-4ac4-b078-e10a609a3623">
</div>
