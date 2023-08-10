## JPA란?

- Java Persistence API로 자바 진영 ORM 기술 표준
- Java와 JDBC API를 사용하여 실제 DB와 통신을 하여 데이터를 관리
- JPA는 인터페이스의 모음이고 그 인터페이스를 구현한 하이버네이트를 사용

![Untitled](https://github.com/juhwan-Ki/TIL/assets/87765888/8d04e3a2-6e5b-4fff-95ee-8ac60cbb8fcf)

### ORM이란?

- Object-relational-mapping(객체 관계 매핑 즉 객체와 관계형 DB를 연결해줌)
- 객체는 객체대로 관계형 데이터 베이스는 관계형 데이터 베이스 대로 설계

### JPA 구동방식

- JPA는 설정 정보 파일인 persistence를 사용해서 EntityManagerFactory를 생성
- 생성된 EntityManagerFactory는 EntityManager를 생성하여 엔티티를 관리
- 어플리케이션은 단 하나의 EntityManagerFactory를 가지고 어플리케이션이 실행하면 생성하고 종료하면 소멸
- 실제 DB 사용을 담당하는 EntityManager는 하나의 Thread(Transaction)가 생성될 때마다 EntityManagerFactory가 생성
    - JPA의 모든 데이터 변경은 트랜잭션 안에서 실행
- Thread를 모두 사용하고 commit or rollback을 해야하며 close을 해줘야함
    - JPA는 commit을 한 후 flush를 해야 실제로 DB에 반영됨!


![Untitled (1)](https://github.com/juhwan-Ki/TIL/assets/87765888/b12882fd-fd47-4d2b-8062-7a729a29e526)


### JPA의 성능 최적화 기능

- 1차 캐시와 동일성(identity) 보장
    - 같은 트랜잭션 안에서는 같은 엔티티를 반환 (약간의 조회 성능 향상)
- 트랜잭션을 지원하는 쓰기 지연 - INSERT
    - 트랙잭션을 커밋할 때까지 INSERT SQL을 모음
    - JDBC BATCH SQL 기능을 사용해서 한번에 SQL 전송
    - SQL을 한번에 모아서 전송하기 때문에 데이터 베이스와 통신을 줄여 성능을 높임
- 지연 로딩과 즉시 로딩
    - 지연 로딩 : 객체가 실제 사용될 때 로딩
    - 즉시 로딩 : JOIN SQL로 한번에 연관된 객체까지 미리 조회

