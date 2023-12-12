## GC(Garbage Collection)
- 시스템에서 동적으로 할당됐던 메모리 영역 중에서 필요없어진 메모리 영역을 자동으로 회수하여 메모리를 관리해주는 기법 

### GC의 동작 원리
-  Stop The World
    - 가비지 컬렉션을 실행하기 위해 JVM이 애플리케이션의 실행을 멈추는 작업으로 GC가 실행될 때는 GC를 실행하는 쓰레드를 제외한 모든 쓰레드들의 작업이 중단되고, GC가 완료되면 작업이 재개된다.
-  Mark and Sweep
    - Stop The World를 통해 모든 작업을 중단시키면, GC는 스택의 모든 변수 또는 접근 가능한 객체를 스캔하면서 각각이 어떤 객체를 참고하고 있는지를 탐색하게 된다. 
    - 그 후 사용되고 있는 메모리를 식별하는데, 이러한 과정을 Mark라고 한다. 이후에 Mark가 되지 않은 객체들을 메모리에서 제거하는데, 이러한 과정을 Sweep라고 한다.

### Minor GC & Major GC
- Heap 영역은 Young 과 Old로 나뉜다
- Young 
    - 새롭게 생성된 객체가 할당(Allocation)되는 영역
    - 대부분의 객체가 금방 Unreachable 상태가 되기 때문에, 많은 객체가 Young 영역에 생성되었다가 사라진다.
    - Young 영역에 대한 가비지 컬렉션을 Minor GC라고 부른다.
- Old
    - Young영역에서 Reachable 상태를 유지하여 살아남은 객체가 복사되는 영역
    - Young 영역보다 크게 할당되며, 영역의 크기가 큰 만큼 가비지는 적게 발생한다. 
    - Old 영역에 대한 가비지 컬렉션(Garbage Collection)을 Major GC라고 부른다.

- Minor GC
    - Young 영역은 1개의 Eden 영역과 2개의 Survivor 영역, 총 3가지로 나뉘어진다.
    - Eden 영역: 새로 생성된 객체가 할당(Allocation)되는 영역
    - Survivor 영역: 최소 1번의 GC 이상 살아남은 객체가 존재하는 영역
    - Eden 영역이 꽉 차면 Minor GC가 발생하게 되는데, 사용되지 않는 메모리는 해제되고 Eden 영역에 존재하는 객체는 (사용중인) Survivor 영역으로 옮겨지게 되고 1~2번의 과정이 반복되다가 Survivor 영역이 가득 차게 되면 Survivor 영역의 살아남은 객체를 다른 Survivor 영역으로 이동시킨다.(1개의 Survivor 영역은 반드시 빈 상태가 된다.)
    - 이러한 과정을 반복하여 계속해서 살아남은 객체는 Old 영역으로 이동(Promotion)된다.

- Major GC
   - Major GC는 객체들이 계속 Promotion되어 Old 영역의 메모리가 부족해지면 발생한다. Young 영역은 일반적으로 Old 영역보다 크키가 작기 때문에 GC가 보통 0.5초에서 1초 사이에 끝난다. 그렇기 때문에 Minor GC는 애플리케이션에 크게 영향을 주지 않는다. 하지만 Old 영역은 Young 영역보다 크며 Young 영역을 참조할 수도 있다. 그렇기 때문에 Major GC는 일반적으로 Minor GC보다 시간이 오래걸리며, 10배 이상의 시간을 사용한다. 
