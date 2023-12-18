### Blocking VS NonBlocking

- Blocking
    - 호출된 함수가 제어권을 가지고 있어 자신의 작업이 모두 끝날때 까지 호출한 함수를 대기하도록 하는것
    - 블로킹이 이루어지는 동안 하드웨어 리소스를 효율적으로 사용하지 못함
- NonBlocking
    - 호출된 함수가 제어권을 호출한 함수에게 넘겨주어 호출된 함수의 작업이 모두 종료되지 않아도 호출한 함수가 다른 작업을 할 수 있도록 하는 것
    - 업무 흐름이 복잡해짐

### 차이

- Blocking과 NonBlocking의 차이는 제어권이 어디에 있는가 이다.
- 제어권이 호출된 함수에게 있으면 Blocking 호출한 함수에게 있으면 NonBlocking 이다.

### **Synchronous VS Asynchronous**

- Synchronous
    - 메소드를 호출하면 메소드 종료시간과 결과 리턴시간이 같은 방식으로 요청을 보낸 후 응답이 올때 까지 대기
    - 요청한 작업의 완료여부와 작업 결과를 반환받는데 필요한 시간이 긴 경우 문제 발생
- Asynchronous
    - 메소드를 호출 했을 때 결과가 리턴되지 않더라도 다른 작업을 수행하는 방식으로 요청을 보낸 후 응답이 오지 않더라도 다른 작업을 진행

## 정리
### **Blocking & NonBlocking**

1. Synchronous & Asynchronous와 Blocking & Nonblocking의 차이
    1. Synchronous와 Asynchronous는 응답에 관점을 두는 방식이고 
    Blocking Nonblocking은 제어권에 중점을 두는 방식
2. Blocking와 non-blocking 차이점
    1. Blocking : 호출된 함수에게 제어권이 있어 호출된 함수가 종료될 때 까지 호출한 함수는 다른 작업을 하지 못함
    2. non-blocking : 호출한 함수에게 제어권이 있어 호출된 함수가 종료되지 않아도 다른 작업을 진행함
3. synchronous와 asynchronous의 차이점
    1. Synchronous : 함수의 종료시간과 응답시간이 같은 방식으로 응답이 올 때 까지 다른 작업을 진행하지 않음
    2. Asynchronous : 함수의 응답을 신경쓰지 않고 다른 작업을 진행하다가 응답이 오면 응답에 대한 내용을 반환

### **Blocking I/O & Non-Blocking I/O**

1. Blocking, non-blocking의 장단점
    1. Blocking의 장점 : 구현하기 쉽고 처리 순서가 정해져 있어 직관적으로 로직을 파악할 수 있음, 작업이 끝날 때 까지 기다리기 때문에 반환 결과가 보장
    2. Blocking의 단점 : 호출된 함수가 종료될 때 까지 다른 하드웨어 리소스를 사용하지 못함, 동시에 여러작업을 수행하지 못함
    3. non-blocking의 장점: 작업이 끝나지 않아도 다른 작업을 진행할 수 있음 즉 동시에 여러가지 작업이 가능
    4. non-blocking의 단점: 블로킹에 비해 코드가 복잡해지고 작업의 결과를 핸들링하는데 많은 코드량을 요구, 작업의 완료를 기다리지 않기 때문에 결과값이 정확하지 않거나 동기화 되지 않는 이슈 발생
2. non-blocking에서 결과값을 handling하는 방법
    1. Polling : 작업의 완료 여부를 주기적으로 체크
    2. Callback : 작업이 완료되었을 때 호출될 함수로 callback 함수를 설정
    3. Events : 작업이 완료되었을 때 트리거가 작동하는 event를 기다림 polling 보다 효과적
    4. Promise 객체 : promise 객체를 활용하여 작업의 상태를 확인하고 완료되면 promise 객체를 업데이트