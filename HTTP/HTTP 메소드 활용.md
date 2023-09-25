### 클라이언트에서 서버로 데이터 전송

- 쿼리 파라미터를 통한 데이터 전송
    - GET 이나 주로 정렬 필터(검색어)를 통해
- 메시지 바디를 통한 데이터 전송
    - POST, PUT, PATCH
    - 회원 가입, 상품 주문, 리소스 등록, 리소스 변경
- 데이터 조회
    - 정적 데이터 조회
        - 이미지, 정적 텍스트 문서 조회는 GET을 사용
        - 정적 데이터는 일반적으로 쿼리 파라미터(쿼리 스트링) 없이 리소스 경로로 단순하게 조회
    - 동적 데이터 조회
        - 검색, 게시판 목록에서 정렬 및 검색
        - 조회 조건을 줄여주는 필터, 조회 결과를 정렬하는 조건에 주로 사용
        - GET을 사용하여 쿼리 파라미터를 사용해서 데이터를 전송
            - HTTP Body를 사용할 수 있지만 지원하지 않는 서버도 있어 사용하지 않는걸 권장
        
    - HTML Form 데이터 전송 (POST)
        - HTML의 Form 태그를 사용하여 서버에 데이터를 전송하게 되면 웹 브라우저가 HTTP 메세지를 생성
        - Form 전송은 GET, POST만 가능
        - HTTP 메세지 생성
            - URL → action
            - HTTP 버전
            - Content-type → application/x-www-form-urlencoded(전송 데이터를 인코딩 처리)
            - body → 태그의 name 속성값을 key value 형식으로 변경
            - GET으로 전송할 수도 있지만 등록 및 수정 할때는 사용하지 않아야함

    - multipart/form-data
        - 파일 업로드 같은 바이너리 데이터 전송 시 사용
        - 다른 종류의 여러 파일과 폼의 내용을 함께 전송 가능        
    - HTTP API 데이터 전송
        - 서버 to 서버 통신에 사용
        - 웹 클라이언트
            - form을 사용하는것이 아닌 AJAX나 React, Vue.js 같은 웹 클라이언트와 API 통신
        - POST, PUT, PATCH 메소드와 함께 HTTP body를 사용해 데이터 전송에 사용
        - GET을 통해 조회 시 쿼리 파라미터로 데이터를 전송
        - Content-type : application/json을 주로 사용 (JSON을 대부분 사용)

### HTTP API 설계 예시

- API 설계 - POST 기반 등록(컬렉션)
    - 컬렉션 기반
        - 회원 목록 → /members → GET(정렬조건은 쿼리 파라미터로 추가 가능)
        - 회원 등록 → /members → POST
        - 회원 조회 → /members/{id} → GET (계층 구조로 설계)
        - 회원 수정 → /members/{id} → PATCH, PUT, POST
            - PATCH는 일부분만 수정할 때 사용하는 것이 좋음
            - PUT은 게시물 수정등 모든 데이터를 덮어씌워야 할 때 사용
            - 애매하면 POST를 사용하면됨
        - 회원 삭제 → /members/{id} → DELETE
    - 특징
        - POST로 신규 등록을 하게 되면 서버에서 리소스 URI를 결정하고 만들어줌
        - 이러한 형식을 컬렉션이라고 함
            - 서버가 관리하는 리소스 디렉토리로 서버가 리소스의 URI를 생성하고 관리
- API 설계 → PUT 기반 등록(스토어)
    - 스토어 기반
        - 파일 목록 /files → GET
        - 파일 조회 /files/{filename} → GET
        - 파일 등록 /files/{filename} → PUT
        - 파일 삭제 /files/{filename} → DELETE
        - 파일 대량등록 /files → POST
    - 특징
        - filename으로 등록하면 기존 파일이 없으면 생성 있으면 대치함
        - 즉 클라이언트가 리소스 URI를 알고 있어야하며 직접 리소스의 URI를 지정
        - 이러한 형식을 스토어라고 함
            - 클라이언트가 관리하는 리소스 저장소로 클라이언트가 리소스의 URI를 알고 관리
- HTML FORM 사용
    - GET, POST만 지원
        - 회원 목록 /members → GET
        - 회원 등록 폼  /members/new → GET
        - 회원 등록  /members/new , /members→ POST
        - 회원 조회 → /members/{id} → GET
        - 회원 수정 폼 → /members/{id}/edit → GET
        - 회원 수정 폼 → /members/{id}/edit, /members/{id}/ → POST
        - 회원 삭제 →  /members/{id}/delete
    - GET, POST만 지원하기 때문에 제약이 있음
    - 컨트롤 URI
        - 위 제약을 해결하기 위해 동사로 된 리소스 경로를 사용
        - POST의 /new, /edit /delete 가 컨트롤 URI
        - HTTP 메소드로 해결하기 애매할 경우 사용(HTTP API 포함)

### 참고

- 문서
    - 단일 개념(파일 하나, 객체 인스턴스, 데이터베이스 row)
- 컬렉션
    - 서버가 관리하는 리소스 디렉토리
    - 서버가 리소소의 URI를 생성하고 관리
- 스토어
    - 클라이언트가 관리하는 자원 저장소
    - 클라이언트가 리소스의 URI를 알고 관리
- 컨트롤러(컨트롤 URI)
    - HTTP 메소드(GET,POST 등등)으로만으로 해결하기 여러운 추가 프로세스를 진행
    (문서, 컬렉션, 스토어)
    - 동사를 직접 사용
    - 컬렉션과 문서를 가지고 최대한 해결한 후 해결이 되지 않으면 컨트롤 URI를 사용