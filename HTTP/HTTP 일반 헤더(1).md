### HTTP 헤더 개요

- HTTP 전송에 필요한 모든 부가정보
    - 메시지 바디의 내용 및 크키, 압축, 인증 요청 클라이언트, 서버 정보, 캐시 관리 정보 등등
- 표준 헤더 필더가 많고 필요시 임의의 헤더 추가 가능

### HTTP 헤더 분류

- General 헤더 : 메세지 전체에 적용되는 정보 예) Connection : close
- Request 헤더 : 요청 정보 예) User-Agent : Mozilla/5.0 (Macintosh)
- Response 헤더 : 응답 정보 예) Server: Apache
- Entity 헤더 : 엔티티 바디 정보 예) Content-Type : text/html. Content-Length : 3423

### HTTP BODY

- 과거(****1999년 RFC2616 현재는 폐기)****
    - 메시지 본문(message body)은 엔티티 본문(entity body)을 전달하는데 사용
    - 엔티티 본문은 요청이나 응답에서 전달할 실제 데이터
    - 엔티티 헤더는 엔티니 본문의 데이터를 해석할 수 있는 정보 제공
        - 데이터 유형(html, json), 데이터 길이, 압축정보
- 현재 (****2014년 RFC7230~7235 등장)****
    - 엔티티라는 개념이 사라지고 표현이라는 개념으로 변경
    - 엔티티(Entity) -> 표현(Representation)
    - Representation = representation Metadata + Representation Data
    표현 = 표현 메타데이터 + 표현 데이터
    - 메시지 본문(message body)을 통해 표현 데이터 전달 즉 메시지 본문 = 페이로드(payload)
    - **표현**은 요청이나 응답에서 전달할 실제 데이터**표현 헤더는 표현 데이터**를 해석할 수 있는 정보 제공
    - 데이터 유형(html, json), 데이터 길이, 압축 정보 등등
    - 참고: 표현 헤더는 표현 메타데이터와, 페이로드 메시지를 구분해야 하지만, 여기서는 생략
### 표현

- 서버와 클라이언트 통신 시 데이터를 어떻게 표현 할 것인지에 대한것을 정의 해야함
- Content-Type: 표현 데이터의 형식
- Content-Encoding: 표현 데이터의 압축 방식
- Content-Language: 표현 데이터의 자연 언어
- Content-Length: 표현 데이터의 길이
- 표현 헤더는 전송, 응답 둘다 사용

### Content-Type

- 표현 데이터의 형식 설명
- 미디어 타입, 문자 인코딩
    - text/html; charset=utf-8
    - application/json
    - image/png

### Content-Encoding

- 표현 데이터를 압축하기 위해 사용
- 데이터를 전달하는 곳에서 압축 후 인코딩 헤더 추가
- 데이터를 읽는 쪽에서 인코딩 헤더의 정보로 압축을 해제

### Content-language

- 표현 데이터의 자연 언어를 표현
- Content-language를 사용하여 클라이언트에서 언어를 선택하여 설정할 수도 있음

### ****Content-Length****

- 바이트 단위로 Transfer-Encoding(전송 코딩)을 사용하면 Content-Length를 사용하면 안됨

### 컨텐츠 협상

- 클라이언트가 선호하는 표현을 서버에게 요청
- 협상 헤더는 요청시에만 사용
- Accept : 클라이언트가 선호하는 미디어 타입 전달
- Accpet-Charset : 클라이언트가 선호하는 문자 인코딩
- Accpet-Encoding : 클라이언트가 선호하는 압축 인코딩
- Accpet-Language : 클라이언트가 선호하는 자연 언어(ko, en 등등)

- Accpet-Language를 사용하게 되면 클라이언트에서 서버로 사용할 언어를 요청할 수 있다.
- 서버에서 클라이언트에서 요청한 언어를 지원하게 되면 해당 언어로 클라이언트에게 응답을 하게된다.

> 이때 해당하는 언어가 없을 때는 어떻게 되나???!!
> 
- 브라우저에서 지원하는 언어중 클라이언트에서 요청 우선순위로 지정한 언어에서 가장 높은 우선순위를 가진 언어로 응답을 한다!!
- ****Quality Values(q)****
    - 0~1로 나타내며 **클수록 높은 우선순위**생략하면 1
    - 구체적인 것이 우선이 됨
    - **Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8,en;q=07**
        1. ko-KR;q=1 (q생략)
        2. ko;q=0.9
        3. en-US;q=0.8
        4. en;q=0.7