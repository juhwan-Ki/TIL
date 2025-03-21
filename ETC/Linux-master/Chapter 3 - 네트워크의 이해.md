## 1. 네트워크 기초

### 1-1. OSI 7계층

- OSI 7계층 요약 
    
    | 계층 | 특징 | 데이터 종류 | 프로토콜/서비스 | 장비 |
    | --- | --- | --- | --- | --- |
    | 응용 계층 | 각종 응용서비스 제공 | 메시지 | HTTP, FTP, SMTP |  |
    | 표현 계층 | 압축 및 암호화 | 메시지 | ASCII, jpg |  |
    | 세션 계층 | 동기화,세션 연결/관리/종료 | 메시지 | 전송모드 결정 |  |
    | 전송 계층 | 데이터 전송 보장
    흐름 제어 | 세그먼트 | TCP, UDP | 게이트웨이 |
    | 네트워크 계층 | 라우팅 | 패킷 | IP, ICMP, IGMP, ARP, RARP | 라우터 |
    | 데이터 링크 계층 | 오류제어
    MAC 주소 | 프레임 | PPP | 브릿지, 스위치 |
    | 물리 계층 | 물리적 연결 | 비트 |  | 허브, 리피 |

### 1-2. 네트워크 장비

- 허브(Hub)
    - 물리 계층에서 동작하는 장비
    - 더미 허브(Dummy Hub)
        - 일반적인 허브
        - 허브로 들어온 데이터를 모든 포트로 뿌려주는 역할
        - 충돌이 발생할 확률이 높고, 연결된 노드가 많을수록 속도가 저하됨
    - 스위칭 허브(Switching Hub)
        - 허브에 스위칭 개념을 도입한 장비
        - 허브로 들어온 데이터를 해당 목적지에 해당하는 포트로 전송함
        - MAC 주소를 이용하기 때문에 2계층 장비로 분류되고 충돌 도메인을 나누는 역할을
    - 리피터(Repeater) 
        - 약해진 신호를 수신하여 원래의 형태로 재생하고 증폭하는 장비
        - 최근에는 모든 네트워크 장비에 기본적으로 들어가 있는 기능
- 브리지(Bridge)
    - 데이터링크 계층인 MAC에서 동작하는 장비
    - 네트워크를 확장시키고 통신을 격리 시키기 위해 사용
    - 충돌 도메인(Collision Domain)을 나누는 역할을 함
- 라우터(Router)
    - 3계층 장비로 물리 / 데이터 링크 / 네트워크 계층에서 동작
    - 라우터는 각 인터페이스마다 MAC 주소와 IP주소를 갖음
    - 브로드캐스트 도메인(Broadcast Domain)을 나누는 역할을 함
- 게이트웨이(Gateway)
    - 서로 다른 프로토콜을 사용하는 네트워크를 상호 접속하기 위한 장비
    - 4계층 장비로 분류

### 1-4. TCP/IP 및 네트워크 프로토콜의 이해

- 프로토콜(Protocol)과 포트(Port) 
    - 프로토콜 
        - 컴퓨터 간의 통신을 위한 규격
        - /etc/protocols 에서 주요 프로토콜 번호를 확인할 수 있음
    - 포트 번호 
        - 포트 : TCP와 UDP에서 어플리케이션의 상호 통신을 사용
        - OSI 7계층의 4계층(전송계층)에서 사용되는 논리적인 주소
    - 0~65535번의 범위(2^16) 중에서 0~1023번 까지는 특정 프로토콜이 지정되어 있으며, 잘 알려진 포트(well-known port)라고 함
    - /etc/services 에서 주요 포트 번호를 확인할 수 있음
    - 주요 포트
        
        
        | 시스템 | 포트 | 설명 |
        | --- | --- | --- |
        | FTP | TCP/20
        TCP/21 | 데이터 포트(20), 제어 포트(21) |
        | SSH | TCP/22 | 암호화된 원격 터미널 접속 프로토콜 |
        | SFTP | TCP/22 | SSH를 이용한 암호화된 FTP 프로토콜 |
        | TELNET | TCP/23 | 암호화를 하지 않는 원격 터미널 접속 프로토콜 |
        | SMTP | TCP/25 | 메일 전송 프로토콜 |
        | HTTP | TCP/80 | Hyper-Text Transfer Protocol |
        | HTTPS | TCP/443 | 암호화된 HTTP 프로토콜 |
        | POP3 | TCP/110 | 메일 수신용 프로토콜 |
        | IMAP4 | TCP/143 | 메일 수신용 프로토콜 |
        | SMB | TCP/445 | 서버 메시지 블록 프로토콜 |
        | DNS | UDP/53
        TCP/53 | 도메인에 대한 호스트 정보 제공하는 프로토콜 |
        | DHCP | UDP/67
        UDP/68 | 동적 호스트 네트워크 설정 프로토콜
        서버(67) / 클라이언트(68) |
        | TFTP | UDP/69 | 단순 파일 송수신 프로토콜 |
        | NetBIOS | TCP/135~139
        UDP/135~139 | 별개의 컴퓨터상에 있는 어플리케이션들이 LAN내에서 통신할 수 있게 해주는 프로그램 |
        | SNMP | UDP/161
        UDP/162 | 네트워크 관리 프로토콜
        서버(161) / 클라이언트(162) |
- IP
    - IP 주소 : OSI 7계층의 3계층(네트워크 계층)에서 사용되는 논리적인 주소
    - 32bits의 길이를 가지며, 네트워크 ID와 호스트 ID로 구성
    - 클래스
        - A Class
            - Network(8bit) Host(24bit)
            - IP주소를 32자리 2진수로 표현했을때, 맨 비트 패턴이 항상 0
            - 1.0.0.0 ~ 126.255.255.255 까지(127은 제외)
            - 가장 많은 호스트를 가질 수 있음
        - B Class
            - Network(16bit) Host(16bit)
            - IP주소를 32자리 2진수로 표현했을때, 맨 비트  패턴이 10
            - 128.0.0.0 ~ 191.255.255.255 까지
            - 중간 규모 네트워크에서 사
        - C Class
            - Network(24bit) Host(8bit)
            - IP주소를 32자리 2진수로 표현했을때, 맨 비트 패턴이 항상 100
            - 192.0.0.0 ~ 223.255.255.255 까지
            - 소규모 네트워크에 사용
- 예약된 주소 
    - 0.0.0.0/32 : 현재 네트워크를 뜻하는 주소로, 자신의 IP 주소를 모를 때 사용
    - 10.0.0.0/8 : A 클래스의 사설 주소
    - 127.0.0.0/8 : 루프백(loopback) 주소
    - 172.16.0.0/12 : B 클래스의 사설 주소
    - 192.168.0.0/16 : C 클래스의 사설 주소
    - 255.255.255.255/32 : 브로드캐스트 주소 같은 네트워크 상의 모든 장치에게 패킷을 전송할 때 사용
- 서브네팅(Subnetting) 
    - IPv4 주소의 고갈로 인해 하나의 네트워크를 여러 개의 서브 네트워크로 나누어 낭비를 막기 위한 방법
    - 서브넷 마스크(Subnet mask) : IP 주소를 네트워크 주소와 호스트 주소로 구분하기 위한 주소
- IPv6 
    - IPv4 주소의 고갈로 더 큰 주소 길이를 갖는 IPv6 주소 체계가 등장
    - 크게 확장된 주소 공간, 향상된 서비스, 보안성
- ARP 프로토콜 
    - IP 주소를 이용해 해당 MAC 주소를 요청하는 프로토콜
    - 보통은 3계층 프로토콜로 분류 (2계층으로도 분류되는 경우도 있음)
    - RARP : MAC 주소를 이용해 IP 주소를 요청하는 프로토콜

## 2. 네트워크 설정

### 2-1. 환경 설정

- IP 주소 설정
    - LAN 카드를 OS에 인식시킨 후 IP를 설정해야 함
- ifconfig 명령어 
    - 형식
        - ifconfig [인터페이스 명] [옵션]
    - 주요 옵션
        - `[-a]` : 시스템의 전체 인터페이스에 대한 정보를 출력
    - 옵션 없이 사용 시 현재 설치된 네트워크 인터페이스의 설정 내용을 출력
    - IP 주소, 서브넷 마스크, 브로드캐스트 주소, MAC 주소, MTU, RX/TX 패킷 등의 정보를 알 수 있음
    - ex) IP 설정 예제
        - ifconfig eth0 192.168.1.33 netmask 255.255.255.0 → eth0 인터페이스에 IP, 넷마스크 설정
        - ifconfig eth0 up → eth0 인터페이스 활성화
- ip 명령어 
    - 형식
        - ip [옵션] OBJECT { COMMAND | help }
    - 오브젝트
        - `link(l)` : 네트워크 인터페이스를 표시하고 수정
        - `address(a)` : IP 주소를 표시하고 수정
        - `route(r)` : 라우팅 테이블을 표시하고 변경
        - `Neigh(n)` : 인접 개체를 표시하고 조작(ARP 테이블)
    - 주요 옵션
        - `[addr]` : 네트워크 인터페이스에 대한 정보를 출력(show) / 추가(add) / 삭제(del)
            - ex) ip addr show → 인터페이스 정보 출력
        - `[route]` : 라우팅 테이블 출력(show) / 정적 라우팅 추가(add) / 삭제(del)
        - `[link]` : 네트워크 인터페이스 상태 출력(show) / 관리(set)
- netstat 명령어 
    - 네트워크 상태 정보를 출력하는 명령어
    - 주요 옵션
        - `[-a]` : 모든 소켓 정보 출력
        - `[-r]` : 라우팅 정보 출력
        - `[-n]` : 호스트 이름을 IP 주소로 출력
        - `[-l]` : LISTEN 상태인 소켓 정보만 출력
        - `[-p]` : 해당 소켓과 관련된 프로세스의 이름과 PID 출력
        - `[-t]` : TCP 프로토콜만 출력
        - `[-u]` : UDP 프로토콜만 출력
- 네트워크 상태 진단 명령어
    - traceroute 명령어 : 목적지까지 패킷이 지나가는 경로를 출력 
    - ping 명령어 : 목적지에 ICMP 패킷(echo_request)을 보내 상태를 점검
- DNS 관련 명령어
    - nslookup 명령어 : DNS 서버와 질의/응답하는 명령어
    - dig 명령어 : nslookup 명령어와 비슷하지만 더 간결하고 사용하기 편리
- 관련 파일 
    - /etc/resolv.conf 파일 : 네임서버가 정의되어 있는 파일
    - /etc/hosts 파일 : 도메인/호스트명과 IP 주소 매핑 정보를 저장하고 있는 파일
- route 명령어
    - 라우팅 테이블을 출력/수정 하는 명령어
        - 주요 옵션
            - `[add]` : 라우팅 경로나 디폴트 게이트웨이를 추가
            - `[del]` : 라우팅 경로나 디폴트 게이트웨이를 삭제
            - `[-n]` : 호스트 이름을 IP 주소로 출력
            - 옵션 없이 사용시 라우팅 테이블을 출력
- nmcli 명령어
    - 네트워크 관리자(Network Manager) 서비스를 커맨드 라인에서 수행하는 명령어
    - 주요 옵션
        - [con show] : 네트워크의 모든 연결에 대한 정보를 출력
        - [con add] : 네트워크 연결 설정 추가
            - ex) nmcli con add con-name ens33 ifname test-net type eth ip4 192.168.1.33/24 gw4 192.168.1.1
        - [con del] : 네트워크 연결 설정 제거
        - [con up] : 네트워크 연결 활성화
        - [con mod] : 네트워크 연결 설정 수정
