## 1. 웹 관련 서비스

### 1-1. 웹 관련 서비스의 이해

- 웹(Web) 
    - HTML(HyperText Markup Language) : 웹 페이지를 만들기 위한 마크업 언어
    - HTTP 프로토콜이 사용되고 W3C에서 주관하여 개발
- 주요 웹 브라우저 종류
    - chromium
        - 구글 크롬으로 더 잘 알려져 있는 브라우저
        - 속도가 빠르고 다양한 플러그인이 존재
    - FireFox(파이어폭스)
        - 모질라(Mozilla) 재단에서 개발한 게코 엔진 기반의 오픈 소스 웹 브라우저 
    - Opera(오페라)
        - 노르웨이에서 개발된 블링크 엔진 기반의 웹 브라우저
    - Safari(사파리)
        - 애플에서 개발한 Webkit 엔진 기반의 웹 브라우저
        - IOS를 위한 브라우저
- HTTP(HyperText Transfer Protocol)
    - 웹에서 웹페이지를 가져오기 위한 프로토콜
    - 요청 메소드(method) 
        - GET 방식 : 요청 데이터에 대한 인수를 URL에 포함시켜 전송하는 방식
        - POST 방식 : HTTP Body 영역에 소켓을 이용하여 전송하는 방식
- 상태 코드 
    
    
    | 코드 | 상태 메세 | 설명 |
    | --- | --- | --- |
    | 200  | OK  | 요청 정상 처리 |
    | 301 | Moved Permanently | 요청된 정보의 위치가 영구적으로 변경됨을 알림 |
    | 302 | Found | 요청된 정보의 위치가 일시적으로 변경됨을 알림 |
    | 400 | Bad Request | 잘못된 요청으로 처리 불가 |
    | 403 | Forbidden | 접근 권한이 없음 |
    | 404 | Not Found | 요청한 페이지 없음 |
    | 500 | Internal Server Error | 내부 서버 오류 |

### 1-2. 웹 관련 서비스의 운영

- 웹 서버 종류 
    - Apache, IIS, Nginx 등이 있으며, 그 중 Apache가 가장 많이 쓰이고 있음
- Nginx  
    - 비동기 이벤트 기반 구조
    - 웹 서버, 리버스 프록시 및 메일 프록시 기능을 지원
- Apache (아파치) 
    - 오픈 소스 소프트웨어 그룹인 아파치 소프트웨어 재단에서 개발하였고, 현재 가장 많이 쓰이고 있는 웹 서버
    - 리눅스, 유닉스 계열 뿐만 아니라 윈도우에서도 운용 가능
- Apache 웹 서버
    - 설치 방식 
        - DSO(Dynamic Shared Object) : 처음 컴파일 이후 모듈 추가 시 다시 컴파일할 필요 없음
            - /usr/local/apache/modules 디렉터리에 모듈이 생성 
        - SO(Static Object) : 모듈 추가 시 새로 컴파일 해주어야 함
    - MPM(Multi-Processing Module) 
        - 아파치가 받아들인 요청을 처리하기 위해 자식 프로세스에게 분배하는 방식
        - Perfork 방식과 Worker 방식이 존재
            - Prework 방식 : 하나의 자식 프로세스 당 하나의 쓰레드를 사용하는 방법
            - Worker 방식 : 하나의 자식 프로세스 당 여러 개의 쓰레드를 사용하는 방법
    - 소스 파일로 설치 시 configure 옵션 
        - `[--prefix=DIRECTORY]` : DIRECTORY에 지정한 디렉터리에 아파치를 설치한다
        - `[--enable-mods-shared=MODULE_LIST]` : 지정 모듈을 동적 모듈로 컴파일
        - `[--enable-so]` : DSO 기능 활성화
- 서버 환경 설정 (httpd.conf) 
    
    
    | 구성 요소 | 설명 |
    | --- | --- |
    | ServerRoot | 아파치 Root 홈 디렉터리 경로로 절대 경로를 사용해야함
    설치시 기본적으로 /etc/httpd 경로로 지정  |
    | Listen | Apache 웹 서버 포트 기본적으로 80 포트로 설정됨 |
    | Include | httpd.conf 파일이 아닌 다른 설정 파일을 포함하여 적용 |
    | ServerAdmin | 서버 오류 발생 시 클라이언트로 전송할 오류 메시지에 보여질 관리자 이메일 주소
    에러 발생시 에러 화면에 해당 이메일 주소가 표시 |
    | ServerName | 서버의 도메인으로 클라이언트에게 보여줄 호스트 이름 및 포트를 지정
    DNS 주소가 등록되지 않으면 IP주소로 설정 |
    | MaxClients | 동시 연결 가능한 최대 클라이언트 수 |
    | KeepAlive | 클라이언트와 연결된 작업 프로세스가 계속해서 요청을 처리할지(On), 
    새로운 프로세스가 처리할지(Off) 설정  |
    | MaxKeepAlive
    Request | KeepAlive On 일 경우, 요청 작업의 최대 개수  |
    | DocumentRoot | 아파치 서버의 웹문서가 있는 경로 및 웹 페이지의 루트를 지정
    DocumentRoot 라인 이후에 <Directory> 지시자가 작성
    <Directory> : 특정 디렉터리를 제어할 때 사용 |
    
    ```bash
    <Directory {디렉터리 명}>
        [Options]
        [AllowOverride]
        [Require]
        ...
    </Directory>
    
    # options : 특정 디렉터리 하위의 모든 디렉터리와 파일에 대한 접근 권한을 제어 
    # AllowOverride : 디렉터리에 .htaccess 파일이 있을 경우 기존 설정을 덮어쓸지 여부를 설정
    # Require : 해당 디렉터리의 접근 허용 여부 설정
    ```
    
- 가상 호스트 환경 설정 파일 (httpd-vhosts.conf) 
    - 하나의 IP 주소에 여러 도메인을 사용하는 경우에 설정
- htpasswd 명령어 
    - Apache HTTP 서버에서 기본 인증을 설정할 때 사용하는 비밀번호 파일 관리 도구
    - 특정 페이지를 제한하기 위해 인증을 요구하고 그 인증에 대한 관리
    - 제한하고 싶은 아파치 내 디렉터리로 이동하여 .htaccess 파일을 만들어야 함
    - 형식
        - `htpasswd [옵션] 비밀번호파일 사용자명`
    - 주요 옵션
        - `[-c]` : 지정한 디렉터리(페이지)에 사용자 계정 및 패스워드를 생성
        - `[-D]` : 지정한 디렉터리(페이지)에 생성한 사용자 계정을 삭제
        - ex) htpasswd -c /var/www/html/.htpasswd admin 
        → /var/www/html/ 디렉터리에 대한 admin 계정, 패스워드를 생성, 처음 추가 이후에는 [-c] 옵션 없이 추가
- apachectl 명령어 
    - Apache HTTP Server의 관리 도구로, 서버의 시작 중지 재시작 상태 확인 설정파일 테스트 등 다양한 기능을 제공
    - 형식
        - `apachectl [옵션]`
    - 옵션
    - `[start]` : 아파치 서버 시작
    - `[stop]` : 아파치 서버 중지
    - `[restart]` : 아파치 서버 재시작
    - `[graceful]` : 현재 연결을 끊지 않은 상태로 재시작 (설정을 변경한 경우에 사용)
    - `[status]` : 서버 상태 확인 (mod_status 설정 필요)
- APM
    - Apache, PHP, MySQL을 묶어 APM이라고 부름
- PHP(Power Hypertext Preprocessor) 
    - 동적 웹페이지를 만들기 위한 프로그래밍 언어
    - PHP5 설치 시 생성되는 모듈 : libphp5.so
    - PHP 작성 시 <? ~ ?> 태그를 사용
- MySQL 
    - 오픈 소스의 관계형 데이터베이스 관리 시스템
    - `mysql_install_db`: 최초 설치 후 기본 DB 생성할 때 사용하는 명령어

### 1-3. 웹 관련 서비스의 운영

- 데이터베이스(Database, DB)
    - 개념
        - 통합된 데이터(Integrated Data) : 원칙적으로 똑같은 데이터가 중복되어 있지 않지만, 의도적인 중복이 있을 수 있음
        - 운영 데이터(Operational Data) : 어떤 기능을 수행하기 위해 반드시 유지해야 할 데이터가 존재
        - 공용 데이터(Shared Data) : 데이터가 하나의 어떤 것에 한정되어 지지 않고 여러 존재들이 해당 데이터를 공동으로 이용할 수 있음
- MySQL
    - 많이 사용되는 RDBMS(관계형 데이터베이스 관리 시스템)
    - 오픈 소스 프로그램이며, 다양한 OS를 지원
- Oracle DB
    - 오라클 사에서 개발한 상업용 RDBMS
    - 유닉스/리눅스 환경에서 가장 많이 사용됨
    - 대량의 데이터 처리에 특화
- PostgreSQL
    - 객체-관계형 DB 관리 시스템(ORDBMS)
    - 특징으로는 객체 생성의 유연함, 상속 기능 등이 있음
- SQLite
    - 임베디드 RDBMS
    - 일반적인 RDBMS에 비해 대량의 데이터 처리에는 적합하지 않음
    - 데이터를 저장하는 데에 하나의 파일만을 사용
- NoSQL (Not Only SQL)
    - 전통적인 RDBMS보다 덜 제한적인 일관성 모델을 이용
    - RDBMS가 아닌 키(key)와 값(value) 형태로 저장되고, 키를 통해 데이터 관리/접근
    - 대용량 데이터 처리, 데이터 분산 처리, 빠른 읽기/쓰기 속도의 장점을 가지고 있음
    - 종류 : Key-value 기반(Riak, …) / Document 기반(Mongo DB, …) / Big Table 기반(Hbase, …)
- Mongo DB
    - NoSQL이며 Document 기반의 DB
    - 오픈 소스 프로그램이며, NoSQL의 가장 유명한 DB
    - 각 객체의 구조가 뚜렷하며, RDBMS처럼 고정된 Schema 및 복잡한 JOIN이 없음
- CGI(Common Gateway Interface)
    - 외부의 프로그램을 실행시켜 그 결과를 HTML로 돌려줌
    - CGI 프로토콜이 단순하여 사용하기 쉬움
    - CGI 스크립트는 어떤 언어로도 코딩될 수 있으나 프로세스의 생성과 초기화에 많은 시간이 소요됨
- Apache Tomcat 
    - 웹 서버와 연동하여 실행 가능한 자바 환경을 제공하여 자바 서버 페이지(JSP)와 자바 서블릿이 실행할 수 있는 환경을 제공하는 웹 어플리케이션 서버(WAS)
    - 오픈 소스 프로그램

## 2. 파일 관련 서비스

### 2-1. SMB 서버 관리

- 삼바(Samba) 
    - Windows 클라이언트에서 유닉스 서버에 있는 파일/프린터를 공유할 수 있게 해주는 오픈 소스 소프트웨어
    - SMB 프로토콜을 유닉스 환경에 구현한 프리웨어 GPL 라이선스를 따르고 있음
    - SMB(Server Message Block) : 삼바 표준 프로토콜
    - CIFS(Common Internet File System) : 네트워크를 위한 SMB 프로토콜의 확장된 버전
- SMB 서버 환경 설정 (smb.conf) 
    - smb.conf : Samba 서비스의 동작을 제어하고 공유 디렉터리 및 보안 정책 등을 정의하는 핵심 파일
    - 구성
        
        ```bash
        # global 섹션
        [global]
           workgroup = WORKGROUP
           server string = Samba Server
           security = user
        
        # 공유 디렉토리 섹션
        [public]
           path = /samba/public
           browsable = yes
           writable = yes
           guest ok = yes
        ```
        
    - 주요 섹션
        - `global 섹션`
            - Samba 전체 설정 (서버 공통 옵션 정의)
            - 서버 이름, 워크그룹, 인증 방식 등 전역 설정
            
            | 옵션 | 설명 |  |
            | --- | --- | --- |
            | workgroup | Windows 네트워크 워크그룹 이름(작업 그룹 이름) |  |
            | server string | 서버 설명 |  |
            | security | 인증 방식 (`user`, `share`, `domain`, `ads`) |  |
            | log file | 로그 파일 위치 |  |
            | max log size | 로그 최대 크기 (KB) |  |
            | hosts allow | 허용할 IP 대역 지정 (화이트리스트) |  |
            | hosts deny  | 차단할 IP 대역 지정 (블랙리스트) |  |
        - security 옵션
            - user : 기본적인 보안 정책. 아이디/패스워드 방식으로 서버에 접근
            - share : 아이디/패스워드 인증을 거치지 않음
            - server : 기본적으로 user 모드와 동일하지만 현재 시스템이 아닌 SMB 서버를 지원하는 다른 서버를 통해 처리하는 방식
            - domain : Windows NT 도메인에서 처리하는 방식
    - `공유 디렉토리 섹션`
        
        
        | 옵션 | 설명 |
        | --- | --- |
        | comment | 공유 폴더에 대한 설명 |
        | path | 공유 폴더 경로 |
        | read only | 공유 폴더를 읽기 전용으로 설정 |
        | writable, write ok | 공유 폴더를 쓰기 가능으로 설정 |
        | valid users | 공유 폴더 접근 가능 유저 목록 |
        | write list | 공유 폴더 쓰기 가능 유저 목록 |
        | public, guest ok | 게스트 이용 가능 여부 |
        | browsable | 공유 이름을 브라우저에 표시 유무 |
        | printable | 프린터 스풀 파일 지정 |
        | create mask, create mode | 생성 권한 |
        | force group | 접근 가능 그룹 지정 |
        | directory mask | 공유 폴더 권한 |
- 관련 명령어/파일
    - testparm 명령어 : smb.conf 파일을 점검하는 명령어 
    - smbclient 명령어 
        - Samba 클라이언트 프로그램으로, 리눅스/유닉스에서 Windows 파일 공유(SMB/CIFS)에 접근할 때 사용하는 명령어
        - 형식
            - `smbclient //[HOSTNAME | IP]/[SHARE_NAME(디렉터리)] -U 사용자명`
            - `[-U]` : 로그인할 SMB 사용자 이름 지정
                - ex) smbclient //192.168.x.x/share -U smbuser → smbuser 계정으로 192.168.x.x 호스트의 share 폴더에 접근
            - `[-L]` : 지정한 윈도우 host의 공유 폴더를 확인
    - smbpasswd 명령어 
        - 삼바 사용자를 생성 및 삭제, 패스워드 변경, 활성 및 비활성화 등 전체적인 관련 정보를 변경
        - 형식
            - `smbpasswd [옵션] [사용자명]`
        - 옵션
            - `[-a]` : 계정 추가
            - `[-d]` : (disable) 지정한 계정을 사용하지 못하도록 설정
            - `[-e]` : (enable) 사용 중지된 계정을 다시 사용할 수 있도록 설정
            - `[-x]` : 지정한 계정을 삭제
            - `[-n]` : 지정한 계정의 패스워드를 삭제 (패스워드 없이 로그인 가능)
            - 옵션 없이 사용 시 지정한 계정의 패스워드를 변경
    - pdbedit 명령어 
        - `smbpasswd`가 주로 비밀번호 설정·변경에 초점이 맞춰져 있다면, `pdbedit`는 Samba 사용자 계정 전체를 관리
        - 형식
            - `pdbedit [옵션] 사용자명`
        - 옵션
            - `[-L]` : 삼바에 등록된 모든 계정의 정보를 출력 (USERNAME : UID : GROUP)
            - `[-u 사용자명]` : 특정 사용자 지정

### 2-2. NFS 서버 관리

- NFS(Network File System)
    - 네트워크 파일 시스템으로 네트워크 상의 다른 컴퓨터에 있는 파일이나 파일 시스템을 공유하기 위한 시스템
    - RPC(원격 프로시저 호출)을 사용
- 서버 환경 설정 (/etc/exports 파일) 
    - 형식
        - `[마운트 디렉터리] [마운트 허용 클라이언트] ([옵션])`
    - 옵션
        - `ro` : 공유된 자원을 읽기만 가능
        - `rw` : 공유된 자원을 읽기/쓰기 가능
        - `root_squash` : root 권한으로 접근 시 nobody 사용자로 변경하여 마운트
        - `no_root_squash` : root 권한으로 접근 가능
        - `all_squash` : 모든 사용자의 권한을 nobody 사용자로 변경하여 마운트
        - `no_all_squash` : root를 제외한 사용자에 대해 같은 UID를 가진 사용자에게 동일한 권한으로 마운트
        - `sync` : 파일 시스템 내 데이터 변동 시 즉시 동기화 처리
        - `async` : 파일 시스템 내 데이터 변동 시 비동기적으로 처리
        - ex) /home/nfs 192.168.10. (no_root_squash) → /home/nfs 디렉터리에 192.168.10.0/24 네트워크 내 클라이언트가 root  권한으로 마운트 가능 하도록 설정
- 관련 명령어
    - showmount 명령어 
        - NFS(Network File System) 서버가 외부 클라이언트에 공유하고 있는 디렉터리 목록을 조회
        - NFS/NIS의 마운트 정보를 출력
        - 형식
            - `showmount [옵션] 서버_IP 또는 호스트명`
        - 옵션
            - `[-a]` : 모든 원격 마운트 정보 출력
            - `[-e]` : export된(공유되거나 내보낸) 디렉터리의 정보 출력
            - ex) showmount -e 192.168.10.9 → 192.168.10.9의 NFS 서버의 export된 정보 출력
    - exportfs 명령어 
        - NFS 서버에서 공유 디렉터리(Export)를 관리하는 명령어
        - `/etc/exports`에 설정된 공유 디렉터리를 실제로 NFS 서버에 적용하거나, 현재 공유 상태를 확인할 때 사용하는 **NFS 서버 전용 관리 명령**
        - NFS 서버를 다시 시작하지 않고 공유정보를 수정하거나 출력
        - 형식
            - `exportfs [옵션] [디렉터리명]`
        - 옵션
            - `[-a]` : /etc/exports 파일을 읽음
            - `[-r]` : /etc/exports 파일을 다시 읽음
            - `[-v]` : 현재 공유 목록을 출력

### 2-3. FTP 서버 관리

- FTP(File Transfer Protocol)
    - 네트워크상의 컴퓨터들 간에 파일을 교환하기 위한 프로토콜
    - 포트번호 : TCP/20(data), TCP/21(control) 
    - 종류 : vsftpd, proftpd 등
- vsftpd(Very Secure FTP Daemon)
    - 보안이 강화된 FTP 데몬 많은 배포판에서 기본 FTP로 채택된 데몬
- 서버 환경 설정 (/etc/vsftpd/vsftpd.conf 파일) 
    - FTP 서버의 동작 방식, 사용자 권한, 보안 설정, 로깅 옵션 등을 제어
    - 키=값 형태의 설정 파일
    
    | 옵션 | 설명 |
    | --- | --- |
    | anonymous_enable | 익명 사용자의 접속 허용 유무(yes or no) |
    | local_umask | 로컬 계정 사용자들의 umask 값 설정 |
    | idle_session_timeout | idle(유휴) 시간에 대한 타임아웃 값 설정 |
    | max_clients | 최대 FTP 접속 클라이언트 수 설정 |
    | max_per_ip | 한 호스트가 동시에 접속할 수 있는 IP 수 설정 |
    | tcp_wrappers | tcp_wrapper 적용 여부 설정(yes or no) |
    | chroot_list_enable | 특정 사용자에 대해 자신의 홈 디렉터리를 루트 디렉터리 로 인식하도록 설정 (yes or no) |
    | chroot_list_file | chroot() 기능 관련 설정을 적용할 사용자를 등록한 파일 지정  |
    | chroot_local_user | 홈 디렉터리 밖 이동 금지 여부(yes or no) |
    | listen | 독립 실행형 여부 (yes or no) |
- /etc/vsftpd/ftpuser 파일 
    - ftp 서버 접근을 제한할 계정 목록을 저장
- proftpd
    - **유연하고 강력한 구성 옵션을 제공하는 FTP 서버 데몬**으로, 주로 대규모 시스템이나 고급 기능이 필요한 환경에서 많이 사용
    - `vsftpd`가 보안과 성능에 집중한 경량 서버라면, `proftpd`는 **아파치 웹서버처럼 모듈화 구조**와 **세부적인 사용자 정의 기능**이 강점
- 서버 환경 설정 (/etc/proftpd.conf 파일) 
    
    
    | 옵션 | 설명 |
    | --- | --- |
    | ServerName | FTP 서버 이름을 설정 |
    | ServerType | 데몬 구동 방식 설정(standalone/xinetd) |
    | Port | 리스닝 포트번호 설정(기본 21) |
    | Umask | 로컬 계정 사용자들의 umask 값 설정(022로 권장) |
    | MaxInstances | 최대 FTP 접속 클라이언트 수 설정 |
    | User nobody | FTP 실행 시 사용할 사용자 명 설정 |
    | Group Nobody | FTP 실행 시 사용할 그룹 명 설정 |
    | TimeoutIdle | idle(유휴) 시간에 대한 타임아웃 값 설정 |
    | <Directory>  | 지정한 디렉터리에 대한 설정  |
    | <Anonymous ~ftp> | 익명 사용자를 위한 디렉터리에 대한 설정 |
