### Stack
- Stack은 나중에 들어간 요소가 제일 먼저 나오는 후입선출(LIFO, Last In First Out)의 자료구조이다. 

- Stack은 브라우저의 뒤로가기,  ctrl + z 되돌리기 연산,깊이우선탐색(DFS) 등이 있다.

- 주요 연산
1. push(삽입) : Stack에 새로운 item을 추가한다.
2. pop(삭제) : 가장 위에 있는 요소를 return하고, Stack에서 해당 요소를 제거한다.
3. peek(최상단 아이템 반환) : 가장 위에 있는 요소를 return 한다. pop과는 다르게 스택에서 해당 요소를 제거하지 않는다.
+ top : 가장 위에 있는 item의 인덱스를 나타낸 Stack Class의 멤버 변수이다. isEmpty(), isFull(), peek() 등을 구현할 때 사용한다.

### Queue
- Queue는 먼저 들어간 요소가 제일 먼저 나오는 선입선출(FIFO, First In First Out)의 자료구조이다.

- Queue는 버퍼, 프린터 큐, CPU 스케줄링 Ready 큐,BFS(너비 우선 탐색) 알고리즘 등에 사용된다.

- 주요 연산
1. enqueue (삽입) : Queue에 새로운 item을 삽입한다.
2. dequeue (삭제) : Queue의 가장 앞에 있는 item을 큐에서 삭제하고 return한다.

### Priority queue
- Queue 자료구조는 시간 순서상 먼저 집어 넣은 데이터가 먼저 나오는 선입선출 구조로 저장하는 형식이지만 이와 다르게 우선순위큐(priority queue)는 들어간 순서에 상관없이 우선순위가 높은 데이터가 먼저 나오는 자료구조이다.

- Queue의 operation 시간복잡도는 enqueue O(1), dequeue O(1)이고,Priority queue는 push O(logn), pop O(logn)이다. Priority Queue는 보통 Heap을 이용해서 구현한다.


