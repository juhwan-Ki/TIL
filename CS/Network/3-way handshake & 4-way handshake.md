### 3-way handshake & 4-way handshake

- 3-Way Handshake 는 TCP의 접속,4-Way Handshake는 TCP의 접속 해제 과정

- 포트(PORT) 상태 정보
    - CLOSED: 포트가 닫힌 상태
    - LISTEN: 포트가 열린 상태로 연결 요청 대기 중
    - SYN_RCV: SYNC 요청을 받고 상대방의 응답을 기다리는 중
    - ESTABLISHED: 포트 연결 상태

- 플래그 정보
    - TCP Header에는 CONTROL BIT(플래그 비트, 6bit)가 존재하며, 각각의 bit는 "URG-ACK-PSH-RST-SYN-FIN"의 의미를 가짐
        - 즉, 해당 위치의 bit가 1이면 해당 패킷이 어떠한 내용을 담고 있는 패킷인지를 나타냄
    - SYN(Synchronize Sequence Number) / 000010
        - 연결 설정. Sequence Number를 랜덤으로 설정하여 세션을 연결하는 데 사용하며, 초기에 Sequence Number를 전송
    - ACK(Acknowledgement) / 010000
        - 응답 확인. 패킷을 받았다는 것을 의미한다.
        - Acknowledgement Number 필드가 유효한지를 나타냄
        - 양단 프로세스가 쉬지 않고 데이터를 전송한다고 가정하면 최초 연결 설정 과정에서 전송되는 첫 번째 세그먼트를 제외한 모든 세그먼트의 ACK 비트는 1로 지정된다고 생각할 수 있음
    - FIN(Finish) / 000001
        - 연결 해제. 세션 연결을 종료시킬 때 사용되며, 더 이상 전송할 데이터가 없음을 의미

### 3-way handshake

- TCP/IP 프로토콜을 이용해서 통신을 하는 응용프로그램이 데이터를 전송하기 전에 먼저 정확한 전송을 보장하기 위해 **상대방 컴퓨터와 사전에 세션을 수립하는 과정**
- 양쪽 모두 데이터를 전송할 준비가 되었다는 것을 보장하고, 실제로 데이터 전달이 시작하기 전에 한 쪽이 다른 쪽에 준비되었다는 것을 알 수 있도록 함
- 즉, TCP/IP 프로토콜을 이용해서 통신을 하는 응용 프로그램이 데이터를 전송하기 전에 먼저 정확한 전송을 보장하기 위해 상대방 컴퓨터와 사전에 세션을 수립하는 과정을 의미

### 동작 방식

- Client는 Server와 연결하기 위해 3-way handshake를 통해 연결 요청
- 일반적으로 생각하는 Client와 Server는 모두 서로 연결 요청을 먼저 할 수 있기 때문에, 연결 요청을 먼저 시도한 요청자를 Client, 연결 요청을 받은 수신자를 Server로 지칭
- **SYN(Synchronization)** : 연결요청, 세션을 설정하는데 사용되며 초기에 시퀀스 번호를 보냄
- **ACK(Acknowledgement)** : 보낸 시퀀스 번호에 TCP 계층에서의 길이 또는 양을 더한 것과 같은 값을 ACK에 포함하여 전송
    - 동기화 요청에 대한 답변 : `Client의 Sequence Number+1`을 하여 ACK로 돌려줌
- **Step 1 (SYN)**
    
    **클라이언트는 서버와 커넥션을 연결하기 위해 SYN을 보냄 (seq : x)**
    
    - 송신자가 최초로 데이터를 전송할 때 Sequence Number를 임의의 랜덤 숫자로 지정하고, SYN 플래그 비트를 1로 설정한 세그먼트를 전송
    - PORT 상태
        - Client : `CLOSED``SYN_SENT` 로 변함
        - Server : `LISTEN`
- **Step 2 (SYN + ACK)**
    
    **서버가 SYN(x)을 받고, 클라이언트로 받았다는 신호인 ACK와 SYN 패킷을 보냄 (seq : y, ACK : x + 1)**
    
    - 접속요청을 받은 Q가 요청을 수락했으며, 접속 요청 프로세스인 P도 포트를 열어달라는 메세지를 전송 (SYN-ACK signal bits set)
    - ACK Number필드를 Sequence Number + 1 로 지정하고 SYN과 ACK 플래그 비트를 1로 설정한 새그먼트 전송 (`Seq=y, Ack=x+1, SYN, ACK`)
    - PORT 상태
        - Client : `CLOSED`
        - Server : `SYN_RCV`
- **Step 3 (ACK)**
    
    **클라이언트는 서버의 응답은 ACK(x+1)와 SYN(y) 패킷을 받고, ACK(y+1)를 서버로 보냄**
    
    - 마지막으로 접속 요청 프로세스 P가 수락 확인을 보내 연결을 맺음 (ACK)
    - 이때, 전송할 데이터가 있으면 이 단계에서 데이터를 전송할 수 있음
    - PORT 상태
        - Client : `ESTABLISED`
        - Server : `SYN_RCV` ⇒ ACK ⇒ `ESTABLISED`

### **4-Way Handshake**

- 4-Way Handshake은 연결을 해제 (Connecntion Termination)하는 과정으로 FIN 플래그를 이용
- `FIN` (finish) : 세션을 종료시키는데 사용되며, 더 이상 보낸 데이터가 없음을 나타냄

### 동작원리

- **STEP1 (Client → Server : FIN(+ACK)**
    - 서버와 클라이언트가 연결된 상태에서 클라이언트가 close()를 호출하여 접속을 끊으려함
    - 이때, 클라이언트는 서버에게 연결을 종료한다는 `FIN` 플래그를 보냄
        - 이때 FIN 패킷에는 실질적으로 ACK도 포함되어있음
- **STEP2 (Server → Client : ACK)**
    - 서버는 FIN을 받고, 확인했다는 `ACK`를 클라이언트에게 보내고 자신의 통신이 끝날때까지 기다림 (이상태가 TIME_WAIT 상태)
        - Server(수신자)는 ACK Number 필드를 (Sequence Number + 1)로 지정하고, ACK 플래그 비트를 1로 설정한 세그먼트를 전송
    - 서버는 클라이언트에게 응답을 보내고 **`CLOSE_WAIT` 상태**에 들어감 그리고 **아직 남은 데이터가 있다면 마저 전송을 마친 후에 close( )를 호출**
    - 클라이언트에서는 서버에서 ACK를 받은 후에 서버가 남은 데이터 처리를 끝내고 FIN 패킷을 보낼 때까지 기다리게 됨 (**`FIN_WAIT_2`)**
- **STEP3 (Server → Client : FIN)**
    - 데이터를 모두 보냈다면, 서버는 연결이 종료에 합의 한다는 의미로 `FIN` 패킷을 클라이언트에게 보낸 후에, 승인 번호를 보내줄 때까지 기다니는 `LAST_ACK` 상태로 들어감
- **STEP4 (Client → Server : ACK)**
    - 클라이언트는 FIN을 받고, 확인했다는 `ACK`를 서버에게 보냄
    - 아직 서버로부터 받지 못한 데이터가 있을 수 있으므로 `TIME_WAI`T을 통해 기다림 (실질적인 종료과정 `CLOSED`에 들어가게 됨)
        - 이때 `TIME_WAIT` 상태는 **의도치 않은 에러로 인해 연결이 데드락으로 빠지는 것을 방지**
        - 만약 에러로 인해 종료가 지연되다가 타임이 초과되면 `CLOSED`로 들어감
- 서버는 ACK를 받은 이후 소켓을 닫음 (Closed)
- TIME_WAIT 시간이 끝나면 클라이언트도 닫음 (Closed)
