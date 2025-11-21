-----
### 시나리오 2 소개
-----
1. 가정 : 개발 환경과 운영 환경을 위한 CI/CD 구성
2. branch
   - dev : 개발 환경
   - master : 운영 환경
3. 요구사항
   - 특정 path에 대해서만 실행 (my-app)
   - dev, master Branch로 PR이 생성 및 동기화 될 때 테스트 작업 실행 (CI)
   - PR이 dev Branch로 Merge 되면, 이미지를 빌드하고 개발 환경에 배포 (CD)
   - PR이 master Branch로 Merge 되면, 이미지 빌드하고 운영 환경에 배포 (CD)
   - 배포 성공 여부 슬랙으로 전송
4. 흐름도
<div align="center">
<img src="https://github.com/user-attachments/assets/049e2ecd-d45c-44c1-b255-dcf8c61f134b">
</div>

   - dev Branch가 Merge 되면 개발 환경에 배포되는 것까지는 시나리오 1과 동일
   - feature Branch에서 dev Branch로 PR을 생성하면, CI가 실행되고, 이 CI가 성공한 후 PR을 Merge하면 개발 환경에 배포
     + 운영 환경 배포를 위해서는 master Branch에서 PR을 통한 변경 사항을 적용되어야 함 : 시나리오 2는 master Branch를 운영 환경으로 사용하고, PR을 통해 Merge 되어야 운용 환경 배포를 진행하기 때문임
     + 이를 위해 개발 환경 배포에 성공하면 그 시점에 동일한 커뮤니티 스토리를 가지는 특별한 Branch인 release-run-id Branch를 생성
     + 새로 생성된 release-run-id Branch에서 master Branch로 PR을 생성
     + PR이 생성될 때, CI가 실행이 되고, 이 CI가 성공한 후 PR을 Merge 시키면 운영 환경 배포가 됨
     + release/run-id Branch : run-id는 Github Actions Workflow의 고유한 Unique Number로, release Branch를 새로 생성할 때마다 Branch의 이름 중복을 방지하기 위해 추가
<div align="center">
<img src="https://github.com/user-attachments/assets/3f2a1fd3-f411-481b-a4a8-2ed892c10136">
</div>

   - dev-master Branch 간 직접적인 PR 연결 : PR이 계속 동기화됨 (feature Branch에서 작업한 변경사항을 PR을 통해 dev Branch로 PR을 통해 반영하면, master-dev Branch 간에 생성된 PR에도 변경사항이 추가되고, Commit이 동기화)
<div align="center">
<img src="https://github.com/user-attachments/assets/549d4917-289b-4050-8363-26323153f08a">
</div>

   - feature-2의 작업은 개발 환경에만 배포하기를 원하고, 운영 환경에는 아직 배포하고 싶지 않다고 가정
     + feature2 Branch에서의 작업을 개발 환경에 반영하기 위해 dev Branch로 PR을 Merge : dev-master Branch 간 PR이 동기화 되어 있어 이 PR에 반영
     + 이 때, 이 PR을 Merge하면, 결과적으로 master Branch에 의도하지 앟은 feature2의 작업이 반영
     + 이를 해결하기 위해 release-run-id Branch를 도입하여 dev와 master Branch 사이 간접적 PR을 생성하게 되면 동기화 문제를 피할 수 있음
     + feature Branch에서 dev Branch로 생성된 PR이 Merge되면, release/run-id라는 특별한 Branch가 만들어져 그 Branch에서 master Branch로 PR이 생성되고, feature2 Branch에서 dev Branch로 생성된 PR이 merge되면 또 다른 release/run-id를 사용하는 특별 Branch가 만들어져 각 작업이 분리되어 서로 영향을 주지 않음
<div align="center">
<img src="https://github.com/user-attachments/assets/38b1915d-0a2d-4e52-9f47-a8a3f87f660f">
</div>

   - 일반적으로 개발 환경과 운영 환경 인프라는 아예 분리함 (개발 환경용 인프라와 운영 환경용 인프라가 필요하므로 AWS 비용이 더 발생하는 단점 존재)
     + dev-master Branch 간 직접적인 PR 연결 : 의도하지 않은 동기화 문제가 발생
     + release/run-id Brnach를 사용한 간접적인 PR 연결 : 의도하지 않은 동기화 문제가 발생하지 않음

5. 여기서는 하나의 동일한 클러스터를 사용하면서 Namespace만 분리
<div align="center">
<img src="https://github.com/user-attachments/assets/98e77a31-63ed-485a-8adc-9126fd45510f">
</div>

<div align="center">
<img src="https://github.com/user-attachments/assets/d38b751e-ad79-40c3-b522-27cd70db02d7">
</div>

   - 개발 환경에 배포할 때 my-app-dev라는 Namespace에 my-app 애플리케이션이 배포
   - 운영 환경에 배포할 때 my-app-prod라는 Namespace에 my-app 애플리케이션이 배포
   - 즉, 서로 다른 Namespace이므로 두 개의 애플리케이션은 서로 분리
