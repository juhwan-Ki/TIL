## 1. 운영체제의 개요

### 1-1. 운영체제의 이해
- 운영체제의 주요 역할
    - HW 제어, 사용자들 간에 HW 자원을 공유
    - 스케줄링을 통해 자원의 효율적 사용
    - I/O 용이
    - 오류 방지/복구
    - 편리한 사용자 인터페이스 제공
- 운영체제 유형 
    - 다중 교환(Multi-switching) : 다수의 작업이 동시 실행 포어그라운드 프로그램만 동작
    - 단일 작업(Single-tasking) : 한 번에 하나의 작업만 처리
    - 다중 작업(Multi-tasking) : 여러 개의 작업을 동시에 수행
    - 다중 사용자(Multi-user) : 단일 프로세서에서 여러 사용자의 프로그램이 실행
    - 대화형 처리(Interactive Processing) : 대화형으로 작업을 처리 (시분할 처리 기능 필요)
    - 일괄 처리(Batch Processing) : 여러 개의 작업을 묶어 한 번에 처리
    - 실시간 처리(Real Time Processing) : 작업의 처리가 지연 없이 즉각적으로 처리
    - 분산 처리(Distributed Processing) : 여러 시스템을 연결하여 작업을 나누어 처리
- 시스템 성능을 나타내는 4가지 요소 
    - 처리 능력(Throughput) : 단위 시간당 처리 능력
    - 반환 시간(Turnaround Time) : 작업이 제출되어 결과를 얻을 때까지의 총 소요시간
    - 신뢰도(Reliability) : 시스템이 얼마나 정확하게 작동하는지를 나타냄
    - 사용 가능도(Availability) : 시스템에서 곧 사용할 수 있는 정도를 나타냄
- 가상 메모리 
    - 하드디스크의 일부를 메모리(RAM)처럼 사용하는 것
    - 하드디스크에서 가상 메모리로 쓰이는 영역을 스왑 영역(swap space)이라고 함
    - 스와핑(Swapping) : 메모리와 하드디스크 사이의 데이터 교환
- 페이지 교체 알고리즘 
    - FIFO(First-In First-Out) : 먼저 적재된 페이지를 제거하는 알고리즘
    - LRU(Least Recently Used) : 가장 오랫동안 참조되지 않은 페이지를 제거하는 알고리즘
    - LFU(Least Frequently Used) : 최근에 가장 적게 사용된(참조된) 페이지를 제거하는 알고리즘
    - NUR(Not Used Recently) : 참조 비트와 변형 비트를 사용하여 가장 오랫동안 참조되지 않은 페이지를 제거하는 알고리즘(LRU 알고리즘의 변형)
    - SCR(Second Chance Replacement) : FIFO 알고리즘의 단점을 보완한 알고리즘
    참조 비트로 한번의 기회를 더 부여하는 알고리즘

### 1-2. 운영체제의 종류
- 리눅스의 특징 
    - CPU 최적화 : 인텔 CPU 뿐만 아니라 AMD와 Cyrix CPU에도 최적화되어 있음
    - 뛰어난 네트워킹 : TCP/IP 뿐만 아니라 IPX/SPX, SLIP, PPP 등의 여러 네트워킹 프로토콜을 지원
    - 뛰어난 이식성/확장성 : 다른 OS에 비해 이식성, 확장성이 뛰어남
    - 6개의 가상 콘솔 제공 : 하나의 모니터를 장착한 시스템에서 기본적으로 6개의 가상 콘솔을 제공
    - 효율적인 가상메모리

## 2. 리눅스 기초

### 2-1. 리눅스의 철학

- GNU 프로젝트 
    - GNU : Gnu is Not Unix 라는 뜻의 재귀적 약어
    - 유닉스 호환 자유 소프트웨어들을 개발하는 프로젝트
    - GNU 소프트웨어에는 gcc, emacs, Bash 쉘, GNOME 등이 있다.
- GPL(General Public License) 
    - FSF에 의해 만들어진 특별한 오픈소스 라이선스. “카피레프트(copyleft)”
    - FSF(Free Software Foundation, 자유 소프트웨어 재단) 2016(2)
    - 리차드 스톨만이 설립한 비영리 조직
    - 해당 프로그램을 마음대로 배포, 복사, 소스코드 수정이 가능
    - GPL 프로그램의 소스코드를 수정해 만든 프로그램 또한 GPL을 가짐
    - 다른 소프트웨어와의 완전한 통합은 해당 소프트웨어가 GPL을 수용한다는 조건하에서만 허용  (독점 소프트웨어와 결합이 불가능)
    - 독점 소프트웨어와 결합 시 해당 영역의 소스를 공개해야 함
- LGPL(Lesser General Public License) 
    - GPL의 조건을 완화시킨 라이선스 (GPL 특징 중 전염성을 완화)
    - LGPL 프로그램의 소스코드를 이용하여 만든 프로그램을 판매/배포 시 프로그램 소스 공개 의무가 없고 LGPL 소스코드를 사용했음을 명시하면 됨하지만 수정한 경우는 전체 코드를 공개해야 함
    - 독점 소프트웨어와 결합이 가능하다
- BSD(Berkeley Software Distribution) 라이선스
    - BSD 계열 소프트웨어에 적용되는 오픈소스 라이선스
    - 소스코드 공개의 의무가 없음
    - 저작권자 표기 BSD 라이선스의 소프트웨어를 이용하는 경우, 해당 저작권자의 이름과 BSD 라이센스의 내용을 같이 배포해야 함
    - 독점 소프트웨어와 결합 시 원래 소스의 저작권자와 관련 사항을 표기하면 소스 공개 의무는 없음
- Apache 라이센스
    - 아파치 소프트웨어에서 만든 소프트웨어 라이선스
    - 누구나 자유롭게 아파치 소프트웨어의 소스코드를 다운 받아 부분 혹은 전체를 개인적 혹은 상업적 목적으로 이용할 수 있음
    - 재배포 시에는 원본 소스코드 혹은 수정한 소스코드를 반드시 포함할 필요는 없으며 아파치 라이선스를 따른다는 사실만 명시하면 됨
- MPL(Mozila Public License) 라이선스
    - BSD와 GPL의 혼합적인 성격을 띄고 있음
    - MPL 소스코드 수정 시 수정한 소스코드를 공개해야 함
    - MPL 소스코드와 혼합하여 다른 프로그램을 개발한 경우 MPL 소스코드 외의 소스코드는 공개할 필요 없음
- MIT 라이선스
    - 라이선스 및 저작권 명시만 하면 됨
- GNU/FSF에서 규정한 자유 소프트웨어 조건 
    - 프로그램을 어떠한 목적으로도 실행할 있는 자유
    - 프로그램의 작동 원리를 연구하고, 이를 자신의 필요에 맞게 수정할 수 있는 자유
    - 이웃을 돕기 위해서 프로그램을 복제하고 배포할 수 있는 자유
    - 프로그램을 향상시키고 이를 공동체 전체의 이익을 위해서 다시 환원시킬 수 있는 자

### 2-2. 리눅스의 역사

- 리눅스 배포판의 종류
    - 레드햇(RedHat)
        - RPM 기반으로 제작된 리눅스 배포판
        - 현재는 기업용 배포판으로 상업적으로 배포하고 있으며, 무료 버전으로는 페도라가 있음
        - 종류
            - RHEL : 유료 배포판
            - CentOS : RHEL을 완벽하게 호환하는 무료 배포판
            - Rocky : CentOS 종료에 따라 대안으로 등장한 무료 배포판
            - Fedora
            - Oracle Linux : 오라클의 어플라이스에 최적화된 리눅스 배포판
    - 데비안(Debian)
        - GNU의 후원을 받는 리눅스 배포판
        - 패키지 설치 및 업그레이드가 단순 (패키지 관리 – dpkg, apt)
        - 종류
            - Ubuntu : 데비안 계열로 영국 회사인 캐노니컬의 지원을 받음, 사용자 편의성에 초점을 맞춤
            - Raspbia
            - ChromeOS
    - 슬랙웨어(Slackware)
        - 초창기에 나온 배포판으로 현재까지 살아있는 가장 오래된 배포판
        - 구조가 간결하지만 설치과정이 어렵고 패키지 관리가 어려워 많이 사용되지는 않음
        - 종류
            - Slackware : 가장 먼저 대중화된 현존하는 가능 오래된 배포판
            - openSUSE : 독일에서 제작, 유료 라이센스인 SUSE Linix와 무료인 openSUSE로 나뉨
            - Vector Linux
    - 우리나라의 리눅스 배포판 
        - SULinux, 안녕 리눅스, 아시아눅스 등
- 리눅스 커널이 사용된 모바일 OS 
    - 안드로이드(Android) 
        - C/C++ 라이브러리들을 포함하는 오픈소스 플랫폼
        - 가상머신은 Java 가상머신이 아니라 구글에서 자체 개발한 달빅(Dalvik) 가상머신을 사용
    - 바다(Bada) OS 
        - 삼성에서 개발한 리눅스 커널 기반의 OS
        - 미고(MeeGo)와 리모(LiMo)가 통합된 타이젠(Tizen)과 통합
    - 타이젠(Tizen) OS 
    - 삼성과 인텔의 주축으로 개발된 오픈소스 OS
    - HTML5 기반으로 만들어졌으며, 자바스크립트, CSS와 같은 웹 표준을 지원
- 리눅스 클러스터
    - 고계산용 클러스터(High Performance Cluster) : 고성능 계산 능력을 제공하기 위한 목적으로 사용
    - 부하분산 클러스터(Linux Virtual Server) : 로드 밸런서를 운영하여 대규모 트래픽을 여러 대의 서버로 분산하는 기술로 웹 서비스에서 널리 사용
    - 고가용성 클러스터(High Availability Cluster) : 웹서비스를 제공하는 서버가 오류로 인해 정상 서비스를  하지 못하면 백업 노드에서 서비스를 이어 받아 서비스를 지속 가능하게 하는 기술
