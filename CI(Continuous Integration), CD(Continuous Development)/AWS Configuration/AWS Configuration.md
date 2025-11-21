-----
### AWS 구성
-----
1. 각 시나리오마다 AWS 환경 구성은 동일
2. 시나리오에 따라 달라지는 점 : ECR 레포지토리 생성
3. 진행 과정
   - Github OIDC 설정 : Github Actions에서 AWS에 접근할 수 있도록 임시 자격을 얻을 수 있음
   - AWS IAM Role 생성 : 접근할 수 있는 자격을 AWS IAM Role만으로, Github Actions에서 AWS로 접근 가능 
     + github actions
     + Cloud Shell : 서비스에서 사용할 Role
   - AWS ECR 생성 : image-build job에서 생성한 이미지를 관리하기 위해 이미지 레포지토리 생성
     + Region 선택 : 어떤 Region에서 클라우드 서비스를 사용할 지 선택
     + AWS Cloud Shell 구성 : 환경 구성하기 위함으로, 웹 브라우저를 통해 접근 가능한 클라우드 기반 통합 개발 환경 (특정 장비나 OS에 구애받지 않고 작업 가능)
     + AWS EKS를 구성하기 위한 스크립트 실행 : AWS EKS 구성 완료
   - Github Repository에 환경 변수 및 Secret 구성

4. Github OIDC
```
https://docs.github.com/en/actions/how-tos/secure-your-work/security-harden-deployments/oidc-in-aws
```
<div align="center">
<img src="https://github.com/user-attachments/assets/4a2fe42f-822e-4f90-b313-14196a164cc5">
</div>

5. OIDC 설정 (AWS IAM 이용)
   - AWS IAM 진입 후, ID 제공 업체(자격 증명 공급자) - 공급자 추가 - OpenID Connect 선택 후, 공급자 URL에 ```https://token.actions.githubusercontent.com``` 입력
   - 대상 : ```sts.amazonaws.com``` 입력 후, 공급자 추가

6. Github Actions에서 사용할 AWS IAM Role 설정
   - AWS IAM 진입 후 역할 - 역할 생성 - 사용자 신뢰 정책 - 아래 JSON 입력 (내용 일부 수정) - 특정 Github Repository에서만 IAM role을 사용해 AWS에 접근할 수 있음
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Federated": "arn:aws:iam::123456123456(계정 아이디로 변경 부분):oidc-provider/token.actions.githubusercontent.com"
            },
            "Action": "sts:AssumeRoleWithWebIdentity",
            "Condition": {
                "StringLike": {
                    "token.actions.githubusercontent.com:sub": "repo:octo-org/octo-repo:*" # Organization, Repository 지정
                },
                "StringEquals": {
                    "token.actions.githubusercontent.com:aud": "sts.amazonaws.com"
                }
            }
        }
    ]
}
```
   - 정책 추가 : AdministratorAccess 권한 추가
   - 역할 이름 : github-actions로 작성 후, 역할 생성

7. Cloud9에서 사용할 AWS IAM Role 설정
   - 역할 생성 - AWS 서비스 : 서비스 또는 사용 사례 EC2 선택 - AdministratorAccess 권한 부여
   - 역할 이름은 admin-cloud9로 작성 후 역할 생성

8. IAM은 Region에 종속되지 않음 (하지만, Cloud와 ECR을 위한 작업은 특정 Region에서 진행해야 함)
9. 생성한 이미지를 관리하기 위해 AWS ECR 서비스를 통해 이미지 레포지토리 생성
    - EKS 검색 후 진입 - 관련 서비스 - Amazon ECR 선택 - 왼쪽 메뉴의 Private Registry - Repositories 선택
    - 이름은 my-app-dev로 지정 후 레포지토리 생성

10. Cloud9 서비스를 통해 작업 환경 구성
    - Cloud Shell 검색 (AWS Cloud9 서비스 종료)

-----
### CloudShell 사용 가이드
-----
<div>
<img width="974" height="495" alt="image" src="https://github.com/user-attachments/assets/5b46b222-d241-41a0-ad52-27924009cf94" />
<img width="974" height="488" alt="image" src="https://github.com/user-attachments/assets/b3a54d71-37b7-4f6c-93b7-ec9836a88380" />
<img width="984" height="490" alt="image" src="https://github.com/user-attachments/assets/8f938a19-01bf-456c-89da-0ad1fa28b595" />
<img width="978" height="489" alt="image" src="https://github.com/user-attachments/assets/e5e149cb-6a32-4491-a7f2-ef12f59b3b6f" />
<img width="978" height="503" alt="image" src="https://github.com/user-attachments/assets/e555c467-1fa0-4c0b-bf47-d04ccca1f759" />
<img width="824" height="523" alt="image" src="https://github.com/user-attachments/assets/86441d00-f667-4908-90ae-1cf1af4b312c" />
<img width="982" height="480" alt="image" src="https://github.com/user-attachments/assets/c6a33759-0fc2-4b68-8a4c-da5ca4e9abae" />
<img width="984" height="308" alt="image" src="https://github.com/user-attachments/assets/aecd32e2-54a4-48c5-a006-e1cc1a793ff7" />
<img width="975" height="505" alt="image" src="https://github.com/user-attachments/assets/46022fc1-caae-4fde-8bfe-65fc6ca0ae85" />
<img width="966" height="334" alt="image" src="https://github.com/user-attachments/assets/8f148083-5fa8-4b2e-bb14-a644719188a4" />
<img width="963" height="480" alt="image" src="https://github.com/user-attachments/assets/ffa7475d-c080-4490-a8b7-dbc1e48dfb6c" />
<img width="952" height="520" alt="image" src="https://github.com/user-attachments/assets/eec8f331-ff95-4028-b141-cf6fe272e969" />
<img width="883" height="517" alt="image" src="https://github.com/user-attachments/assets/b5cdbafc-cfcc-4a44-a8d5-230e8562fe88" />
<img width="852" height="540" alt="image" src="https://github.com/user-attachments/assets/8a43de94-2b61-4a23-9bfe-2577e1947b81" />
<img width="960" height="422" alt="image" src="https://github.com/user-attachments/assets/c50171ec-3285-403c-ab19-0ce917effa9e" />
<img width="899" height="520" alt="image" src="https://github.com/user-attachments/assets/46b06f91-51f0-4f12-910d-0a8d0423eba9" />
<img width="972" height="391" alt="image" src="https://github.com/user-attachments/assets/037a04de-3939-42a8-9890-b487c75a9f46" />
<img width="970" height="457" alt="image" src="https://github.com/user-attachments/assets/c32156e5-5f44-4318-a29e-81f397daac61" />
<img width="976" height="494" alt="image" src="https://github.com/user-attachments/assets/66fc799c-85d4-4863-be35-38c322ebf149" />
<img width="976" height="389" alt="image" src="https://github.com/user-attachments/assets/c807560e-0808-4cda-b2e3-ec70f512e006" />
<img width="959" height="315" alt="image" src="https://github.com/user-attachments/assets/2cb0c7f8-6295-4415-acb2-e69593768156" />
</div>
