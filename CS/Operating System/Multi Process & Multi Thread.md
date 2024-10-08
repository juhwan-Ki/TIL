### Multi Process

- 2개 이상의 프로세스가 동시에 실행되는 것을 말한다 이때 메모리는 각각 독립된 메모리를 할당 받는다
- 동시라는 말에는 동시성과 병렬성을 의미한다
- 동시성은 CPU core가 1개일 때, 여러 프로세스를 짧은 시간동안 번갈아 가면서 연산을 하게 되는 시분할 시스템(time sharing system)으로 실행되는 것을 말한다
- 병렬성은 CPU core가 여러개일 때, 각각의 core가 각각의 프로세스를 연산함으로써 프로세스가 동시에 실행되는 것이다

### 장점

- 프로그램 안정성
    - 프로세스 마다 독립적인 메모리를 사용하기 때문에 한 프로세스가 비정상적으로 종료 되어도 다른 프로세스에게 영향을 미치지 않음
    - 이러한 결과로 프로그램 전체의 안정성을 확보할 수 있음

### 단점

- Context Switching Overhead
    - 프로세스를 컨텍스트 스위칭 하면, CPU는 다음 프로세스의 정보를 불러오기 위해 메모리를 검색하고, CPU 캐시 메모리를 초기화하며, 프로세스 상태를 저장하고, 불러올 데이터를 준비해야 하기 때문에, 이로 인한 빈번한 Context Switching 작업으로 인해 비용 오버헤드가 발생할 수 있게 된다
- 자원 공유 비효율성
    - 멀티 프로세스는 각 프로세스가 독립적인 메모리 공간을 가지므로, 결과적으로 메모리 사용량이 증가하게 된다 만일 각 프로세스간에 자원 공유가 필요할 경우 프로세스 사이의 어렵고 복잡한 통신 기법인 IPC(Inter-Process Commnuication)을 사용하여야 한다

### Context

- 시분할 시스템에서는 한 프로세스가 매우 짧은 시간동안 CPU를 점유하여 일정부분의 명령을 수행하고 다른 프로세스에게 점유권을 넘기게 된다 그 후 다시 차례가 되면 다시 CPU를 점유하여 명령을 수행한다 이전에 어디까지 명령을 수행했고 register에는 어떠한 값이 저장되어 있었는지에 대한 정보가 필요하게 된다 이 정보를 context라 하고 context는 PCB(Process Control Block)에 저장 된다

### PCB(Process Control Block)

- 운영체제가 프로세스를 제어하기 위해 정보를 저장해 놓는 곳으로, 프로세스의 상태 정보를 저장하는 자료구조이다 PCB에는 PID, 프로세스의 상태, 프로그램 카운터, 레지스터 등의 정보가 저장된다
    
    
    | PCB |  |
    | --- | --- |
    | Process State | new, running, waiting, halted 등의 state가 있다 |
    | Process Number | 해당 process의 number |
    | Program counter(PC) | 해당 process가 다음에 실행할 명령어의 주소를 가리킨다 |
    | Registers | 컴퓨터 구조에 따라 다양한 수와 유형을 가진 register 값들 |
    | Memory limits | base register, limit register, page table 또는 segment table 등 |
    | Process ID | 프로세스 식별자를 저장하는 프로세스 ID 또는 PID라는 고유 한 ID가 할당 |

### CPU Register

- CPU는 PC(Program counter) register가 가리키고 있는 명령어를 읽어들여 연산을 진행한다 PC register에는 다음에 실행될 명령어의 주소값이 저장되어 있다 multi process시스템에서는 process1이 진행되고 있을 때는 process1의 code 영역을 PC register가 가리키다가, process2가 진행되면 process2의 code 영역을 가리키게 된다 CPU는 PC register가 가리키는 곳에 따라 process를 변경해 가면서 명령어를 읽어들이고 연산을 하게 된다

### Context switching

- Context switching란 한 프로세스에서 다른 프로세스로 CPU 제어권을 넘겨주는 것을 말한다
- CPU가 어떤 하나의 프로세스를 실행하고 있는 상태에서 인터럽트 요청에 의해 다음 우선 순위의 프로세스가 실행되어야 할 때 기존의 프로세스의 상태 또는 레지스터 값(Context)을 저장하고 CPU가 다음 프로세스를 수행하도록 새로운 프로세스의 상태 또는 레지스터 값(Context)를 교체하는 작업을 Context Switching라고 한다
- 이 때 이전의 프로세스의 상태를 PCB에 저장하여 보관하고 새로운 프로세스의 PCB를 읽어서 보관된 상태를 복구하는 작업이 이루어진다

### Multi Thread

- 멀티 스레드는 하나의 프로세스에서 여러가지 일을 동시에 처리하기 위해 스레드를 사용하여 여러 작업을 병렬로 처리하는 것을 말한다
- 멀티 스레드는 하나의 프로세스에서 2개 이상의 프로세스가 동작하며 각 스레드들은 Stack 메모리를 제외한 나머지 영역(Code, Data, Heap) 영역을 공유하게 된다

### 장점

- 스레드는 프로세스보다 가벼움
    - 스레드는 프로세스 내에 생성되고 스레드마다 Stack 영역을 제외한 나머지 영역을 같이 공유하기 때문에 프로세스보다 용량이 작고 가볍다
- 자원의 효율성
    - 멀티 스레드는 하나의 프로세스 내에서 여러 개의 스레드를 생성되기 때문에, heap 영역과 같은 공유 메모리에 대해 스레드 간에 자원을 공유가 가능하다 이를 통해 자원 공유에 필요한 자원을 사용하지 않아도 되기 때문에 효율성이 좋아진다
- Context Switching 비용 감소
    - 멀티 스레드에도 Context Switching 비용이 발생하지만 프로세스와 다르게 스레드는 공유자원을 제외하고 Stack과 register만 교체하면 되므로 비용이 감소된다

### 단점

- 안정성 문제
    - 멀티 프로세스에서는 각 프로세스가 독립적으로 동작하기 때문에 하나의 프로세스가 문제가 생겨도 다른 프로세스들이 영향을 받지 않아 프로그램의 안정성에는 크게 문제가 생기지 않는다
    - 하지만 멀티 스레드는 하나의 스레드에서 문제가 생기면 다른 스레드에게도 영향이 가서 프로그램의 안정성에 문제가 생길 수 있다
- 동기화로 인한 성능 저하
    - 멀티 스레드 모델은 여러 개의 스레드가 공유 자원에 동시에 접근할 수 있기 때문에, 동기화 문제가 발생할 수 있다 예를들어 여러 스레드가 동시에 한 자원을 변경해 버린다면 의도되지 않은 엉뚱한 값을 읽어 서비스에 치명적인 버그가 생길수도 있다 따라서 스레드 간 동기화(syncronized)는 데이터 접근을 제어하기 위한 필수적인 기술이다 동기화 작업은 여러 스레드들이 자원에 대한 접근을 순차적으로 통제하는 것이다 그러면 동시 접근으로 인한 동시 수정과 같은 현상은 일어나지 않게 된다 그러나 동기화 작업은 여러 스레드 접근을 제한하는 것이기 때문에병목 현상이 일어나 성능이 저하될 가능성이 높다는 단점이 있다

### Stack memory &  PC register

- 스레드가 함수를 호출하기 위해서는 인자 전달, Return Address 저장, 함수 내 지역변수 저장 등을 위한 독립적인 stack memory 공간을 필요로 한다 결과적으로 thread는 process로부터 Stack memory 영역은 독립적으로 할당받고, Code, Data, Heap 영역은 공유하는 형태를 갖게 된다
- 또한 멀티 스레드에서는 각각의 스레드마다 PC register를 가지고 있어야 한다 그 이유는 한 프로세스 내에서도 스레드 끼리 context switching이 일어나게 되는데, PC register에 code address가 저장되어 있어야 이어서 실행을 할 수 있기 때문이다
