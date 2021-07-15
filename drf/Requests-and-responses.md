# 3. Requests and Responses

## Request object
- HttpRequest's extended version
- 더 유연한 request parsing
- 🚨<span style="color:red">request.data</span>🚨
 - request.POSTdhk 윳하지만 좀더 효율적이다
	- request.POST : only handeling form data, only use POST method
	- request.data : more data, can use POST,PUT,PATCH method

## Response object
- client가 요청한 타입으롤 render

## Status codes
- 좀 더 명확하게 전달하기 위해 직접 숫자를 쓰지 않고 DRF에서 제공하는 메세지 사용
	ex) HTTP_400_BAD_REQUEST

## Wrapping API Views


