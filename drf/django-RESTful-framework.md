1. django REST framework
===

## 1-1.What is the meaning of RESTful

- REST : HTTP의 URL과 HTTP method(GET, POST, PUT, DELETE)를 사용해 API 가독성을 높인 구조화된 시스템 아키텍쳐.
	하나의 URL로 최소 4가지의 HTTP method 전송 가능

 스마트폰 어플과 웹에서 동일한 기능을 제공할 때 기존의 웹서버를 계속 사용하면 매번 HTML을 읽어 해당 태그에 있는 정보를 찾아낼 수 없기 때문에, HTML로 렌더링 하는 웹서버가 아니라, JSON 혹은 XML과 같은 형식을 통해 데이터를 다루는 별도의 API 서버가 필요. RESTful 아키텍쳐를 HTTP method와 함께 사용해 웹, 데스크탑 앱, 스마트폰 어플들까지 하나의 API서버 생성 가능.

Django는 View 클래스 자체가 RESTful한 서버를 만들기 최적인 프레임 워크

## 1-2 Django REST Framework

- DRF란 Django안에서 RESTful API 서버를 쉽게 구축할 수 있도록 도와주는 오픈소스 라이브러리이다.

- DRF를 사용하는 이유
	- 웹 브라우저 API는 범용성이 크다. 개발을 쉽게 만들어준다.
	- 인증 정책에 OAuth1, OAuth2 를 위한 추가적인 패키지가 추가되어 있는 경우
	- 시리얼라이즈 기능을 제공해준다. (DB data -> JSON)
	- 문서화 및 커뮤니티 지원이 잘 되어있다.

## 1-3. Serializer (직렬화)
