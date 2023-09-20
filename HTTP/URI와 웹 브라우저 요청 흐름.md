### URI(Uniform Resource Identifier)

- URI는 로케이터(locator), 이름(name)또는 둘다 추가로 분류 될 수 있다.
- URI는 크게 URL과 URN으로 나눌 수 있다.

![Untitled](https://github.com/juhwan-Ki/TIL/assets/87765888/77036a62-c72c-41a3-a66f-8e811739e845)


- URI 단어의 뜻
    - Uniform : 리소스를 식별하는 통일된 방식
    - Resource : 자원, URI로 식별할 수 있는 모든 것 (제한 없음)
    - Identifier : 다른 항목과 구분하는데 필요한 정보
    - URL : Uniform Resource Locator → 리소스가 있는 위치를 지정
    - URN : Uniform Resource Name → 리소스에 이름을 부여
        - URN 이름만으로 실제 리소스를 찾을 수 있는 방법이 보편화 되지 않음
- URL 분석
    - https://www.google.com/search?q=hello&hi=ko
- URL 전체 문법
    - scheme://[userinfo@]host[:port][/path][?query][#fragment]
    - ex) https://www.google.com:443/search?q=hello&hi=ko
    - scheme
        - 주로 프로토콜 사용
        - 프로토콜이란 ?
            - 어떤 방식으로 자원에 접근할 것인가 하는 약속 규칙 http, https, ftp 등
            - http는 80포트 https는 443포트를 주로 사용하고 https는 http에 보안을 추가한 것
    - userinfo
        - URL에 사용자정보를 포함해서 인증 거의 사용하지 않음
    - host
        - 호스트명으로 도메인명 또는 IP 주소를 직접 사용가능
    - port
        - 접속포트로 일반적으로 생략 http → 80 https → 443
    - path
        - 리소스 경로, 계층적 구조
    - query
        - key=value 형태
        - ?로 시작 &로 추가 가능
        - query String query parameter로 불림
    - fragment
        - html 내부 북마크 등에 사용

### 웹 브라우저 요청 흐름

- https://www.google.com:443/search?q=hello&hi=ko 로 요청을 보냈다고 가정
- 흐름
    1. google.com에 대한 IP를 DNS 서버에서 조회
    2. HTTP 요청 메세지 생성
        - 요청 메세지 : GET /[search?q=hello&hi=ko](https://www.google.com:443/search?q=hello&hi=ko) HTTP/1.1 HOST: www.google.com
    3. HTTP 메시지 전송
        
       ![Untitled (1)](https://github.com/juhwan-Ki/TIL/assets/87765888/cb28dc57-61d7-4a4f-a506-09c6e67a68f1)
       
        - 생성된 HTTP 메세지를 SOCKET 라이브러리를 통해 전달하면 핸드 쉐이크를 통해 해당 IP와 연결
        - 연결 후 TCP/IP에서 패킷을 생성(IP와 port정보, 전송 데이터를 추가) 후 인터넷으로 전송
        - 서버에서 요청된 패킷을 확인하여 요청 받은 데이터를 찾음
        - 정상적으로 찾게되면 요청 서버에서 HTTP 응답 메시지를 패킷으로 만든 후 클라이언트 서버로 전송
        - 응답 패킷을 확인하게 되면 HTTP 응답 메세지 안에 있는 HTML을 랜더링 하여 데이터를 확인