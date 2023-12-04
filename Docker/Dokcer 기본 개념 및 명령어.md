## Docker란?

- 컨테이너 기반 가상화 도구!
- 애플리케이션을 컨테이너라는 단위로 격리하여 실행하는 기술!
- 리눅스 컨테이너 기술인 LXC(Linux Containers)을 기반으로 한 기술
- 다양한 운영체제에서 사용할 수 있으며, 컨테이너화된 애플리케이션을 손쉽게 빌드, 배포, 관리할 수 있는 다양한 기능을 제공
- 위 기능들을 통해 애플리케이션을 빠르게 개발하고, 효율적으로 배포, 관리할 수 있음
- 컨테이너는 서로 격리되어 있지만 여러가지 방식을 통해 데이터를 공유하거나 통신 할 수 있음

### Container란?

- 컨테이너는 가상화 기술 중 하나
- 호스트 운영체제 위에 여러 개의 격리된 환경을 생성하여 각각의 컨테이너 안에서 애플리케이션을 실행

### 가상화(Virtualization) 기술이란?

- 하나의 물리적인 컴퓨터 자원(CPU, 메모리, 저장장치 등)을 가상적으로 분할하여 여러 개의 가상 컴퓨터 환경을 만들어 내는 기술
- 이를 통해 물리적인 컴퓨터 자원을 더욱 효율적으로 사용할 수 있으며,서버나 애플리케이션 등을 운영하는데 있어 유연성과 안정성을 제공

### 도커를 사용하는 이유?

- 가상화는 필요하지만 가상머신은 각각의 Guest OS가 있기 때문에 무겁지만 도커는 Container Engine 을 통해 각각 Guest OS를 두지 않고 사용할 수 있어 VM 보다 더 가벼움
- 애플리케이션을 구동하기 위한 환경을 제공
- 코드 배포와 테스트를 쉽고 빠르게 진행가능

### Docker **Architecture**

![Untitled](https://github.com/juhwan-Ki/TIL/assets/87765888/79c163ab-100b-4caf-a7e5-37cb3c2d19fb)


### 도커 데몬(Docker deamon = dockerd)

- 도커 엔진의 핵심 구성 요소
- 도커 호스트에서 컨테이너를 관리하고 실행하는 역할
- 컨테이너를 생성, 시작, 중지, 삭제하는 등의 작업을 수행
- 컨테이너 이미지를 관리하고 외부에서 이미지를 다운로드하고 빌드하는 작업을 수행

### 도커 클라이언트(Docker Client)

- 도커 사용자가 도커와 상호작용하는 기본방법
- `docker` 명령어를 사용하면  클라이언트가 명령을 API로서 도커 데몬으로 보내어 실행
- 이때 도커 클라이언트는 /var/run/docker.sock에 있는 유닉스 소켓을 통해 도커 데몬의 API를 호출
- 도커 클라이언트가 사용하는 유닉스 소켓은 같은 호스트 내에 있는 도커 데몬에게 명령을 전달할 때 사용

### 도커 레지스트리(Docker Registries)

- 도커 이미지를 관리하고 저장하는 곳
    - Docker hub: 디폴트 레지스트리로 누구나 접근 가능한 공개형 저장소

### 도커 오브젝트(Docker Object)

- 도커 이미지
    - 도커 컨테이너를 만들기 위한 읽기 전용 템플릿
- 도커 컨테이너
    - 한 도커 이미지의 실행 가능한 인스턴스
    - 애플리케이션을 실행하기 위한 모든 파일과 설정 정보를 포함하는 패키지

<aside>
💡 Docker hub는 App Store로 image는 App Store에서 받아온 프로그램(App), 그 프로그램을 실행시키면 발생하는 프로세스를 Container라고 생각하면 된다!

</aside>

## 도커 명령어

- docker pull
    
    ```docker
    docker pull [OPTIONS] NAME[:TAG|@DIGEST] 
    
    ex) docker pull httpd
    ```
    
- docker images
    
    ```docker
    docker images
    ```
    
- docker run
    
    ```docker
    docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
    
    docker run httpd
    docker run --name secondContainer httpd
    docker run -p 8888:80 -v /Users/jk/wanted/2308/docker-pro-2308/lecture/1st:/usr/local/apache2/htdocs httpd
    ```
    
    - **--** name ****
        - 컨테이너 이름을 설정
    - -**d, --detach**
        - ****Detached 모드로 보통 데몬 모드라고 부르며, 컨테이너가 백그라운드로 실행
    - **p, --publish**
        - 호스트와 컨테이너의 포트를 연결 (포트포워딩)
        - <호스트 포트>:<컨테이너 포트> → p 80:80
    - **v, --volume**
        - 데이터 볼륨을 설정 호스트와 컨테이너의 디렉토리를 연결하여, 파일을 컨테이너에 저장하지 않고 호스트에 바로 저장 (마운트)
- docker stop
    
    ```docker
    docker stop [OPTIONS] CONTAINER [CONTAINER...]
    
    docker stop 9b0f49de746c
    docker stop -a
    ```
    
- docker logs
    
    ```docker
    docker logs [OPTIONS] CONTAINER
    
    docker logs second
    docker logs second -f
    ```
    
- docker rm
    
    ```docker
    docker rm [OPTIONS] CONTAINER [CONTAINER...]
    
    docker rm 6026ab9b44cc
    docker rm second -f
    ```
    
- docker rmi
    
    ```docker
    docker rmi [OPTIONS] IMAGE [IMAGE...]
    docker rmi 6026ab9b44cc
    ```