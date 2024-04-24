### Stack

- 특징
    - 선형 자료구조
    - 삽입, 삭제 연산이 한 방향에서 이루어진다
    - LIFO(Last In First Out) : 나중에 들어간 원소가 제일 먼저 나오는 구조
        - ex) 프링글스
- 용어
    - Top : 스택에 데이터가 삽입 될 위치
- 주요 연산
    - push : 스택의 top에 원소 삽입
    - pop : 스택의 top에 원소 삭제 및 반환
    - peek : 스택의 top에 원소 반환
- Stack 시간 복잡도
    - 데이터 삽입/삭제 : O(1)
    - top 데이터 조회 : O(1)
    - 특정 데이터 조회 : O(n)
- 활용
    - 시스템 스택(System Stack) / 실행시간 스택(Runtime stack) : 프로그램의 함수 호출과 복귀에 따른 실행 순서 관리
    - 인터럽트 루틴 처리
    - 웹 브라우저 방문 기록 관리 (뒤로가기)
    - 실행 취소 (undo)
    - 수식의 후위 표기법(Postfix Notation)
    - 수식의 괄호식 검사
    - 계산기 검사
    - 깊이 우선 탐색(DFS, Depth-First Search)
    
    > **프로그램의 함수 호출과 복귀에 따른 실행 순서 관리는 다음과 같은 과정을 가진다.**
    > 
    > 1. 함수 호출이 발생하면 스택 프레임(stack frame)에 지역변수, 매개변수, 수행 후 복귀할 주소 등의 정보를 저장하여 시스템 스택에 삽입한다.
    > 2. 함수의 실행이 끝나면 시스템 스택의 top에 있는 stack frame 원소를 pop하고, frame에 저장되어 있던 복귀 주소를 확인하고 복귀한다.
    > 3. 함수 호출 - 복귀에 따라 이 과정을 반복하고, 전체 프로그램 수행이 종료되면 시스템 스택은 공백 스택이 된다.
    > 
    > 함수 호출은 가장 마지막에 호출된 함수가 가장 먼저 실행을 완료하고 복귀하는 후입선출 구조이기 때문에, 스택을 이용해 관리한다.
    > 

### Queue

- 특징
    - 선형 자료구조
    - 한 방향에서 삽입 연산, 반대 방향에서 삭제 연산이 이루어진다
    - FIFO(First In Last Out) : 먼저 들어간 원소가 먼저 나오는 구조이다.
- 용어
    - Front / Head : 큐에서 데이터가 삭제될 위치
    - Rear / Tail : 큐에서 마지막 데이터가 삽입된 위치
- 주요 연산
    - enQueue : 큐의 rear에 원소 삽입
    - deQueue : 큐의 front에 원소 삭제 및 반환

> Java에서는 Queue를 사용할 때 java.util.Queue로 사용하는데 이때 Queue는 인터페이스 이므로 그 구현체로 java.util.LinkedList를 사용한다
> 
- Java에서 Queue 사용 메소드
    - 값 추가
        - add(value) : 성공 시 true, 실패 시 Exception
        - offer(value) : 성공 시 true, 실패 시 false
    - 삭제
        - remove() : 성공 시 value 리턴, 공백 큐면 NoSuchElementException
        - remove(value) : 값이 존재하면 삭제 후 true, 존재 하지 않으면 false
        - poll() : 성공 시 value 리턴, 공백 큐면 null 리턴
        - clear() : 반환 값 x (큐 초기화)
    - 값 반환
        - element() : 큐 head에 위치한 value 리턴 공백 큐면 NoSuchElementException
        - peek() : 큐 head에 위치한 value 리턴 공백 큐면 null 리턴
    - 공백 확인
        - isEmpty() : 공백 큐면 true 아니면 false
- Queue 시간 복잡도
    - 데이터 삽입/삭제 : O(1)
    - front 데이터 조회 : O(1)
    - 특정 데이터 조회 : O(n)
- 활용
    - 프로세스 레디 큐
    - 스케쥴링
    - 캐시(Cache) 구현
    - 네트워크 패킷 전송시 필요한 버퍼 대기 큐
    - javascript의 Event Loop 관리 (비동기 처리)
    - 키보드 버퍼
    - 프린터의 출력 처리
    - 너비 우선 탐색(BFS, Breadth-First Search)

- 주요 연산
1. enqueue (삽입) : Queue에 새로운 item을 삽입한다.
2. dequeue (삭제) : Queue의 가장 앞에 있는 item을 큐에서 삭제하고 return한다.

### Priority queue
- Queue 자료구조는 시간 순서상 먼저 집어 넣은 데이터가 먼저 나오는 선입선출 구조로 저장하는 형식이지만 이와 다르게 우선순위큐(priority queue)는 들어간 순서에 상관없이 우선순위가 높은 데이터가 먼저 나오는 자료구조이다.

- Queue의 operation 시간복잡도는 enqueue O(1), dequeue O(1)이고,Priority queue는 push O(logn), pop O(logn)이다. Priority Queue는 보통 Heap을 이용해서 구현한다.


