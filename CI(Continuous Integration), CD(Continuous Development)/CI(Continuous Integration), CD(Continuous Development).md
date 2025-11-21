-----
### CI(Continuous Integration), CD(Continuous Development or Delivery)
-----
1. 소프트웨어 개발 및 배포 과정을 자동화하고, 이러한 과정을 효율화하는 데 중요한 역할
2. CI(Continuous Integration)
   - 공유 저장소(여기서는 GitHub) 내의 코드 변경사항에 대해 자동으로 테스트 실행
   - 문제를 조기에 발견하고, 소프트웨어를 더 안정적 개발하도록 도와줌

3. CD(Continuous Delivery)
   - 코드 변경사항을 배포할 환경에 배포 준비 상태로 만드는 것 
   - 즉, 변경 사항이 배포할 준비가 되어있더라도, 실제 배포는 수동으로 진행으로 이루어짐

4. CD(Continuous Development)
   - 변경 사항이 자동으로, 실제 배포 환경에 자동으로 반영
   - 즉, 둘의 차이는 수동 / 자동의 차이

5. 소셜 미디어 애플리케이션 개발
   - 개발자 A : 메세지 기능
   - 개발자 B : 이미지 업로드 기능
   - CI / CD가 없을 때의 상황 : 개발자 A와 개발자 B는 다른 기능을 만들고 이러한 기능을 GitHub에 반영
<div align="center">
<img src="https://github.com/user-attachments/assets/e7b7d95f-232e-406d-9714-b59d00af605a">
</div>

   - 반영 완료 후, 기능 문제가 발생
<div align="center">
<img src="https://github.com/user-attachments/assets/21e8aedd-e8cb-4cda-aaab-133928ce61ad">
</div>

   - CI / CD 도입
     + 개발자 A의 메세지 기능 코드가 GitHub에 반영되면, CI 프로세스 과정을 거치게 됨
     + 만약, 메세지 기능에 문제가 없다면 CD 프로세스로 진입 가능
     + 개발자 B의 코드 또한 동일하지만, 문제가 있으면 CD 프로세스로 진입 불가
     + 즉, B가 작업 중인 기능에 문제가 발생하더라도, A의 작업은 안전하게 사용자에게 제공할 수 있게 됨
<div align="center">
<img src="https://github.com/user-attachments/assets/9470c0c0-b8c6-4765-be41-5eaa0b35bf76">
</div>

4. CI / CD가 존재할 경우
   - CI 프로세스
     + 각 개발자의 작업이 독립적으로 검증됨
     + 코드 변경사항을 자동으로 테스트

   - CD 프로세스
     + CI 프로세스에 이상이 없다면 배포
     + 사용자에게 안정적인 서비스를 지속적으로 제공
    
   - CI / CD를 통해 검증된 소프트웨어(CI)를 배포 환경에 안정적, 효과적으로 전달(CD)

-----
### GitHub에서의 CI / CD 사용
-----
1. Github Actions을 활용해 CI / CD 프로세스가 언제 실행되도록 할 지 결정
2. 구성
   - CI (테스트) : PR Open, PR Synchronize (동기화)
     + PR Open
       * 코드 변경사항이 처음 메인 코드 베이스로 병합되도록 제안하는 순간
       * 기능 추가나 버그 수정과 같은 변경사항을 만들고, 이를 메인 코드베이스에 통합하고자 할 때 생성
       * 이 시점에 CI 프로세스를 시작하는 이유 : 변경사항이 기존 코드와 문제없이 잘 통합되는지 확인 및 새로운 버그가 발생하지 않는지 확인하기 위함
     + PR Synchronize
       * 이미 열려있는 PR에 추가 Commit이 발생할 때 발생
       * 추가적인 변경사항이 기존 PR에 추가되면, 이 변경사항도 기존 코드와 잘 통합되는지, 새로운 문제를 발생시키지 않는지 검증
       * 이 시점에 CI 프로세스를 시작하는 이유 : 최초 PR(PR Open 시점)이 안정적이라도 추가된 변경사항에 문제가 발생하는지 확인하기 위함

     + CI 프로세스가 없다면, 버그나 오류가 그대로 반영될 위험이 있음

   - CD (배포) : PR Merge
     + 코드 변경사항이 메인 코드 베이스로 통합되는 순간
     + 이 시점에 CD 프로세스를 시작하는 이유 : 안정적이고, 검증된 코드를 실제 배포 환경에 신속하게 반영하고, 새로운 기능이나 수정사항을 제공하기 위함
     + CD 프로세스가 없다면, 검증된 코드가 사용자에게 도달하는데 시간이 지체될 수 있음

3. Pull Request을 소셜 미디어 애플리케이션에 적용
   - 각 개발자가 만들려고 하는 기능을 별도 Branch에서 작업하고, 이 작업 내용을 반영하기 위해 PR 생성
   - 발생한 이벤트로 CI 프로세스 실행 : 메세지 기능은 CI 프로세스에서 테스트를 통해 안정성을 검증하고 테스트가 성공하지만, 이미지 업로드 기능은 실패
<div align="center">
<img src="https://github.com/user-attachments/assets/6e924da9-d9d6-4e73-b467-90d692561af8">
</div>

   - 메세지 기능은 CD 프로세스를 진입해 CD 프로세스 처리 가능
<div align="center">
<img src="https://github.com/user-attachments/assets/33e4ef13-e50d-4ea1-973c-4d8f6abffb5e">
</div>

4. CI 프로세스에서 실행되는 테스트 : 애플리케이션 종류와 요구사항에 따라 다를 수 있음 
   - Unit Test, Integration Test, Regression Test (소프트웨어 변경 사항이 기존 기능에 부정적인 영향을 주지 않는지 확인하기 위해 이전에 실행했던 테스트를 다시 실행하는 소프트웨어 테스트 기법)
   - 필요 시, Code Convention, 보안 취약점 검사 등
  
5. CD (배포) : CI 프로세스에서 검증된 안정적 코드를 제공하는 단계
   
