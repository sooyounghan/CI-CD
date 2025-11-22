-----
### 시나리오 3
-----
1. 시나리오 1 : 개발 환경을 위한 CI / CD
2. 시나리오 2 : 개발 & 운영 환경을 위한 CI / CD
3. 시나리오 3 가정 : 개발 환경과 QA, 운영 환경을 위한 CI / CD 구성
   - QA (Quality Assurance) : 코드의 품질과 기능이 올바르게 동작하는지 검증
     + 테스트를 통해 소프트웨어 결함 식별
     + 식별된 결함 수정을 통해 품질 향상
   - branch
     + dev : 개발 환경
     + master : 운영 환경

4. 배포 순서
   - 개발 환경 (dev Branch)
   - QA 환경
   - 운영 환경 (master Branch)

5. 요구사항
   - 특정 path에 대해서만 실행 (my-app)
   - dev, master Branch로 PR이 생성 & 동기화 될 떄, 테스트 작업 실행 (CI)
   - PR이 dev Branch로 Merge되면, 이미지를 빌드하고 개발 환경에 배포 (CD)
   - PR이 master Branch로 Merge되면, 이미지를 빌드하고 운영 환경에 배포 (CD)
   - 배포 성공 여부를 Slack으로 전송
   - 필요한 시점에 QA 환경에 배포 (CD) : Tag event를 사용해 QA 환경에 배포

6. 흐름도
<div align="center">
<img src="https://github.com/user-attachments/assets/8eeed201-af79-4ca7-aae8-3f29320e75c1">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/0d91e227-8252-49b5-9dfa-e668b059a85d">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/6b6703d9-0131-4629-b254-aef3d4d92c1f">
</div>

   - QA Branch로도 가능 : 하지만 별도 Branch를 생성 및 관리해야 함
<div align="center">
<img src="https://github.com/user-attachments/assets/27755183-b546-4415-9636-ca2ace1b6291">
</div>

   - 필요한 시점에만 QA 환경에 배포하는 것으로, Tag를 사용하면 필요한 시점에만 Tag를 생성해서 QA 환경에 배포할 수 있게 되어 유연한 배포 관리가 가능
   - 또한, Tag는 원하는 특정 Commit에 Label을 붙이는 방식으로 동작해서 명확하고 구체적인 배포 포인트로 인식 가능
<div align="center">
<img src="https://github.com/user-attachments/assets/208b9237-cc23-4d1b-83a5-994e93ff50d5">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/aaada620-ce54-4092-ac4a-1d9ccdf76c0c">
</div

   - Branch를 사용하는 이유 : 새로운 Commit이 추가될 때마다, Commit History 업데이트
   - Tag를 사용하는 이유 : 태그는 특정 Commit에 Labeling하므로 그 값이 변하지 않음
     + Rollback 고려 : 특정 커밋(abcd)에 v1.0.0이라는 Tag를 붙였다면, v1.0.0 Tag는 특정 커밋(abcd)를 명확하게 참조
     + 예) 현재 버전 : v2.0.0일 때, 안정적인 v1.0.0 버전으로 Rollback 해야 한다면, v1.0.0 Tag가 붙은 Commit을 기준으로 Rollback 진행
     + 즉, 오류가 발생한다면 즉시 안정된 버전으로 복귀하는 큰 장점 제공
<div align="center">
<img src="https://github.com/user-attachments/assets/b34fe65c-ffed-42ba-8242-0c34cbf2964a">
</div>
