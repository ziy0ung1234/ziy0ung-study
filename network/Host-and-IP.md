# Localhost 와 127.0.0.1

보통 백엔드가 서버를 개발할때 localhost를 사용한다. 위코드에서는 서버 통신시 127.0.0.1과 localhost를 동일하게 사용했기 때문에 완전히 같다고 생각했지만 취업하고 일을 배우며 로컬에서 내부적으로 돌려볼 때 localhost를 사용해야 하는 이유를 알게 되었다.

localhost:8000과 호스트  IP주소인 127.0.0.1:8000을 브라우저 창에 입력하면 같은 화면이 출력된다.

이것을 `loop-back` 이라고 한다. 루프백 주소는 호스트 자신을 가리키는 IP(:12)로, 127.0.0.1 번 C 클래스를 사용하며, 주로 네트워크(:12) 관련 프로그램이나 환경의 테스트를 위한 목적으로 사용한다. 루프백주소를 이용하면 가상 네트워크 인터페이스(Virtual network interface) 환경을 구축할 수 있다. 즉 자신의 컴퓨터를 자체적으로 서버로 만들고 그 컴퓨터에서 콜을 보내 응답을 받을 수 있는 것을 말한다. 

## difference of localhost and 127.0.0.1

localhost는 일반적으로 로컬 머신 또는 로컬이라고 하며, 127.0.0.1은 일반적으로 로컬 주소로 간주된다. 127.0.0.1을 사용할 때와 달리 localhost를 사용할 때는 네트워크 카드를 통과하지 않지 않는다. 즉, localhost는 network card conf 및 방화벽 설정의 영항을 받지 않으며 모든 포트도 열려있다. 가끔 일부 통신은 localhost에서 작동하지만 127.0.0.1에서는 작동하지 않는 이유도 이와 같다. 그렇기 때문에 로컬 테스트 환경에서 localhost를 사용하는 것이 더 나을 수 있다. 또한 localhost를 사용해 리소스에 접근할 때는 로컬 사용자 권한으로 접근하고, 127.0.0.1을 사용하여 접근할 때는 네트워크 사용자 권한으로 접근한다.

_💡네트워크 카드 : 과거에 네트워크 연결이 가능한 데스크탑 PC를 제공하는 확장카드, 즉 PC를 인터넷에 연결할 수 있는 기능_

## Summary 

|  | localhost | 127.0.0.1 | local IP(192.56.xxx.xxx)|
|:----------:|:----------:|:----------:|:----------:|
| Network | Not connected | Not connected| Connected |
| Transmission | No network card, not firewall restriction | Network card, firewall restrictions | Network card, firewall restrictions |
| Access | Local access | Local access(Network user privileges) | Local or external access |

> reference : https://www.pixelstech.net/article/1538275121-Difference-between-localhost-and-127-0-0-1