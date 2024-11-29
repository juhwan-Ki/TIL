### Netty란?
Netty는 고성능 네트워크 애플리케이션(서버 및 클라이언트)을 신속하게 개발할 수 있도록 설계된 비동기 이벤트 기반 프레임워크로, 유지보수와 확장성을 고려하여 효율적인 프로토콜 구현을 지원한다
Netty를 사용하게 되면 비동기 및 Non-blocking I/O 모델을 활용하여 많은 동시 연결을 효율적으로 처리할 수 있으며 다양한 프로토콜(HTTP, WebSocket, TCP, UDP 등)을 지원하여, 필요에 맞는 네트워크 프로토콜을 쉽게 구현할 수 있다

### Blocking & Non-Blocking
소켓의 동작 방식에는 Blocking과 Non-Blocking으로 나뉘게 된다

1. Blocking
- Blocking 이란 요청한 작업이 성공하거나 에러가 발생하기 전까지는 응답을 돌려주지 않는 것을 의미한다
- Blocking은 이해하기 쉽고 구현이 용이하다는 장점이 있지만, 컴퓨터 리소스를 비효율적으로 사용하는 등의 문제가 있다
- Blocking 방식의 문제점
    1. 스레드 낭비 :  Blocking 방식에서는 각 요청마다 새로운 스레드를 할당하거나, 스레드가 블록된 상태에서 대기 이로 인해 스레드가 실제로 작업을 하지 않고 대기만 하므로 CPU 자원을 낭비
    2. 스레드 수 제한: 시스템에서 동시에 처리할 수 있는 스레드의 수에는 한계가 존재함 많은 클라이언트 요청을 처리하기 위해 많은 스레드를 생성하면 스레드 컨텍스트 전환(context switching)이 빈번하게 발생하여 오버헤드가 증가 
    3. 응답 시간 지연 : 요청이 완료되기 전까지 다른 작업을 할 수 없어 서버의 응답 시간이 길어져 지연이 발생하게 되고 지연이 발생하게 되면 시스템 전체 성능에 부정적인 영향을 끼치게 됨
    4. 확장성 부족 : 요청을 처리하는 데 하나의 스레드를 차지하게 되므로, 서버에 많은 클라이언트가 접속할 때 서버의 성능이 급격하게 저하될 수 있음

이 문제를 해결하기 위해 ThreadPool을 도입하여 다중 커넥션을 동시에 처리할 수 있게 하였고 또한, 특정 시점에 특정 커넥션만을 유지함으로써 리소스(CPU, 메모리 등)의 과다 사용을 방지할 수 있었다 
그러나 ThreadPool이 가득 차면 idle thread가 나올 때까지 새로운 커넥션은 대기하거나 차단되며 Blocking 방식을 사용하다 보니, 하나의 스레드에서 처리하는 작업이 지연되면 다른 스레드의 처리도 지연되는 문제도 발생하게 된다
이로 인해 높은 트래픽을 효율적으로 처리하기 어려워지며, 대용량 트래픽을 처리하는 데 비효율적인 방식이 된다

2. Non-Blocking
- 위 문제의 근본적인 문제를 해결하기 위해 Non-Blokcing이라는 새로운 방식이 등장하게 되었다
- Non-Blocking 이란 요청한 작업의 처리 결과와 상관없이 즉시 응답을 돌려주는 것을 의미한다
- Non-Blocking은 작업이 완료되면 결과를 처리하는 콜백 함수나 이벤트 리스너를 통해 결과를 받아오기 때문에 적은 수의 Thread로도 다수의 연결을 효과적으로 처리할 수 있다
- 기존 Blokcing 방식은 요청한 작업의 처리가 완료될 때까지 Thread를 점유함으로서 다른 작업 요청을 처리하지 못하였던과는 달리 Non-Blocking 방식에서는 결과와 상관없이 즉시 반환하기 때문에 I/O 작업과 같은 느린 작업이 발생할 때, 기다리지 않고 다른 작업을 처리하여 응답 시간 및 리소스를 효율적으로 사용할 수 있다

Non-blocking 시스템은 이벤트 루프(Event Loop)를 사용하여 작업을 처리하며 이벤트 루프는 여러 작업을 순차적으로 실행하지 않고, 각 작업이 완료되면 결과를 처리하는 이벤트를 발생시킨다
이러한 방식을 사용하면 동시성을 효율적으로 처리할 수 있으며 Netty는 실제로 위 EventLoop 개념을 적극 적용하여 높은 성능을 유지한다

## Netty의 구성 요소

### 1. BootStrap
BootStrap은 네트워크 애플리케이션의 초기 설정 및 실행을 도와주는 구성 도구로 프로세스를 지정된 포트로 바인딩 하거나 프로세스를 지정된 호스트의 지정된 포트에서 실행 중인 다른 호스트로 연결하는 등의 역할을 한다

BootStrap은 ServerBootstrap과 BootStrap으로 나뉘게 된다
#### ServerBootstrap
서버 애플리케인션을 설정하고 실행하는데 사용 <br>
다중 클라이언트를 처리할 수 있도록 서버 소켓 채널을 생성하고 이벤트 루프 및 파이프라인의 설정에 대한 설정을 진행

#### ServerBootstrap 주요 메소드
1. **`group(EventLoopGroup bossGroup, EventLoopGroup workerGroup)`** : 서버 `Channel`의 이벤트 처리를 담당할 스레드 풀 설정
2. **`channel(Class<? extends ServerChannel> channelClass)`** : 서버 `Channel`의 타입을 지정(일반적으로 `NioServerSocketChannel`을 사용하여 Non-blocking 소켓을 설정)
3. **`childHandler(ChannelInitializer)`** : 클라이언트와 연결된 `Channel`의 초기 설정(Handler 추가 등)을 담당, `ChannelInitializer` 내부에서 `Pipeline`에 Handler 추가
4. **`option(ChannelOption<T> option, T value)`** : 서버 `Channel`에 설정할 옵션을 지정
5. **`childOption(ChannelOption<T> option, T value)`** : 클라이언트 `Channel`에 적용할 옵션을 지정
6. **`bind(int port)`** : 지정된 포트로 서버를 바인딩, 반환값은 `ChannelFuture`로 비동기 작업 완료 여부를 확인할 수 있음

#### BootStrap
클라이언트 애플리케이션을 설정하고 실행하는 데 사용하며 서버와 달리 단일 소켓(Channel)만 생성, 서버에 연결하는 작업 수행

#### BootStrap 주요 메소드
1. **`group(EventLoopGroup group)`** : 클라이언트 `Channel`의 이벤트 처리를 담당할 스레드 풀 설정
2. **`channel(Class<? extends ServerChannel> channelClass)`** : 클라이언트 `Channel`의 타입을 지정(일반적으로 `NioSocketChannel` 사용하여 Non-blocking 소켓을 설정)
3. **`handler(ChannelInitializer)`** : 클라이언트 `Channel`에 적용할 초기 설정(Handler 추가)을 담당, `ChannelInitializer` 내부에서 `Pipeline`에 Handler 추가
4. **`connect(String host, int port)`** : 지정된 서버의 호스트와 포트로 연결 시도, 반환값은 `ChannelFuture`로 비동기 연결 상태를 확인할 수 있음

### 2. EventLoop
EventLoop는 단일 스레드로 동작하며 하나의 Thread와 연결되어 무한 루프를 돌며 Selector에 등록된 Channel에서 이벤트가 발생할 때까지 대기하다가 이벤트가 발생하면 해당 이벤트를 처리할 수 있는 Handler에게 전달하는 역할을 한다

Selector에 등록된 Socket Channel Key중 이벤트가 발생한 SelectedKey들을 Task Queue에 넣고 등록된 Channel에 Handler Pipeline에 위임하여 네트워크 read/write와 비즈니스 로직을 처리한다

같은 역할을 수행하는 복수의 EventLoop를 EventLoop Group으로 묶을 수 있으며, EventLoopGroup은 크게 BossGroup와 ChildGroup으로 구분된다

#### BossGroup
Accept EventLoop Group으로 새로운 클라이언트의 연결 요청을 처리하는 역할을 하는 Group으로 연결 요청이 감지되면, accept 이벤트를 처리하여 새로운 SocketChannel을 생성한다

BossGroup내 EventLoop는 클라이언트와의 연결 부분만 처리하며, accept 완료된 SocketChannel은 WorkerGroup EventLoop Selector에 위임하여 read/write 이벤트는 WorkerGroup의 EventLoop가 처리하도록한다

#### WorkerGroup
BossGroup으로부터 전달받아 Selector에 등록된 SocketChannel에서 발생하는 read/write 이벤트를 처리한다

read/write에 대한 이벤트를 모두 처리하기때문에 사용자와의 요청/응답에 대한 비즈니스 처리는 모두 WorkerGroup의 EventLoop에서 처리한다

보통 WorkerGroup은 BossGroup보다 EventLoop를 더 많이 생성하게 되는데 그 이유는 새로운 커넥션 연결보다 WorkerGroup에서 처리하는 read/write 이벤트가 훨씬 많이 발생하기 때문이다

Netty를 클라이언트로 사용할 경우 새로운 연결을 한번만 하면 되기 때문에 보통 하나의 EventLoopGroup으로  I/O(connect, read, write) 이벤트를 모두 처리한다

### 3. Channel
Channel은 TCP 커넥션에 대한 I/O 작업을 처리하는 객체로 클라이언트와 서버 간 데이터를 주고받기 위한 통로의 역할을 수행한다

Channel은 클라이언트 요청이 들어오거나, 클라이언트에서 서버로 연결 요청을 보낼 때 생성 되며 데이터 처리를 위한 ChannelPipeline이 연결되어 있다

ChannelPipeline에는 여러 Handler를 포함할 수 있고 설정된 Handler들을 차례로 호출하여 데이터의 가공 및 처리 로직을 수행한다
#### Channel 주요 메소드
1. **`write(Object msg)`** : 원격에 데이터를 write함. 데이터는 메시지는 버퍼에 작성되며, 실제 전송은 `flush()`를 통해 이루어짐
2. **`flush(Object msg)`** : 이전에 write된 데이터를 모두 전송 계층 소켓에 flush
3. **`writeAndFlush(Object msg)`** : 데이터를 작성하고 즉시 전송
4. **`pipeline()`** : `Channel`에 등록된 `ChannelPipeline`을 반환
5. **`remoteAddress()`** : 원격의 `SocketAddress`를 반환


### 4. ChannelFuture
Channel에서 처리하는 모든 I/O 작업은 비동기로 진행되기 때문에 완료 여부와 상관 없이 즉시 반환된다

이때 모든 I/O 작업 요청의 결과로 void를 return하는 것이 아닌, ChannelFuture를 반환하게 되며 ChannelFuture를 통해서 I/O 처리가 완료 되었는지 확인 하고 결과를 검색 할 수 있다

#### ChannelFuture 주요 메소드:
1. **`isDone()`**:작업이 완료되었는지 여부를 반환(작업이 성공하거나 실패하면 true를 반환)
2. **`isSuccess()`**: 작업이 성공적으로 완료되었는지 여부를 반환
3. **`cause()`**: 작업 실패 시 예외를 반환 성공적인 작업이라면 null을 반환
4. **`sync()`**: 현재 ChannelFuture가 완료될 때까지 Blokcing 처리, 작업이 완료될 때까지 기다리고, 완료되면 ChannelFuture를 반환
5. **`addListener(ChannelFutureListener listener)`**: 작업이 완료되었을 때 실행할 리스너를 추가
6. **`cancel()`**:  비동기 작업 취소

### 5. ChannelHandler
ChannelHandler는 데이터 송수신 또는 네트워크 이벤트를 처리하기 위해 사용하는 인터페이스로 ChannelPipeline에 등록되어, 매핑된 Channel이 등록된 EventLoop에서 발생한 I/O Event와 I/O Operation을 처리하거나 Pipeline 내에서 다음 Handler를 실행하는 역할을 한다

ChannelHandler는 데이터를 받기 위한 Inbound(Input Stream), 데이터를 내보내기위한 Outbound(Output Stream)에 따라 구분되어 사용된다

#### ChannelInboundHandler
Inbound I/O Event를 handle하는 Handler로 원격 장치에서 요청한 CONNECT와 READ에 대한 이벤트에 대한 콜백을 수행하는 ChannelHandler

#### ChannelInboundHandler 주요 메소드
1. **`channelRegistered(ChannelHandlerContext ctx)`** : `Channel`이 `EventLoop`에 등록되어 I/O 작업을 수행할 준비가 되었을 때 호출
2. **`channelUnregistered(ChannelHandlerContext ctx)`** : `Channel`이 `EventLoop`로부터 해제되어 어떠한 I/O 작업도 수행할 수 없을 때 호출
3. **`channelActive(ChannelHandlerContext ctx)`** : `Channel`이 `EventLoop`에 등록되고 난 후 active 상태 일때 호출(connected, bind된 상태)
4. **`channelInactive(ChannelHandlerContext ctx)`** : `Channel`이 deactive된 상태를 의미하며, 원격 장치와 connection이 끊겼을 때 호출
5. **`channelRead(ChannelHandlerContext ctx, Object msg)`** : `Channel`로부터 데이터를 read할 때 호출
6. **`channelReadComplete(ChannelHandlerContext ctx)`** : 데이터 read 작업이 완료되었을 때 호출
7. **`exceptionCaught(ChannelHandlerContext ctx, Throwable cause)`** : 처리 중 예외가 발생했을 때 호출

#### ChannelOutboundHandler
Outbound I/O Event를 handle하는 Handler로 원격 장치에 보낼 연결 요청 (connect), write 이벤트에 대한 콜백을 수행하는 ChannelHandler

출력 이벤트는 Pipeline을 통해 역순으로 처리

#### ChannelOutboundHandler 주요 메소드
1. **`bind(ChannelHandlerContext ctx, SocketAddress addr, ChannelPromise promise)`** : `Channel`이 특정 로컬 주소에 바인딩될 때 호출
2. **`connect(ChannelHandlerContext ctx, SocketAddress addr, SocketAddress addr2, ChannelPromise promise)`** : 원격 주소에 연결될 때 호출
3. **`disconnect(ChannelHandlerContext ctx, ChannelPromise promise)`** : 연결을 끊을 때 호출
4. **`close(ChannelHandlerContext ctx, ChannelPromise promise)`** : `Channel`이 close될 때 호출
5. **`deregister(ChannelHandlerContext ctx, ChannelPromise promise)`** : `EventLoop`로부터 `Channel`을 해제할 때 호출
6. **`read(ChannelHandlerContext ctx)`** : `Channel`로부터 더 많은 데이터를 read 요청할 때 호출
7. **`write(ChannelHandlerContext ctx, Object msg, ChannelPromise promise)`** : 데이터가 `Channel`을 통해 전송될 때 호출
8. **`flush(ChannelHandlerContext ctx)`** : `Channel`에 남아있는 데이터를 flush할 때 호출
    
ChannelInboundHandler와 ChannelOutboundHandler 모두 순수 인터페이스라 모든 메서드를 구현해줘야하는 불편함이 존재하는데, Netty는 편의를 위해 ChannelHandlerAdaptor 추상 클래스를 구현한 어탭터 클래스를 제공한다
   - ChannelInboundHandlerAdaptor: Inbound I/O Event 어댑터 구현체
   - ChannelOutboundHandlerAdaptor: Outbount I/O Operation 어댑터 구현체
   - ChannelDuplexHandler: Inbound, Outbound Event 처리용 어댑터 구현체(Inbound, Outbound 모두 처리 가능)

### 6. ChannelHandlerContext
ChannelHandlerContext는 ChannelPipeline 내에서 각 ChannelHandler가 다른 ChannelHandler와 상호작용할 수 있도록 돕는 객체이다

ChannelHandlerContext를 통해 다음 ChannelHandler에게 이벤트를 넘기거나, 동적으로 ChannelPipeline을 변경할 수 있다

#### ChannelHandlerContext 주요 메소드
1. **`channel()`** : 현재 Context와 연결된 채널 반환
2. **`pipeline()`** : 현재 Context의 ChannelPipeline을 반환
3. **`read()`** : `Channel`에서 데이터를 읽기 시작하도록 요청
4. **`write(Object msg)`** : 데이터를 다음 Handler로 전달
5. **`writeAndFlush(Object msg)`** : 데이터를 다음 Handler로 전달하고 즉시 flush
6. **`fireChannelRead(Object msg)`** : 현재 Handler 이후의 Handler로 데이터를 전파
7. **`fireExceptionCaught(Throwable)`** : 예외를 다음 Handler로 전달
8. **`close()`** : 현재 Channel 닫기
9. **`attr(AttributeKey<T>)`** : 현재 Channel의 속성(Attribute)을 읽거나 설정

