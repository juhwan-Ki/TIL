## 1. 시스템 분석

### 1-1. 시스템 로그 분석

- 시스템 로그 파일 종류
    - 일반적으로 리눅스에서는 `/var/log` 디렉터리에서 시스템의 모든 로그를 기록하고 관리
    - 로그들은 `(r)syslogd`에 의해서 관리되며, `/etc/syslog.conf` 파일에서 로그 파일의 저장 위치와 저장 파일 이름을 변경할 수 있음 
- 시스템 로그 / 관련 명령어 정리 
    
    | 로그명 | 내용 | 로그경로 | 명령어 | 기타 |
    | --- | --- | --- | --- | --- |
    | utmp | 현재 로그인한 사용자의 정보 | /var/run/utmp | finger, w, who | 바이너리 파일이므로 
    명령어를 통해 확인 |
    | wtmp | 성공한 로그인/로그아웃,  
    시스템 boot/shutdown 정보 | /var/log/wtmp | last, 
    last -F | 바이너리 파일이므로 
    명령어를 통해 확인 |
    | lastlog | 마지막으로 성공한 로그인 기록 | /var/log/lastlog | lastlog, finger | - lastlog -u [계정명] 
    - lastlog -t [일수] |
    | btmp | 실패한 로그인 시도에 대한 기록 | /var/log/btmp | lastb 
    last -f btmp | - 실패한 모든 로그를 남김 - 바이너리 파일이므로 명령 어를 통해 확인 |
    | acct/pacct | 사용자가 입력한 명령어,  터미널 종류, 프로세스 
    시작시간 등을 기록 | - 
    (생략) | lastcomm | 바이너리 파일이므로 
    명령어를 통해 확인 |
    | secure | 사용자/그룹 생성, 삭제, 로그 인 등 사용자 인증에 대한 정 보 기록 | /var/log/secure | x | 원격 접속(ssh, telnet) 기록,  su 명령어 수행 내역, xinetd  인증 기록 |
    | message | 시스템 운영에 대한 전반적인 메시지 기록 | /var/log/messages | x | tcpwrapper, snort 등의 수행 내역 기록 |
    | dmesg | 리눅스가 부팅될 때 출력되는 메시지 기록 | /var/log/dmesg | dmesg | 텍스트 파일 |
    | boot.log | 리눅스가 부팅될 때 
    파일시스템, 서비스 데몬 
    체크한 정보 기록 | /var/log/boot.log | x | - |
    | Xferlog | FTP 로그 파일 | /var/log/xferlog | x | FTP 사용한 내용을 상세하게 기록 |
- 로그 관련 명령어 상세 
    - last 명령어 
        - 성공한 로그인/로그아웃 정보, 시스템 부팅/종료 정보를 출력하는 명령어
        - 관련 로그 파일 : `/var/log/wtmp`
        - 형식
            - `last [옵션] [사용자명]`
        - 옵션
            - `[-F]` : 정보에 대한 시간을 자세히 표시
            - `[-n]` : 최근으로부터 NUM개의 정보를 출력
            - `[-f FILE]` : 지정한 파일의 로그 정보 출력
            - `[-x]` : 시스템 이벤트(reboot, shutdown)도 포함
            - 명령어 뒤에 특정 사용자 계정을 지정하면 해당 사용자 계정에 대한 로그만을 출력
            - ex) last -10 reboot → 시스템 부팅/종료에 대한 로그의 최근 10개 출력
    - lastb 명령어 
        - 실패한 로그인 시도에 대한 정보를 출력하는 명령어
        - 관련 로그 파일 : `/var/log/btmp`
        - 형식
            - `lastb [옵션]`
        - last 명령어와 옵션이 같음
    - lastlog 명령어 
        - 마지막으로 성공한 로그인 기록을 출력하는 명령어
        - 관련 로그 파일 : `/var/log/lastlog`
        - 형식
            - `lastlog [옵션]`
        - 옵션
            - `[-u]` : 지정한 사용자에 대한 로그만 출력
            - `[-i]` : 특정 ip로 로그인한 사용자 로그 출
            - `[-t DAY]` : DAY 일수 이전에 대한 로그만 출력
    - dmesg 명령어 
        - 커널 메시지(Kernel Message)를 출력하는 명령어
        - 시스템 부팅 시 발생한 메시지, 하드웨어 인식 정보, 드라이버 로딩 상태 등을 포함한 시스템 초기화 정보를 확인
        - 관련 로그 파일 : `/var/log/dmesg`
- /etc/(r)syslog.conf 파일 
    - 파일 내용 구성
        - 형식
            - `서비스 종류.우선순위;서비스 종류.우선순위 /로그파일 위치`
        - `facility(서비스 종류)`
            - `auth` : 인증 관련 시스템 (로그인, su, getty 등)
            - `mail` : sendmail, pop, qmail 등과 같은 메일 관련
            - `authpriv` : auth와 같지만 사용자 별로 읽을 수 있는 파일에 기록됨
            - `kern` : 커널에 의한 메시지(커널 메시지)
            - `cron` : crond 데몬과 atd 데몬에 의해 발생
            - `lpr` : 프린터 데몬인 lpd에 의해 발생
            - `*` : 모든 시스템에서 발생
        - `priority(우선순위)`
            - `* (9)` : 모든 상황
            - `debug (8)` : debugging에 관한 (가장 낮은 단계)
            - `info (7)` : 단순한 프로그래밍에 대한 정보
            - `notice (6)` : 에러가 아닌 알림
            - `warning (5)` : 주의 필요
            - `err (4)` : 에러
            - `crit (3)` : 급한 상황은 아니지만 치명적인 시스템 문제 발생
            - `alert (2)` : 즉각적인 조치를 취해야함
            - `emerg (1)` : 매우 위험한 상황의 메시지(가장 높은 단계)
            - `none (0)` : 해당 사항 없음(기록될 내용 없음)
        - `action`
            - 앞에 서비스/우선순위에 대한 결과
            - 특정 로그 파일을 지정하거나, 터미널/콘솔/특정 유저에게 보낼 수 있음
    - 설정 내용 예제
        - kern.* /dev/console
            - kernel에 관련된 모든 로그를 콘솔에 출력
        - *.info;mail.none;auth.none /var/log/messages
            - mail, auth 서비스에 대한 로그를 제외한 모든 서비스에 대한 info 레벨 이상의 메시지를 messages 파일에 기록
        - *.err fedora
            - 모든 서비스에 대한 err 레벨 이상의 메시지를 fedora 사용자의 스크린으로 메시지를 보냄
        - mail.*;mail.!=info /var/log/maillog
            - 메일 서비스에 대해 info 레벨을 제외한 모든 메시지를 maillog 파일에 기록
- rsyslog 
    - syslog의 확장 버전으로, TCP/UDP를 이용한 로깅을 지원
    - 데이터 베이스 관련 로그 관리도 가능
    - 데몬은 `rsyslogd`이며, 설정파일은 `/etc/rsyslog.conf`
- 로그 순환(logrotate) 
    - 로그 파일을 여러 개로 분할해주는 프로그램
    - 로그 파일이 지정 용량에 도달했을 때 다른 파일로 대체하고, 로그 파일을 압축하여 관리하는 것
    - 각각의 로그 파일은 하루, 일주일, 한 달 단위로 로테이션 할 수 있음
    - 시스템과 관련된 기본적인 로그 설정 파일 : `/etc/logrotate.conf`
    - 형식
        - `logrotate [옵션] config_file`
    - 주요 옵션
        - `[-f]` : 강제로 환경 설정 파일을 읽어 들여서 진행
    - 통합 로그 설정 (/etc/logrotate.conf) 
        
        ```bash
        /* 주 단위 로그파일 순환 (daily, monthly) */
        weekly
        
        /* 순환 로그 파일의 개수 설정 */
        /* logfile, logfile.1 logfile.2 logfile.3 logfile.4 형태로 생성 */
        rotate 4
        
        /* 순환 시 새롭게 로그파일을 생성 */
        create
        
        /* 로그파일의 확장자로 날짜를 붙여서 생성 ex) maillog-20250224 형태 */
        dateext
        
        /* 로그 파일 압축(compress), 압축안함(uncompress) */
        compress
        
        /* 해당 디렉터리에 있는 개별 데몬/프로세스 설정 파일을 포함 */
        include /etc/logrotate.d
        
        /* 로그 파일이 존재하지 않을 경우 에러 메시지 출력(nomissingok) 미출력(missingok) */
        nomissingok /* (기본값) */
        ```
        
    - 개별 로그 설정 (/etc/logrotate.d/*) 
        
        ```bash
        /* httpd 디렉터리에 있는 모든 로그에 적용 */ 
        /var/log/httpd/*log {
         missingok /* 로그파일이 없어도 오류 발생 X */
         notifempty /* 로그파일이 비어 있는 경우 순환 X */ 
         create 0644 root utmp /* 해당 권한, 소유자, 소유그룹으로 생성 */ 
        }
        ```
        

## 2. 시스템 보안 및 관리

### 2-1. 시스템 보안 관리

- SELinux 
    - 리눅스 커널에 내장된 보안 모듈로 MAC(강제적 접근 제어)를 수행
    - 관련 명령어
        - getenforce 명령어 : SELinux의 상태를 확인하는 명령어
            - 활성화 : enforcing
            - 비활성화 : disabled
            - 테스트 모드(비활성화) : permissive
        - sestatus 명령어 : SELinux의 정보를 출력하는 명령어
        - setenforce 명령어 : SELinux의 동작 모드를 변경하는 명령어 (0 – permissive / 1 – enforcing)

### 2-2. 시스템 보안 관련 명령어

- SSH(Secure Shell) 
    - 암호통신을 이용하여 네트워크상의 다른 컴퓨터에 접속하여 원격으로 명령을 실행/파일 조작을 하는 프로토콜
    - 원격 쉘(rsh) 지원, 원격 복사(scp) 지원, sftp 지원
    - 포트 번호 : TCP/22
    - 형식
        - `ssh 사용자이름@서버주소`
    - 명령어 옵션 `2016(1)`
        - `[-l]` : 원격 시스템에 로그인 할 사용자 지정
        - `[-p]` : 원격 시스템에 연결할 포트 지정
- PAM(Pluggable Authentication Modules) 
    - 리눅스 및 유닉스 시스템에서 인증(authentication)을 관리하는 모듈형 프레임워크
    - PAM 구성 파일(`/etc/pam.d/*`) 문법
        - `[type] [control] [module-path] [module-arguments]`
    - 구성 토큰 
        1. type : PAM에 어떤 타입의 인증이 사용될 것 인지를 알려줌
            - account : 사용자가 해당 서비스에 접근이 가능한지, 패스워드 기간이 만료되었는지 결정
            - auth : 주로 패스워드를 사용하지만, 보다 정교한 방법을 통해 사용자가 맞는지 결정
            - session : 사용자가 인증 받기 전/후에 해야 할 것을 나타냄
        2. control : 이 모듈이 동작하지 않는다면 PAM에게 무엇을 해야 할지 알려주는 것
            - requisite : 이 모듈을 이용하는 인증이 실패하는 경우, 즉시 인증을 거부하도록 함
            - required : 인증이 거부되기 전에 PAM이 이 서비스에 등록된 다른 모듈들을 요구해도 인증을 실패할 경우,  인증을 거부하도록 함
            - sufficient : 이전에 요청되어진 모듈이 실패하더라도 이 모듈에 의해 인증이 성공할 경우, PAM은 인증을 승인
        3. module-path : PAM에게 어떤 모듈을 사용할 것인지, 어디에 위치하여 있는지 알려줌  
        4. module-arguments : 모듈에게 전달되는 인수
- pam_tally2 명령어 
    - 사용자들이 인증의 실패에 대한 정보 출력 / 관리를 하는 명령어
    - 패스워드 인증 실패 횟수 초과 시 명령어 사용
    - 형식
        - `pam_tally2 -u [계정 이름] -r`
- sudo 명령어 
    - 관리자가 사용자에게 특정 명령어에 대해 루트 권한으로 실행할 수 있게 해주는 명령어
    - /etc/sudoers 파일에서 특정 명령어에 대해 sudo 명령어를 사용할 수 있도록 관리할 수 있음
- tripwire 명령어 
    - 원본 파일의 무결성을 점검하는 도구
    - MD5, SHA, CRC-32 등의 다양한 암호화 함수를 제공
    - 먼저 시스템에 존재하는 파일들에 대해 DB를 만들어 저장한 후, 생성된 DB와 비교하여 점검
- COPS(Computer Oracle and Password System) 
    - 리눅스 시스템에 대한 보안 문제를 알려주는 도구
    - /etc/passwd, /etc/group, SUID 파일 등에 대한 점검을 수행
    - 파일, 디렉터리 및 장치 파일에 대한 퍼미션 점검 수행

## 3. 시스템 백업

### 3-1. 백업 정책 수립

- 백업의 종류 
    - 데이 제로 백업(A Day-zero Backup) : 시스템을 설치한 후 사용자들이 시스템을 사용하기 전에 시스템을 백업하 는 것
    - 풀 백업(A Full Backup) : 주기적으로 시스템을 백업하는 것
    - 증분 백업(An Incremental Backup) : 특정한 이벤트 후 또는 주기적으로 이전의 백업 후 변경된 파일들만 백업 하는 것
    - 단순 백업 : 첫 백업 때 풀 백업을 진행한 후, 그 다음부터 변경분 백업을 수행하는 것
    - 다단계 백업(Multilevel Backup) : 큰 규모나 중요한 시스템의 백업을 할 때 수행하는 것

### 3-2. 백업 관련 명령어

- cpio 명령어 
    - 파일 및 디렉토리를 아카이브(압축 없이 묶음)하거나 풀 때 사용되는 명령어
    - `find`, `ls` 같은 명령어와 함께 사용해야 함
    - 많은 양의 데이터를 테이프 드라이브에 백업하기 위한 명령어
    - 네트워크를 통한 백업 / 증분 백업을 지원하지 않음
    - 주요 옵션
        - `[-c]` : ASCII 형태로 헤더 정보를 읽고 씀
        - `[-i]` : 아카이브에서 파일 추출
        - `[-o]` : 아카이브 생성
        - `[-v]` : 진행 과정을 자세하게 출력
        - ex) ls *.conf | cpio -ocv > config.bak → 모든 .conf 파일을 백업
- dump 명령어 
    - 파일 시스템 전체를 백업하는 명령어
    - 주로 `ext2`, `ext3`, `ext4` 파일 시스템을 백업하는 데 사용됨
    - 점진적인 백업 기능 : 이전 백업 이후 변경된 파일들에 대해 백업 수행 가능
    - `restore` 명령어와 함께 사용하여 복구 가능
    - 장점
        - 여러 개의 테이프에 백업 가능
        - 어떤 타입의 파일도 백업 가능
        - 증분 백업 가능
        - 결함을 가진 파일들도 다룰 수 있음
    - 단점
        - 모든 파일 시스템은 개별적으로 dump 되어야 함 (각 파티션도 개별적으로 dump)
        - NFS 파일 시스템을 dump할 수 없음(로컬 파일 시스템만 dump 가능)
    - 형식
        - `dump [옵션] -f [백업파일] [백업할 파일 시스템]`
    - 주요 옵션
        - `[-0~9]` : 0 – 전체 백업 / 1,2,… - 부분 백업
        - `[-f]` : 지정한 파일명으로 백업 파일 생성
        - `[-u]`: 백업 날짜를 `/etc/dumpdates`에 기록
- restore 명령어 
    - `dump` 명령어로 생성한 백업 파일을 복원하는 명령어
    - 개별 파일 또는 전체 파일 시스템을 복원 가능
    - 형식
        - `restore [옵션] -f [백업파일]`
    - 주요 옵션
        - `[-i]` : 대화형으로 복원 수행
        - `[-f]` : 장치 이름 지정
        - `[-r]` : 백업 대상 전체를 복원
        - `[-x]` : 특정 파일 복원
- rsync 명령어 
    - 파일을 동기화하는 명령어 원격지에 있는 파일들도 동기화할 수 있음
    - 파일 및 디렉토리를 동기화(복사 및 백업)하는 명령어
    - **증분 백업(incremental backup) 지원**(변경된 파일만 복사)
    - scp 보다 빠르고 유용하며, SSH를 통해 안전하게 전송 가능
    - cp, ftp, rcp 명령어보다 동기화 기능이 뛰어남
    - 형식
        - `rsync [옵션] [동기화할 원본] [동기화될 위치]`
    - 주요 옵션
        - `[-a]` : 아카이브 모드 (여러 옵션을 묶어 놓은 옵션)
        - `[-v]` : 진행 과정을 자세하게 출력
        - `[-z]` : 동기화 파일을 압축
