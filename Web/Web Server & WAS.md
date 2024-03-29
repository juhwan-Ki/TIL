### Web Server 와 WAS(Web Application Server)의 차이

- Web Server
    - HTTP protocol을 기반으로 하여 클라이언트(웹 브라우저 or 웹 크롤러)의 요청을 서비스하는 기능을 담당
    - Web Server의 기능
        1. 정적인 컨텐츠 제공(Image, HTML, CSS, JS 등) 
            1. WAS를 거치치 않고 바로 자원을 제공 
        2. 동적인 컨텐츠 제공을 위한 요청 전달 → WAS로 요청 전달
            1. 클라이언트의 요청(Request)을 WAS로 보내고 WAS에서 처리한 결과를 클라이언트에게 응답(Response)
    - Web Server 종류
        - Apache, Nginx, IIS
- WAS(Web Application Server)
    - DB조회나 다양한 로직 처리를 요구하는 동적 컨텐츠를 제공하기 위해 만들어진 서버
    - HTTP를 통해 컴퓨터나 장치에 애플리케이션을 수행해주는 미들웨어
    - 웹 컨테이너 혹은 서블릿 컨테이너라고도 불림
        - 서블릿이란?
            - 클라이언트의 요청을 처리하고 그 결과를 반환하는 클래스를 말한다.즉 서블릿이란 네트워크 요청을 처리하기 위한 클래스이며 서블릿 컨테이너에 의해 관리된다.
    - WAS의 역할
        - WAS = Web Server + Web Container
        - Web server의 기능들을 구조적으로 분리하여 처리하고자 하는 목적으로 제시됨
    - WAS의 기능
        1. 프로그램 실행 환경과 DB 접속 기능 제공
        2. 여러 개의 트랜잭션(논리적인 작업 단위)관리 기능
        3. 업무를 처리하는 비즈니스 로직 수행
    - WAS의 종류
        - Tomcat, JBoss, Jeus 등
- Web Server가 필요한 이유?
    - 정적 컨텐츠만 처리하도록 기능을 분배하여 서버의 부담을 줄 일 수 있음
- WAS가 필요한 이유?
    - 웹 페이지는 정적 컨텐츠와 동적 컨텐츠가 모두 존재
    - 이때, Web Server만을 이용한다면 사용자가 원하는 요청에 대한 결과값을 모두 미리 만들어 놓고 서비스를 해야 하는데 이렇게 수행하기에는 자원이 절대적으로 부족함
    - 따라서 WAS를 통해 요청에 맞는 데이터를 DB에서 가져와서 비즈니스 로직에 맞게 요청에 맞는 결과를 만들어서 제공하므로 자원을 효율적으로 사용할 수 있음

### WAS와 Web Server를 나누는 이유?

1. 기능을 분리하여 서버 부하 방지
    - **Web Server (Front-end 서버):** 정적인 콘텐츠(이미지, HTML, CSS 등)를 처리하고, 클라이언트로부터의 요청을 받아 정적 파일을 제공하는 역할만 하도록 하고 주로 정적 파일의 빠른 전송 및 캐싱 기능을 갖추도록 함
    - **WAS (Back-end 서버):** 동적인 콘텐츠(Java, Python, PHP 등으로 작성된 코드)를 실행하고, 데이터베이스와의 상호 작용을 담당하고 비즈니스 로직을 처리하고 동적인 페이지를 생성하도록 함
2. 물리적으로 분리하여 보안 강화
    - Web Server는 클라이언트와의 직접적인 통신을 처리하고, 이를 통해 악의적인 공격으로부터 보호
    - WAS는 내부 비즈니스 로직을 처리하므로 외부에 노출되는 부분을 최소화하여 시스템 보안을 강화
3. 여러대의 WAS 연결 가능 
    - Load Balancing을 위해서 Web Server를 사용
    - fail over(장애 극복), fail back 처리에 유리
    - 대용량 웹 어플리케이션의 경우(여러 개의 서버 사용) Web Server와 WAS를 분리하여 무중단 운영을 위한 장애 극복에 쉽게 대응
4. 여러 웹 애플리케이션 서비스 가능
    - Web Server에 PHP Application과 Java Application을 연결하여 함께 사용할 수 있음
5. 확장성이 좋음
    - Web Server와 WAS를 분리함으로써 각각의 서버를 독립적으로 확장할 수 있음