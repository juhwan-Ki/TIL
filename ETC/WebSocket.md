### WebSocket
- 웹소켓 프로토콜은 클라이언트와 서버의 양방향 통신을 지원하는 프로토콜로 HTTP와는 다른 TCP 프로토콜이지만 HTTP에서 동작가능하게 디자인 되었고 80, 443 포트(ws 프로토콜)를 사용하며 방화벽규칙을 재사용할 수 있도록 되어있다
- 일반 HTTP 요청에 Upgrade 헤더를 포함한 reqeust를 전송하면 WebSocket protocol로 변환되며 WebSocket interaction이 시작된다

``` 
[요청]

GET /spring-websocket-portfolio/portfolio HTTP/1.1
Host: localhost:8080
Upgrade: websocket             ---- 1
Connection: Upgrade            ---- 2
Sec-WebSocket-Key: Uc9l9TMkWGbHFD2qnFHltg==
Sec-WebSocket-Protocol: v10.stomp, v11.stomp
Sec-WebSocket-Version: 13
Origin: http://localhost:8080

1. Upgrade 헤더
2. Upgrade connection 사용
```
위와 같은 요청을 받은 웹 소켓을 지원하는 서버는 일반적으로 200 상태 코드 대신에 101 상태코드를 반환한다

```
[응답]

HTTP/1.1 101 Switching Protocols     ---- 1
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: 1qVdfYHU9hPOl4JYYNXF623Gzn0=
Sec-WebSocket-Protocol: v10.stomp

1. 프로토콜 스위치
```
http upgrade 요청을 받아 성공적인 핸드쉐이크가 성공한 이후에 tcp 소켓은 클라이언트와 계속 열어두고 메시지를 주고 받는다

### HTTP VS WebSocket 
- HTTP
    - 비연결성(stateless) -> 매번 연결을 맺고 끊는 과정의 비용이 있음
    - 단방향 통신(요청/응답 모델)
    - 헤더의 비중이 큼
    - 실시간 통신에는 부적합

- WebSocket
    - 연결지향(stateful) -> 한번 연결 맺은 뒤 유지
    - 양방향 통신
    - 핸드 쉐이크 과정에서 헤더의 비중이 크지만 한번 연결되면 간단한 메시지들만 오고감
    - 실시간 통신에 적합

### Stomp(Simple Text Oriented Message Protocol)
- websocket 위에서 동작하는 문자 기반 메세징 프로토콜로써 클라이언트와 서버가 전송할 메세지의 유형, 형식, 내용들을 정의하는 매커니즘이다
- TCP와 웹소켓과 같은 신뢰할 수 있는 양방향 스트리밍 네트워크 프로토콜에서 사용할 수 있다
- 기본적으로 pub / sub 구조로 되어있어, 메세지를 전송하고 받아 처리하는 부분이 확실히 정해져있다
- http와 마찬가지로 frame을 사용해 전송하는 프로토콜이다

- Frame 구조
```
COMMAND
header1 : value1
header2 : value2
 
Body^@

- COMMAND : 메세지의 타입을 나타내는 문자열이다 SEND, SUBSCRIBE, UNSUBSCRIBE 등이 있다
- header1, header2 : 추가 정보를 제공하는 헤더이다
- destination : 이 헤더로 메시지를 보내거나(SEND), 구독(SUBSCRIBE)할 수 있다
- Body : 메세지의 내용이다
- ^@ : NULL문자이다. Body의 끝을 나타낸다

SEND, SUBSCRIBE COMMAND 요청 Frame에는 메시지가 무엇이고 누가 받아서 처리할지에 대한 Header 정보가 포함되어 있다
이런 명령어들은 "destination" 헤더를 요구하는데 이것이 어디에 전송할지, 혹은 어디에서 메시지를 구독할 것 인지를 나타낸다
```
