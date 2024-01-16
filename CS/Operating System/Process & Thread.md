### Process

- **프로세스는 실행파일(프로그램)이 메모리에 적재되어 CPU를 할당받아 실행되는 것을 말한다.**
- 각 프로세스는 독립적으로 고유의 가상 메모리 공간을 가지며 Code, Data, Stack, Heap 4개의 영역으로 이루어져 있다.
    
    
    | Code 영역 | 실행한 프로그램의 코드가 저장되는 메모리 영역 |
    | --- | --- |
    | Data 영역 | 프로그램의 전역 변수와 static 변수가 저장되는 메모리 영역 |
    | Heap 영역 | 런타임에 동적으로 할당되는 데이터가 저장되는 메모리 영역(ex.인스턴스) |
    | Stack 영역 | 함수 호출 시 생성되는 지역 변수와 매개 변수가 저장되는 임시 메모리 영역 |
- 또한 PCB를 통해 해당 프로세스에 대한 모든 정보(PID, 상태 등)를 저장한다.
- 프로세스의 상태는 생성, 준비, 실행, 대기, 종료로 이루어져 있다.
    - 생성(new) : OS에 막 생성된 상태
    - 준비(ready) : 프로세스가 ready queue에 올라가 실행 대기 상태
    - 실행(running) : 프로세스가 실행되는 상태
    - 대기(blocked) : running 상태에 있는 프로세스에서 입출력(I/O) 이벤트가 발생하여 blocked된 상태로 이벤트가 종료되면 다시 ready queue에 들어가게 됨
    - 종료(terminate) : 프로세스가 종료된 상태

### Thread

- **스레드는 프로세스가 할당 받은 자원을 이용하는 실행의 단위이다.**
- 스레드는 Stack만 따로 할당 받고 나머지 영역은 서로 공유한다.
    - 그 이유는 스레드는 독립적인 동작을 수행하기 위해 존재 하기 때문에 각각의 함수 호출시 발생하는 매개변수, 지역변수나 되돌아갈 주소값 등을 저장하는 메모리가 따로 필요하다.

### Process와 Thread의 차이
    - 프로세스
        - 프로그램을 메모리 상에서 실행중인 작업
        - 각각 별도의 주소공간 할당(독립적)
    - 스레드
        - 프로세스 안에서 실행되는 여러 흐름 단위
        - Stack만 따로 할당 받고 나머지 영역은 서로 공유