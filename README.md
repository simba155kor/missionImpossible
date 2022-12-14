# missionGame


------------------------------
카카오 Oauth 2.0 로그인 구현
 - 프론트에서 카카오 로그인 페이지로 바로 요청.
 - 로그인 성공시 인가코드 받음. 
 - 리다이렉트된 프론트 페이지 created 될때 바로 스프링 서버에 인가코드를 스프링에게 줌.
 - 스프링은 받은 인가코드로 access_token 요청함.
 - access_token 받고 그걸로 유저 정보 요청함.
 - 요청한 유저 정보로 DB에 있는 회원인지 확인함.
    - 있다면 DB에 저장되있는 jwtFakeToken 가져옴.
    - 없다면 DB에 새로 insert함. ( 닉네임, 프로필이미지, access_token, refresh_token, jwtFakeToken, kakaoId ), 이때 jwtFakeToken 생성함.
 - jwtFakeToken 프론트에게 줌.
 - 응답 받은 프론트는 jwtFakeToken 로컬 스토리지에 저장하고, vuex 로그인 상태로 만들고, 로그인 성공 메세지 띄우고 홈으로 감.

 - 로그아웃 시에는 로컬 스토리지에 키 jwtFake 삭제함. vuex 로그아웃 상태로 만듬. 
 
 - 로그인되면 메인 화면에 프로필 이미지와 닉네임이 나옴. 메인 화면 갈때마다 로컬 스토리지 jwtFake 키 값으로 스프링에 요청 보냄.
 - 요청 받으면 jwtFake로 DB에 저장되있는 accessToken으로 바꾸고 이 accessToken 이용해서 스프링에서 카카오에 유저 정보 요청하고 응답받아서 다시 프론트로 주는 것. 
 
 개선점
   - 프론트에서 인가코드 요청하는 URL로 바로 요청함. (백엔드로 로그인 요청 후 다시 프론트로 인가코드 요청하는 주소로 리다이렉트 하고 싶음.)
     -> 이거 때문에 프론트엔드 코드에 앱 키랑 리다이렉트 주소가 드러남..
   - 로그인 성공하면 홈으로 갈때 스프링에서 리다이렉트하라고 요청해서 프론트에서 바뀌도록 하고 싶음.
      -> 둘다 스프링에서 리다이렉트 하려면 CORS 문제 생겨서 그럼. 이거 해결해야 가능.
      -> Spring Security 문제인지, CORS 설정 문제인지..
      
   - jwtFake 토큰 jwt 토큰으로 바꿀 것

   - (완)access token이 만료되면 refresh token으로 다시 얻어야 하는데 이러한 로직 없음.
   - (완)https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api#refresh-token 

   - 백엔드 서버가 내려가도 로컬 스토리지에 jwtFake 토큰이 남아있음. 카카오 로그아웃 해도 jwtFake 토큰 남아있음.
       -> 유효기간을 설정해야하나? 카카오 로그아웃시에는 어떻게 알고 지우지??
--------------------------------

미션 현황판 테이블 만들기.

  - (완)각 회원에게 할당된 미션 할당하기. 미션 번호 하기. 회원 테이블에 미션번호 컬럼 추가해야함.
     -> 회원 테이블에 추가하지 않고, 회원번호, 미션번호를 외래키로 가지는 다대다 테이블 만듬.
  - (완) 나중에 조회할떄는 join 해서 미션 내용 뭔지 조회해야함.
     -> 다대다 테이블 이용해서 미션 내용 조회함.
  
  - (완)서로 예측할 수 있도록 input 만들어야함.
  - (완)미션 받은 사람은 목록에 나와야함.
     -> 로그인 되면 나오게 함.
  - (완)목록에 회원 닉네임, 이미지 뜨게 하면 될듯.
  - (완)그 사람옆에 버튼 눌러서 모달 창으로 뭐일지 예측하는 거 입력 적을 수 있게하기.
  - (완)그 예측은 DB에 어떻게 저장할지 고민해볼것.
     -> 각자 그 사람의 미션 예측하기. 여러번 가능. 한개만 저장됨.
  - 현황판 로그아웃되면 없어지거나 뭐, db에서 지우는거 고려할것.

----------------------------------------------

  - (완)미션은 한번 뽑으면 다시 못 뽑게 함.
  - (완)미션 랜덤으로 뽑게 하기.(중복되지 않게 할까?)
    -> 중복될 수 있음
  - (완)미션 여러개 넣기
  - (완)특정 시간 되면 각자의 예측 남들에게 다 보이게 하기.
  - 특정 시간 되면 이번 미션 다 초기화. -> jwtFakeToken 없애서 하면 좋을듯 함..
---------------------------------------------

배포.

  - GCP 이용. MobaXterm SSH 연결.
  - MYSQL 설치
  - Nginx 설치
    -> https://krksap.tistory.com/1931 해줘야 oauth 잘됨.
  - API서버는 jar 파일로 빌드. 실행
  - 도메인 등록. 
  - 배포용으로 localhost 되있던 소스 코드 다 변경
  - GCP 방화벽 열기
  - Mysql bindaddress 0.0.0.0 으로 하기
  - Mysql 계정 권한 


  -> https 로 할것.

----------------------------------------------

  - 도메인 주소 같은거 env 파일로 해결할것. 하고 깃에 푸시할것.



? post는 params로 넘겨주고 RequestParam 으로 받으면 400에러남..? 인섬니아에서는 에러 안남.


JPA 공부할것
  -  predict 테이블 내번호와 쟤번호 두개 해서 복합키 사용함. -> JPA save() 해도 새로 생성 안됨.
    --> 브라우저에서 요청하니 중복된 키라고 안됨.. 
    --> JPA에서 update는 save 안쓰고 그냥 findby 한 후에 변경하고 놔두는 방식으로 하라고 함..? -> 이러면 setter를 쓰게 되는데?
    --> save 안하니까 db에 반영  안되는데??
    --> 복합키라 좀 다른듯!@
    --> 키 값을 변경하려하면 에러 나옴.
    --> 영속상태란...?
    --> 그냥 복합키 안쓰기로함.. 복합키 써서 해보기도 하자..

Optional 처리 잘 할것.

oauth2 http인데도 되네?




