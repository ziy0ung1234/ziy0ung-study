# HTTP 웹 기본 지식

## 인터넷 통신
- '인터넷'에서 통신하는 방법
  - 인터넷 망을 통해 통신하는데 인터넷 망은 굉장히 복잡한 구조
  - 부여받은 IP주소를 통해 통신이 가능

## IP(인터넷 프로토콜)
  - 역할
    - 지정한 IP address에 데이터 전달
    - 패킷(packet)이라는 통신 단위로 데이터 전달
  - 한계
    1) 비연결성
    - 일단 보내고 봄! 패킷을 받을 대상의 서버가 존재하지 않아도 일단 그냥 보내버림
    2) 비신뢰성
    - 패킷 보냈는데 중간에 패킷이 사라질 수도 있음
    - 패킷의 용량이 클때(1500byte 이상), 끊어서 보내는데 순서대로 안올 수 있음
    3) 프로그램 구분
    - 한 PC에서 게임도 하고 음악도 듣고 다양하게 사용하는데 같은 IP를 사용 -> 이걸 어떻게 구분지을지 불명확

IP 만으로는 언급된 문제들을 해결할 수 없어서 필요한 것이 **TCP,UDP**
<br>
## TCP(Transmission Control Protocol 전송제어 프로토콜)
  - 인터넷 프로토콜 스택의 4계층
    ![인터넷 프로토콜 스택의 4계층](https://www.notion.so/HTTP-b7789253fd204801aa0ed1fc35dd2505#8da1373c99c64f8e88b1b41c23b23dd7)
    - 애플리케이션(최상위) -> 전송 계층 -> 인터넷 계층 -> 네트워크 인터페이스(ex.LAN) 계층(최하위)
    - IP를 위로 살짝 올려 TCP와 함께 보완
  - 프로토콜 계층
    ![프로토콜 계층](https://www.notion.so/HTTP-b7789253fd204801aa0ed1fc35dd2505#878950f1b64d47fb95981a1d43cae203)
    - 특징
      - 연결지향적 : 연결이 되었는가를 먼저 확인 후 메세지를 보
	⭐️TCP 3way handshake⭐️
	  - 3번의 과정을 거쳐 신뢰성 있음 -> 그다음 데이터를 전송
	  - _💡물리적으로 진짜 연결된 것은 아님!! 아~연결이 됬나 보다만 확인💡_
	  ![3way handshake](https://www.notion.so/HTTP-b7789253fd204801aa0ed1fc35dd2505#4d3a1cc0cd4c45a9b9f8b993ef69e479)
      - 데이터 전달 보증 : 메세지가 누락되었을 때 확인이 가능
        - 데이터를 보내면 잘 받았을 때 '잘 받았다'는 메세지를 보내줌
      - 순서 보장
	- 패킷1,패킷2,패킷3 순서로 보냈는데 패킷1,3,2 순서로 도착할 경우 서버가 클라이언트에게 패킷2부터 다시보내라는 메세지 전송
	_💖이것이 가능한 이유💖_
	  - 패킷 안에는 TCP 세그먼트가 포함되어 있는데 그 내용 안에는 전송제어, 순서, 검증 정보등이 포함되어있기 때문에 가능하다
      - 신뢰 할 수 있는 프로토콜
      - 현재 대부분 TCP사용

## UDP(User Datagram Protocol 사용자 데이터그램 프로토콜)

UDP는 TCP와 같은 계층에 있는 프로토콜이지만 기능이 거의 없다.
- TCP 3 way handsake X
- 데이터 전달 보증 X
- 순서 보장 X
- 데이터 전달 및 순서 보장 X but 단순하고 빠름
-IP와 거의 같지만 `PORT`가 추가된 정도
- ***💡PORT*** - 하나의 IP로 여러가지 용도로 사용하고 있을 때 지금 사용하는 것이 어떤 애플리케이션의 패킷인지 구분할 때 쓰는것

더 최적화를 하고싶고 가능할 때 TCP는 손을 댈 수 없다. 그럴 경우에 UDP는 하얀 도화지 이기때문에 여기서 추가 작업을 진행한다. 최근 각광받고 있음

## Stateless(무상태 프로토콜)

- 서버가 클라이언트의 상태를 보존하지 않음

- stateful, stateless차이
  - stateful(상태 유지)한 상황에서는 서버가 바뀌면 원하는 데이터가 사라지기 때문에 장애가 남
  - 중간에 서버기 바뀔 때 상태 정보를 다른 서버에 미리 알려주어야 한다
  - stateless한 상황에서는 응답 서버를 쉽게 바꿀 수 있고, 갑자기 클라이언트 요청이 증가해도 서버를 대거 투입 가능
  - stateless한 상황에서는 응답 서버를 쉽게 바꿀 수 있고, 갑자기 클라이언트 요청이 증가해도 서버를 대거 투입 가능

- 상태 유지(브라우저 쿠키와 서버 세션등을 사용)는 최소한만 사용해야함

## Connectionless(비연결성)

- 연결을 유지하는 모델/ 연결을 유지하지 않는 모델
  - 연결을 계속해서 유지하면 클라이언트가 놀고 있어도 연결할 서버가 유지되어야하기 때문에 서버 자원이 계속해서 소모된다
  - 클라이언트 1과 TCP/IP로 연결 → 끊고 클라이언트 2와 연결 → 종료하고 클라이언트 3과 연결... ⇒ 자원을 연결 할때만 딱 주고받아서 최소한의 자원만이 유지
- 연결을 유지하지 않는 HTTP
  - 초 단위기 이하의 빠른 속도로 응답
  - 1시간 동안 수천명이 서비스를 사용해도 실제 서버에서는 동시 처리하는 요청은 수십개 이하로 매우 적음
  (웹 브라우저에서 계속 연결해서 검색 버튼을 누르지 않음)
  - 서버 자원을 매우 효율적으로 사용 가능
- 한계
  - 예를들어 중간에 새로 다음페이지로 넘어가면 TCP/IP 연결을 새로 맺어야함 ⇒ 즉, 3 way handshake 시간이 추가
  - 웹 브라우저로 사이트를 요청하면 HTML 뿐만 아니라 JS, CSS, 추가 이미지등 수많은 자원들이 함께 다운로드

    🧐  그래서 지금은 HTTP Persistent Connections(지속연결)로 문제를 해결 🧐
  ![http 초](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6ee8507a-284f-44cc-bd97-388d055a8b7d/_2021-05-31__2.56.40.png)
  ![지속연결](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/00ba173f-9879-419b-8871-6b8fa5c705b2/_2021-05-31__2.56.59.png)
  😎 요청하고 응답하고 요청하고 응답하고를 반복하고 모든 요청과 응답이 다 끝날 때까지 연결을 지속

## HTTP Message

- 맨처음 http 버전, 응답 코드 → 헤더값 → 무조건 공백라인 → 필요한 메세지 바디(보통 html)
- HTTP-message = start-line

                                    *( header-field CRLF )

                                    CRLF

                                    [  message-body  ]
- start-line
  - start-line = request-line / status-line
  - request-line = method SP(공백) request-target SP HTTP-version CRLF(엔터)

    method = HTTP method (GET, POST, PUT, DELETE...)

    - 서버가 수행해야 할 동작 지정

    request-target = path 요청 대상 (/search?q=hello&hl=ko)

    HTTP version = HTTP/1.1

    status-line = HTTP version SP(공백) status-code SP reason-phrase CRLF(엔터)

    HTTP version = HTTP/1.1

    status code(상태 코드) = 요청 성공, 실패 나타냄
- HTTP header
  - HTTP 전송에 필요한 모든 메타데이터 포함되어있다
  (메세지 바디의 내용, 바디의 크기, 압축, 인증, 요청 클라이언트(브라우저) 정보, 서버 어플리케이션 정보, 캐시 관리 정보..etc)
- HTTP body
  - 실제 전송할 데이터
  - HTML 문서, 이미지, 영상, JSON 등등 byte로 표현할 수 있는 모든 데이터 전송 가능

## HTTP Method

### API URI 작성시 고민해야 할 것!!
 ‼️리소스 식별‼️
- 리소스
    - 회원 등록, 수정, 조회 =\= 리소스

        *💡 **회원**이라는 개념 자체가 리소스 💡*

    - 회원 등록,수정, 조회 모두 배제 → ✌🏻회원✌🏻이라는 리소스만 식별

           = 회원 리소스를 URI에 매핑
- 주요 매서드
    - GET     (리소스 조회)
        - 리소스를 조회
        - 서버에 전달하고 싶은 데이터는 query(쿼리 파라미터, 쿼리 스트링)를 통해 전달
        - 메세지 바디를 사용해 데이터 전달 가능하지만, 지원하지 않는 곳이 많아서 권장X
    - POST   (요청 데이터 처리, 주로 등록)
        - 요청 데이터 처리
        - 메세지 바디를 통해 서버로 요청 데이터 전달

         예시) 안녕서버? 나 클라이언트인데 내가 !~~! 한 정보를 줄거야! 유저네임은 ##고 나이는%%이야 봤지? 서버 니가 받아서 요청 데이터를 처리해줘

        - 서버는 요청 데이터를 처리
            - 메세지 바디를 통해 들어온 데이터를 처리하는 모든 기능 수행
            - 주로 신규 리소스 등록, 변경된 프로세스 처리
        - ⁉️ 요청 데이터를 도데체 어떻게 처리??  ⁉️

            예시1. HTML 양식에 입력된 필드와 같은 데이터 블록을 데이터 처리 프로세스에 제공

            HTML, FORM에 입력한 정보로 회원 가입, 주문 등에서 사용

            예시2. 게시판, 뉴스 그룹, 메일링 리스트, 블로그 또는 유사한 기사 그룹에 메세지 게시

            게시판 글쓰기, 댓글 달기

            예시3. 서버가 아직 식별하지 않은 새 리소스 생성

            신규 주문 생성

            예시4. 기존 자원에 데이터 추가

            한 문서 끝에 내용 추가

            *😎 정리 - 리소스 URI에 POST요청이 오면 정해진 것이 없기 때문에 요청 데이터를 어떻게 처리할지 리소스 마다 스스로 정해야 함 😎*

            1. 새 리소스 생성(등록)
                - 서버가 아직 식별하지 않은 새 리소스 생성
            2. 요청 데이터 처리
                - 단순히 데이터를 생성하거나, 변경하는 것을 넘어서 프로세스를 처리해야 하는 경우

                    예) 주문에서 결제완료 → 배달시작 → 배달완료 처럼 단순히 값 변경을 넘어 프로세스의 상태가 변경되는 경우

                - POST의 결과로 새 리소스가 생성되지 않을 수도 있음

                    POST/orders/{order_id}/start-delivery(control URI)

            3. 다른 매서드로 처리하기 애매한 경우
                - 애매하면 POST

                    예) JSON으로 조회 데이터를 넘겨야 하는데 GET 매서드는 애매한 경우

    - PUT      ( 리소스 대체, 해당 리소스 없으면 생성)
        - 리소스를 대체

            → 리소스가 있으면 완전히 대체, 없으면 생성 ⇒ 즉, 덮어버리기!!

        - 클라이언트가 리소스를 식별 ⭐️

            ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bfae4748-f270-4aff-8026-ad28e2cffd9d/_2021-05-31__4.41.26.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bfae4748-f270-4aff-8026-ad28e2cffd9d/_2021-05-31__4.41.26.png)

            ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7780c5b2-5d51-4795-a74f-46e04330c2f1/_2021-05-31__4.41.37.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7780c5b2-5d51-4795-a74f-46e04330c2f1/_2021-05-31__4.41.37.png)

            → 클라이언트가 리소스 위치를 알고 URI 지정 (POST와의 큰 차이)

            → POST는 새로운 리소스를 클라이언트가 위치 지정하는 것이 아니라 서버가 URI를 생성해줌

            ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bcd3af87-c7ce-4633-9b68-26f57795dc49/_2021-05-31__4.47.26.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bcd3af87-c7ce-4633-9b68-26f57795dc49/_2021-05-31__4.47.26.png)

            ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4d391c17-fa86-4fae-93e3-77d700190c2a/_2021-05-31__4.47.38.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4d391c17-fa86-4fae-93e3-77d700190c2a/_2021-05-31__4.47.38.png)

            리소스를 완전히 덮어버리기 때문에 username이 없어도 덮어씌워져 필드 삭제되는 경우가 발생 !!!

            이럴 경우를 방지하기 위해 선택해 사용해야 하는것이 부분적으로 변경하는 PATCH

    - PATCH  (리소스 부분 변경)
        - PATCH로 "age":50을 보내면 age만 50으로 부분 변경된다
        - PATCH가 지원안될경우 POST 사용!!
    - DELETE ( 리소스 삭제)
        - 리소스 제거
        - DELETE/members/100 HTTP/1.1 → /members/100 DELETE
    - 기타 매서드
        - HEAD : GET 과 동일하지만 메세지 부분 제외, 상태 줄과 헤더만 반환
        - OPTIONS : 대상 리소스에 대한 통신 가능 옵션(메서드)을 설명(주로 CORS에서 사용)

    ## HTTP 메서드 속성

    - 안전(Safe)
        - 호출해도 리소스를 변경하지 않는다
    - 멱등(Idempotent)

        f(f(x)) = f(x)

         *💡 한번 호출하든 두번 호츨하든 100번 호출하든 결과는 같다*

        - 멱등 메서드
            - GET,PUT,DELETE → 같은 요청을 여러번 해도 최종 결과는 같다
            - POST → ‼️ 멱등 아님!!! 두번 호출하면 같은 결제가 중복해서 발생할 수 있다 ‼️
        - 언제 멱등이라는 개념이 활용될까???

            예) DELETE를 요청했을 때 제대로 삭제가 되었는지 서버가 응답이 없을때 똑같은 요청을 두번 세번 해도 되는지

            - 자동 복구 메커니즘
            - 서버가 TIMEOUT 등으로 정상 응답을 주지 못했을 때, 클라이언트가 같은 요청을 다시 해도 되는가? 를판단하는 근거

                ❔멱등하지 않다의 경우❔

                ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d8fec37b-2d07-491b-b991-9585e5a36bc0/_2021-05-31__5.33.44.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d8fec37b-2d07-491b-b991-9585e5a36bc0/_2021-05-31__5.33.44.png)

    - 캐시가능(Cacheable)
        - 응답 결과 리소스를 캐시해서 사용해도 되는가?

            예시) 용량이 큰 이미지 하나를 요청했을 때, 그다음에 또 요청이 필요할 시 큰 리소스를 또 요청하지 않고 캐시 해서 사용

        - GET, HEAD 정도만 캐시로 사용
            - POST, PATCH는 본문 내용까지 캐시 키로 고려해야 하는데, 구현이 쉽지 않음

## HTTP Status code
클라이언트가 보낸 요청의 처리 상태를 응답에서 알려주는 기능
- 1xx (Informational): 요청이 수신되어 처리중 
- 2xx (Successful): 요청 정상 처리
    - 200 Ok

        request 보냇을 때 결과를 정상적으로 처리해 response 보낼때

    - 201 Created

         요청 성공해서 새로운 리소스가 생성되었을 때

        ✅ HTTP header의 Location 헤더 필드에 넣어줌

    - 202 Accepted

        요청이 접수되었으나 처리가 완료되지 않음 (잘 사용 X)

        예) 요청 접수 후 1시간 뒤에 배치 프로세스가 요청을 처리할 때

    - 204 No Content

        서버가 request를 성공적으로 수행했지만, response 페이로드 본문에 보낼 데이터 없음

        예) 웹 문서 편집기에서 save 버튼

        - save 버튼의 결과로 아무 내용이 없어도 된다
        - save 버튼을 눌러도 같은 화면을 유지해야 한다
- 3xx (Redirection): 요청을 완료하려면 유저 에이전트(클라이언트)의 추가 조치 필요

    *💡 Redirect - 웹 브라우저는 3xx 응답의 결과에 Location 헤더가 있으면, Location 위치로 자동 이동 💡*

    예) URL이 /event인 이벤트 페이지를 운영했다가 /new-event로 변경된 웹페이지가 있다. GET/event HTTP/1.1로 request를 보냈을 때 서버는 301 status code 코드와 함께 Location 헤더에 변경된 주소인 /new-event를 담아 자동으로 리다이렉트를 해 new-event페이지에 다시 request를 보내 제대로 응답이 되면 status 200과 해당 document들을 보여준다. 💖 미친!! 💖

    - 종류
        - 영구 리다이렉션

            특정 리소스의 URI가 영구적으로 이동

            원래의 URL을 더이상 사용하지 않고 검색 엔진 등에서도 변경 인지

            - 301 Moved Permanently

                리다이렉트시 요청 메서드가 GET으로 변경, 본문 제거될 수 있음

            - 308 Permanent Redirect

                301과 기능은 같음

                리다이렉트시 요청 메서드와 본문 유지( 처음POST →리다이렉트 POST)

        - 일시 리다이렉션

            리소스의 URI가 일시적으로 변경 

            → 따라서 검색 엔진 등에서 URL을 변경하면 안됨!!!

            - 302 Found

                리다이렉트시 요청 메서드가 GET으로 변경, 본문 제거될 수 있음

            - 307 Temporary Redirect

                리다이렉트시 요청 메서드와 본문 유지(요청 메서드를 변경해선 절!!대!!안된다)

            - 303 See other

                리다이렉트시 요청 메서드가 GET으로 변경

            - ❔일시적인 리다이렉션을 써야하는 경우❔

                PRG: POST/Redirect/GET

                - PRG 사용전 : POST로 주문후 웹 브라우저를 새로고침 → 리프레시전 마지막 요청이 POST이기 때문에 새로고침하면 POST된  부분으로 다시 넘어가 중복 주문이 될 수 있다
                - PRG 사용: POST로 주문 후 주문 결과 화면을 GET메서드로 리다이렉트 → 리프레시해도 결과화면은 GET으로 조회해 중복 주문 방지
        - 특수 리다이렉션
            - 결과 대신 캐시를 사용

- 4xx (Client Error): 클라이언트 오류, 잘못된 문법등으로 서버가 요청을 수행할 수 없음
    - 400 Bad Request

        클라이언트가 잘못된 요청을 해서 서버가 요청을 처리할 수 없음

        - 요청 구문, 메세지 등등 오류
        - 클라이언트는 요청 내용을 다시 검토하고, 보내야함

        예) 요청 파라미터가 잘못되거나, API 스펙이 맞지 않을 때( 문자를 보내야 하는데 숫자를 보냄;;)

    - 401 Unauthorized

        클라이언트가 해당 리소스에 대한 인증이 필요

        - 인증(Authentication)되지 않음 (오류메세지는 Unauthorized지만 인증 되지 않음)
        - 401 오류 발생시 응답에 WWW-Authendicate 헤더와 함께 인증 방법을 설명

        *💡 인증(Authentication): 본인이 누구인지 확인 💡*

        *💡인가(Authorization): 권한 부여(admin 권한처럼 특정 리소스에 접근할 수 있는 권한, 인증이 있어야 인가가 있음)💡*

    - 403 Forbidden

        서버가 요청을 이해했지만 승인을 거부

        - 인증 자격 증명은 있지만, 접근 권한이 불충분한 경우

        예) 어드민 등급이 아닌 유저가 로그인 했지만, 어드민 등급의 다른 리소스에 접근하는 경우

    - 404 Not Found

        요청 리소스를 찾을 수 없음

        - 요청 리소스가 서버에 없음 ( 유저가 잘못 입력했을 수도 있고,,등등)
        - 클라이언트가 권한이 부족한 리소스에 접근할 때 해당 리소스를 숨기고 싶을 때( 403도 내기 시러!!)
- 5xx (Server Error): 서버 오류, 서버가 정상 요청을 처리하지 못함
    - 500 Internal Server Error

        서버 문제로 오류 발생, 애매하면 무조건 500

    - 503 Service Unavailable

        서비스 이용 불가

        - 서버가 일시적인 과부하에 걸리거나 다른 작업때문에 서버 다운이 필요할 경우

    ‼️ 진짜로 서버에 문제가 생겼을 경우에만 5xx에러를 보내야 한다. ‼️

    ‼️비즈니스 로직상의 문제(예외의 경우)를 500으로 해결해선 안되고 400과 200대로 해결해야 한다 ‼️

❔만일 모르는 상태 코드가 등장하면❔

- 클라이언트는 상위 상태코드로 해석해서 처리

      299 ??? → 2xx (Successful)

      599 ??? → 5xx ( Server Error)

## HTTP Header

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/768703c5-4ca1-4ec3-ac29-9135dd77fdba/_2021-05-31__9.02.04.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/768703c5-4ca1-4ec3-ac29-9135dd77fdba/_2021-05-31__9.02.04.png)

header-field = field-name ":" OWS(띄어쓰기 허용) field-value OWS

** field-name은 대소문자 구분 X

- 용도
    - HTTP 전송에 필요한 모든 부가정보들이 들어간다

        예) 메세지 바디의 내용, 메세지 바디의 크기, 압축, 인증, 요청 클라이언트, 서버 정보, 캐시 관리 정보 ...

    - 필요시 임의의 헤더 추가 가능

### HTTP BODY

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/77b38348-2c90-43e9-b906-df4d76a59e3f/_2021-06-03__1.34.15.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/77b38348-2c90-43e9-b906-df4d76a59e3f/_2021-06-03__1.34.15.png)

- message body를 통해 표현 데이터 전달
- message body = **payload**
- 표현은 요청이나 응답에서 전달할 실제 데이터
- 표현 헤더는 표현 데이터를 해석할 수 있는 정보 제공 (데이터유형 (html,json), 데이터 길이 ..)

### General한 정보성 헤더

- From : 유저 에이전트(클라이언트)의 이메일 정보
    - 잘 사용 X
    - 검색 엔진 같은 곳에서 주로 사용 (우리 사이트 오지 마세요!! 하고 요청할 때)
- **Referer : 이전 웹 페이지 주소**
    - 현재 요청된 페이지의 이전 웹 페이지 주소

        구글에서 검색 후 아무 페이지로 이동해 Request Headers의 referer을 확인하면 [https://www.google.com/ 이라고](https://www.google.com/이라고) 작성되어있음

    - A → B로 이동하는 경우 B를 요청할 때 Regerer:A를 포함해서 요청
    - 유입 경로 분석 가능
    - 요청에서 사용
- User-Agent : 유저 에이전트 애플리케이션 정보 ( = 내 웹브라우저 정보)
    - 클라이언트의 애플리케이션 정보
    - 통계 정보
    - 어떤 종류의 브라우저에서 장애가 발생하는지 로그를 파싱해보면 파악 가능
    - 요청에서 사용

    *💡 Parsing(파싱) - 구문 분석, 데이터를 분해 분석하여 원하는 형태로 조립하고 다시 빼내는 프로그램 💡*

- Server : 요청을 처리하는 ORIGIN 서버의 소프트웨어 정보
    - 응답에서 사용

    *💡 ORIGIN 서버 - HTTP request를 보내면 중간에 여러 프록시 서버들을 거치게 된다. 실제 내 request가 도착해 응답을 해줄 마지막 진짜 서버 💡*

- Date : 메세지가 발생한 날짜와 시간
    - 응답에서 사용

### Special한 정보성 헤더

- Host : 요청한 호스트 정보(도메인)
    - 요청에서 사용
    - ‼️**필수값 헤더**‼️
    - 하나의 서버가 여러 도메인을 처리해야 할 때

        ⇒  하나의 IP주소에 여러 도메인이 적용되어 있을 때

        ⇒ IP로만 통신하기 때문에 호스트 정보가 없으면 어떤도메인으로 처리할지 알 수 없음

- Location : 페이지 리다이렉션

    웹 브라우저는 3xx 응답의 결과에 Location 헤더가 있으면, Location 위치로 자동 이동(리다이렉트)

- Allow : 허용 가능한 HTTP 메서드
    - 405(Method Not Allowed) 에서 응답에 포함해야함
    - Allow: GET, HEAD, PUT
- Retry-After : 유저 에이전트가 다음 요청을 하기까지 기다려야 하는 시간
    - 503(Service Unavailable) : 서비스가 언제까지 불능인지 알려줄 수 있음 (날짜, 초단위 등등) 
