# OAuth-Project

_Fastcampus Node OAuth-Project 강의 내용을 정리해둔 자료입니다._

`.env`

```
FB_APP_ID=
FB_CLIENT_SECRET=
NAVER_CLIENT_ID=
NAVER_CLIENT_SECRET=
KAKAO_JAVASCRIPT_KEY=
KAKAO_REST_KEY=
MONGO_PASSWORD=
MONGO_CLUSTER=
MONGO_USER=
MONGO_DBNAME=
SERVER_SECRET=
```

## Flow별 처리 과정

### OAuth flow

- Vendors
  - Naver
  - Facebook
  - Kakao
- 각각의 OAuth provider에 개발자 계정을 만들고 설정을 해야 함
- access token -> (platformUserId, platform) -> 회원가입/로그인 처리
- HTTPS -> ngrok으로 일단 처리
- 로그인 버튼 디자인의 경우 각 Vendors의 활용가이드를 따라야함

### 이메일 가입

- 인증 상태를 확인할 필요가 있음 -> 유저 정보에 `verified`라는 필드가 있어야 함.

  - `verified` 필드가 `false`이면 정상적인 활동 불가능.
  - 이메일 인증은 특수한 코드와 함껭 ㅣ메일을 보내서 그 링크로 접속했을 때만 인증이 되게 처리.
    - "다음 링크로 들어와 인증을 마무리해주세요. https://somewhere.com/verify_email?code=abcde-defsd-123-asdasd"
    - 위 링크로 `GET`해서 들어오게 되면 `verified`를 `true`로 바꿔줌
  - AWS(Amazon Web Services) -> SES(Simple Email Service)로 인증 메일을 보낼 것.

- 비밀번호 초기화
  - 비밀번호 찾기는 지원하지 않는다. 찾기가 가능하다는 것은 양방향 변환이 가능, 또는 plain text로 저장되어 있다는 소리. 해시 펑션(one-way function)을 사용해 해시된 값을 데이터베이스에 저장
  - 유저가 처음 가입한 해당 이메일로 인증 메일과 비슷하게 초기화 링크를 담은 메일을 보낸다.
  - 해당 링크를 타고 들어오면 그 때 비밀번호를 갱신시킨다.

### 배포

AWS를 사용해서 배포한다.

- EC2(server)
  - git 레포지토리를 clone해서 배포
- HTTPS 지원 - Amazon 인증서.
- ELB(Elastic Load Balancer)를 사용해 여기에 인증서를 물리고, ELB가 뒤의 EC2를 바라보게 함
- SES를 통해 메일 처리
- 데이터베이스는 MongoDB를 사용.
