## 1. 장치의 설치 및 관리
### 1-1. 장치의 설치 및 관리
- /proc 디렉터리 내 주요 파일 
    - `/proc/partitions` : 현재 시스템의 파티션 정보를 저장 
    - `/proc/cpuinfo` : CPU 정보를 저장
    - `/proc/devices` : 현재 시스템이 사용하는 디바이스 정보를 저장
    - `/proc/filesystems` : 커널이 지원하는 파일 시스템의 정보를 저장 
    - `/proc/meminfo` : 시스템의 메모리 정보를 저장 
    - `/proc/swaps` : 스왑 공간 관련 정보를 저장
    - `/proc/mdstat` : RAID나 중복 디스크에 대한 정보를 저장
- /dev 디렉터리 주요 장치 파일 
    - `/dev/lp*` : 프린터 장치 
    - `/dev/hd*` : IDE 드라이버, ATA 드라이버, …
    - `/dev/sd*` : SCSI 드라이버, SATA 드라이버, USB 드라이버, …
    - `/dev/tty*` : 터미널 장치
- 커널 컴파일 
    - 커널(kernel)
        - 운영체제의 가장 바깥 부분에 위치하여 사용자 명령어에 대한 처리를 담당
    - 커널 컴파일
        - 커널 소스를 사용자가 자신에 맞는 커널 환경을 만드는 과정
- 커널 컴파일 과정과 명령어 
    1. 커널 소스 설치 
        - 커널 소스를 /usr/src 디렉터리에 다운로드 후 압축해제 (보통 /usr/src/kernels에 설치됨)
        - 커널 패치 수행
    2. 커널 컴파일 준비(초기화) `(make mrproper 명령어)` 
        - 기존에 설정되어 있는 커널 설정 값을 모두 초기화하는 명령어
        - 컴파일 되어 있는 오브젝트 파일(.o 확장자)이 제거
    3. 커널 컴파일을 위한 환경 설정 
        - 필요 없는 드라이버/모듈을 제외하거나 포함하는 단계
        - `make config` 명령어 : 전통적인 방식
        - `make menuconfig` 명령어 : 메뉴 방식의 화면으로 설정 
        - `make xconfig` 명령어 : X 윈도 환경에서 구현한 방식 인터페이스가 편리 
    4. 커널 컴파일
        - `make dep` 명령어: 새로운 커널을 만드는 명령어 (커널 2.6 버전 이후로는 사용 X)
        - `make clean` 명령어 : 이전에 수행했던 컴파일 과정에서 생성된 목적 파일, 커널, 임시 파일 등을 삭제
        - `make distclean` 명령어 : 커널 소스를 받은 최초 상태로 되돌리는 명령어 
        - `make bzlmage` 명령어 : 압축된 커널 이미지를 생성
        - `make modules` 명령어 : 커널 환경 설정에서 모듈로 설정한 기능들을 컴파일 
        - `make modules_install` 명령어 : 컴파일된 모듈을 /lib/modules에 설치
    5. 커널 설치 (make install 명령어) 
        - 생성한 커널 이미지를 설치
        - /boot 디렉터리에 필요한 파일을 복사하는 과정
- 순서 정리
    - 커널 소스 설치 → make mrproper → make config → make clean → make bzlmage → make  modules → make modules_install → make install 
    - make help 명령어 : 커널 컴파일에 필요한 명령어들을 확인
- 커널 모듈 설정 명령어/파일 
    - 커널 모듈의 설치 위치는 /lib/modules/[커널 버전]/*  
- lsmod 명령어 
    - 현재 로드된 커널 모듈을 확인하는 데 사용
    - `/proc/modules` 파일 참조
- insmod 명령어(insertModule)
    - 모듈을 커널에 설치하는 명령어
    - 단순한 모듈 파일 설치에 사용
    - 모듈에 필요한 의존성을 자동으로 해결하지 않으므로, 의존성이 있는 경우 `modprobe`를 사용하는 것이 좋음
    - 형식
        - `insmod [옵션] <설치할 모듈(오브젝트 파일)>`
- rmmod 명령어
    - 모듈을 삭제
    - `rmmod`는 특정 커널 모듈을 강제로 제거 (의존성 고려 X)
    - `modprobe -r` 은 의존성을 해결하면서 안전하게 모듈 제거
    - 형식
        - `rmmod <모듈명>`
- modprobe 명령어 
    - 커널 모듈을 로드(`insmod` 역할) 및 언로드(`rmmod` 역할)할 때 사용하는 명령어
    - insmod/rmmod 명령어와 다르게 modules.dep 파일을 참조해 의존성 문제를 해결하기 때문에 더 안전하고 권장되는 방법
    - 특정 모듈이 자동으로 로드되지 않도록 하려면 `/etc/modprobe.d/blacklist.conf` 파일에 추가
    - 형식
        - `modprobe <모듈명>`
    - 옵션
        - `[-l]` : 모든 모듈 목록 출력
        - `[-a]` : 모듈을 설치 의존성이 있는 모듈을 함께 설치
        - `[-r]` : 모듈을 삭제 의존성이 있는 모듈 중 사용되고 있지 않는 모듈을 같이 삭제
- depmod 명령어 
    - 커널 모듈 간 의존성을 분석하고 `modules.dep` 파일을 생성하는 명령어
    - 명령어를 실행하면 `/lib/modules/$(uname -r)/modules.dep` 파일이 생성되거나 업데이트됨
    - 형식
        - `sudo depmod`
    - 옵션
        - `[-a]` : /etc/modules.conf 파일에 있는 모든 모듈의 의존성을 갱신
        - `[-v]` : 처리된 모듈의 목록을 출력
- modinfo 명령어 
    - 지정한 모듈의 정보를 출력하는 명령어
    - 모듈의 **파일 경로, 버전, 의존성, 설명, 라이선스, 작성자** 등의 정보를 출력
- /etc/modprobe.d 디렉터리 
    - 부팅 시 모듈을 자동으로 적재하기 위한 디렉터리
    - 디렉터리 안에 있는 .conf 파일을 읽어 부팅 시에 자동으로 적재
    - 커널 2.4 버전 이전에는 /etc/modprobe.conf 파일을 읽어 자동으로 적재했음 
- /lib/modules/[커널 버전]/modules.dep 파일 
    - 모듈의 의존성이 정의되어 있는 파일
    - 파일 형식
        - `[모듈 이름] : [의존성이 있는 모듈 이름] [의존성이 있는 모듈 이름] …`

### 1-2. 주변 장치 설정

- 프린터
    - 오픈 소스 프린팅 시스템 (CUPS) 
        - 애플에서 개발한 오픈 소스 프린팅 시스템
        - 컴퓨터를 프린터 서버로 사용할 수 있게 해주는 모듈 방식의 프린팅 시스템
- /etc/cups/printers.conf 파일
    - 프린터 큐 관련 환경 설정 파일
    - lpadmin 명령어를 이용하거나 웹을 통해 설정 
- 인터넷 인쇄 프로토콜(IPP) 포트 번호 : TCP,UDP/631 
- 프린터 관련 명령어 
    - 기본적으로 [-P] 옵션으로 프린터 지정을 하지 않으면 기본 프린터를 기본값으로 명령 실행
    - /etc/printcap 파일 : 프린터 설정 파일
    - lpc 명령어 : 프린터의 상태를 확인하고, 시작/정지, 큐 정리 등의 작업을 수행하는 명령어
    - lpq 명령어 : 프린터 대기열(큐)에 있는 작업 목록을 확인하는 명령어
        - System V 계열 : lpstat 명령어
    - lprm 명령어 : 프린터 큐의 작업 삭제
        - System V 계열 : cancel 명령어
- lpr 명령어 : 파일 내용을 출력
    - System V 계열 : lp 명령어
- [-# NUM] : 한 페이지를 “NUM”장씩 출력 ([-K] 옵션과 동일)
    - System V 계열 : [-n NUM] 옵션
    - [-b] : 배너나 헤더를 출력하지 않음
    - 다른 방법을 통한 출력 방법 :  cat [출력할 파일] > /dev/lp
- 스캐너
    - SANE 
        - 스캐너 관련 하드웨어를 사용할 수 있도록 해주는 API
        - 관련 소프트웨어
            - Xsane(X 윈도 기반), Simple Scan(GNOME), GIMP(GNU, 그림 편집 프로그램) 
- 사운드 카드 
    - ALSA(Advanced Linux Sound Architecture) 
    - 사운드 카드용 장치 드라이버를 위한 API를 제공
    - 리눅스 커널의 구성요소. GPL 기반
        - 관련 소프트웨어 : alsamixer 
    - OSS(Open Sound System)
        - 사운드를 만들고 캡처하는 인터페이스
        - 초기에는 OSS를 사용했으나 최근에는 ALSA가 더 많이 사용됨
- 하드웨어 관련 명령어
    - lscpu 명령어 : CPU 정보 출력
    - lspci 명령어 : 시스템 하드웨어 정보 출력 
    - lsscsi 명령어 : SCSI 장치의 목록 출력
    - lsusb 명령어 : usb 장치의 목록 출력
