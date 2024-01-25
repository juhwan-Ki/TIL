### REST API란?
- REST를 기반으로 만들어진 API를 의미한다.
- REST는 HTTP URI를 통해 자원을 명시하고 HTTP Method(POST, GET, PUT, DELETE, PATCH 등)를 통해 해당 자원(URI)에 대한 CRUD Operation을 적용하는 것을 의미한다.

### REST란?
- REST는 자원(Resource), 행위(Verb), 표현(Representations)으로 구성된다.
    - 자원 : 고유한 ID가 존재하고 자원을 구분하는건 HTTP URI이다.
    - 행위 : HTTP Method를 사용한다.
    - 표현 : Client가 자원의 상태(정보)에 대한 조작을 요청하면 Server는 이에 적절한 응답을 보낸다.

- REST의 특징
    1. Client - Server 구조
        - 자원이 있는 쪽을 Server 자원을 요청하는 쪽을 Client로 구분하여 서로의 의존성을 낮추게된다.
        - Server는 API 제공 및 비즈니스 로직처리 등을 수행
        - Client는 사용자 인증, 로그인 정보등을 관리
    2. Stateless(무상태)
        - HTTP는 Stateless Protocol이므로 REST 역시 무상태성을 갖는다.
        - Client의 context(인증, 로그인 정보등)를 Server에 저장하지 않는다.
        - 각 API 서버는 Client의 요청만을 단순 처리한다.
    3. 캐시 처리 가능 (Cacheable)
        - HTTP를 사용하기 때문에 캐시를 사용할 수 있고 캐시를 통해 응답시간을 단축 시켜 대용량 요청을 효율적으로 처리할 수 있다.
    4. Layered System(계층화)
        - 클라이언트와 서버가 분리되어 있기 때문에 중간에 프록시 서버, 암호화 계층 등 중간매체를 사용할 수 있어 자유도가 높다.
    5. Uniform Interface(인터페이스 일관성)
        - HTTP 표준에만 따른다면 모든 플랫폼에서 사용이 가능하며, URI로 지정한 리소스에 대한 조작을 가능하게 하는 아키텍쳐 스타일을 의미한다.

- REST의 장점
    - HTTP 프로토콜의 인프라를 그대로 사용하므로 REST API 사용을 위한 별도의 인프라를 구출할 필요가 없다.
    - HTTP 표준 프로토콜에 따르는 모든 플랫폼에서 사용이 가능하다.
    - REST API 메시지가 의도하는 바를 명확하게 나타내므로 의도하는 바를 쉽게 파악할 수 있다.
    - 서버와 클라이언트의 역할을 명확하게 분리한다.

- REST의 단점
    - 표준이 자체가 존재하지 않아 정의가 필요하다.
    - HTTP Method 형태가 제한적이다.
        - HTTP Method로 모든 API를 표현하기 애매하다.
        - Control URI
            - 단순한 데이터를 생성하거나, 변경하는 것을 넘어서 프로세스를 처리해야 하는 경우에 사용.
            - HTTP 메소드로 해결하기 애매할 경우 사용(HTTP API 포함)

### REST API 설계 원칙
1. URI는 동사보다는 명사를, 대문자보다는 소문자를 사용하여야 한다.
2. 슬래시(/)를 이용해 계층 관계를 표현한다.
3. 마지막에 슬래시 (/)를 포함하지 않는다.
4. 언더바(_) 대신 하이폰(-)을 사용한다.
5. 파일확장자는 URI에 포함하지 않는다.
6. 행위를 포함하지 않는다. ex) update, delete ...

