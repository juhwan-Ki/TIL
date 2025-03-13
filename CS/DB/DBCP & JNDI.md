### Connection, Connection Pool

- Connection은 네트워크상의 연결 자체를 의미
- Connection Pool은 Connection 객체를 관리하는 공간을 의미

### DBCP(Database Connection Pool)

- DBCP는 WAS가 실행되면서 DB와 미리 Connection을 맺어놓고 pool로 관리
    - 커넥션 풀(Connection Pool)을 어플리케이션 소스단에 설정 (Timeout, max pool size 등등)
- DB에 Query를 보낼 때 Connection을 생성하는 것이 아닌 pool에 있는 Connection 사용하고 사용 후 종료하는 것이 아닌 pool에 반납하여 재 사용 하도록 함
- Connection의 생성과 종료에 대한 비용을 절약
- Connection pool을 서버당 최대 커넥션 수를 제한할 수 있고 그에 따른 DB에 무한정 연결이 생성되는 것을 방지할 수 있음
- DBCP는 `Commons DBCP`, `HikariCP` 등이 있고 Spring boot 2.0 부터는 HikariCP를 사용

### JNDI(Java Naming and Directory Interface)

- Java를  사용하여 작성된 어플리케이선에 이름 지정 및 디렉토리 기능을 제공하는 API
- JNDI는 DBCP처럼 소스단에서 설정하는 것이 아닌 WAS에 DB Connection 객체를 미리 네이밍 해두는 방식
    - ex) tomcat server.xml에 네이밍을 설정해 놓고 사용
- 장점
    - DB 설정 정보를 파악하기 쉬움
        - 소스 레벨에서 설정 정보를 설정하지 않고 WAS단에 설정 정보를 저장하기 때문에 파악하기 쉬움
    - Connection pool을 효율적으로 사용할 수 있음
        - 어플리케이션 컨테이너는 하나이더라도 내부 어플리케이션은 여러 개가 될 수 있음
        (현재 회사 제품이 그럼 1개의 WAS에 여러 개의 프로젝트가 올라가 있음)
        - 이럴 경우 DBCP를 사용하면 각 어플리케이션 마다 DB pool이 각각 생성되어 풀이 많아져 효율이 떨어질 수 있음
        - JNDI를 사용하면 WAS단에서 DB pool을 하나로 관리하면 효율이 좋아짐
