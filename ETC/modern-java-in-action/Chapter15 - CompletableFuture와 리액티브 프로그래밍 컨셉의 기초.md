최근 소프트웨어를 개발 방법을 획기적으로 뒤집는 두가지 추세가 존재한다 첫 번째는 애플리케이션을 작동시키는 하드웨어와 관련된 것이고 두 번째는 여러 애플리케이션을 합쳐 하나의 애플리케이션으로 만드는 형태인 매시업 형태이다

두 가지 형태 중 매시업 형태에 대해서 유의 깊게 생각해볼 것이다 

만약 여러 애플리케이션을 합쳐서 하나의 서비스를 제공한다고 생각해보자 예를 들어 우리는 영국의 축구 뉴스를 수집하고 번역하여 긍정적인 기사와 부정적인 기사의 수와 분석을 보여주는 애플리케이션을 개발한다고 가정해보자 

1. 영국의 축구 일간지 정보 크롤링
2. 축구 뉴스, 일간지 등의 정보 번역 (구글 번역 API)
3. 통계 및 분석 API (다른 전문 통계 및 분석 API)
4. 화면 보여주기

이러한 형태로 우리의 애플리케이션을 구성할 수 있다 하지만 여기서 중요한 점은 애플리케이션 자체에 국한된 API가 아닌 외부 구글 번역 API, 분석 API 두 가지를 사용했다 이러한 매시업 형태의 애플리케이션을 개발하게 된다면 몇 가지 문제가 발생할 수 있다

- 외부 API의 느린 반응, 응답
- 응답을 기다리는 동안의 자원 낭비
- 외부 API에서 발생하는 오류
- 외부 API와 자체 비즈니스로직의 호환 등

응답을 기다리는 동안의 자원 낭비에 대응 할 수 있는 프로그래밍 방법의 기초 컨셉을 알아보겠다

### 동시성을 구현하는 자바 지원의 진화

자바의 동시 프로그래밍 지원은 지난 20년간 하드웨어, 소프트웨어 시스템, 프로그래밍 컨셉의 큰 변화에 맞춰 진화해왔다 

> 진화 과정
> 
> - 자바 초창기
>     - Runnable과 Thread를 동기화된 클래스와 메소드를 이용
> - 자바 5
>     - ExecutorService 인터페이스를 사용하여 좀 더 표현력있는 동시성을 지원(스레드 실행과 테스크 제출을 분리)
>     - Runnable, Thread의 변형을반환하는 Callable<T>, Future<T> 등을 지원
> - 자바 7
>     - 포크/조인을 구현한 java.util.concurrent.RecursiveTask 지원
> - 자바 8
>     - 람다 지원에 기반한 병렬 프로세싱 추가
>     - Future를 조합하는 기능 추가
> - 자바 9
>     - 분산 비동기 프로그래밍 지원

Future와 분산 비동기 프로그래밍은 앞서 설명한 매시업 애플리케이션을 개발하는데 필수적인(내부를 조금 더 효율적으로 쓰기 위한) 기초 모델과 툴킷을 제공했다 이렇듯 응답을 기다리는 동안의 자원 낭비를 없애기 위한 기본적인 과정을 리액티브 프로그래밍이라 부르며 자바 9에서는 발행-구독 프로토콜(java.util.concurrent.Flow 인터페이스)로 이를 지원했다

<aside>
💡

동시성/병행성 VS 병렬성

동시성 or 병행성(Concurrency)

- 여러가지의 작업을 일부분만 수행하고 다음 작업에 넘어가고를 반복하여 처리
- 다른 작업으로 넘어가야 하기 때문에 Context Switching이 필요
- Context Switching은 매우 빠르게 일어나기 때문에 여러가지 작업을 동시에 수행하는 것 처럼 보이게됨

병렬성(Parallelism)

- 여러 작업이 동시에 처리
- 멀티 코어에서 멀티 스레드를 동작시키는 방식
- 동일한 시간에 독립적인 작업을 실행할 수 있으며 동시성과는 다르게 여러 작업을 
다른 코어, 프로세스 등에 동시에 실행
</aside>

### Executor와 스레드 풀

자바 5는 Executor 프레임워크와 스레드 풀을 통해 스레드의 힘을 높은 수준으로 끌어올리는 즉 프로그래머가 테스크 제출과 실행을 분리할 수 있는 기능을 제공했다

### 스레드의 문제

1. 자바 스레드는 직접 운영체제 스레드에 접근하며 운영체제 스레드를 만들고 종료하려면 비싼 비용(페이지 테이블과 관련한 상호작용)을 치러야 한다
2. 운영체제 스레드 숫자는 제한 되어 있으며 운영체제가 지원하는 스레드 수를 초과해서 사용하면 자바 애플리케이션이 예상치 못한 문제가 발생할 수 있다 
3. 하드웨어에 따라서 스레드를 명시적으로 지정하는 것이 문제의 소지가 생길 수 있다

> 스레드 숫자가 제한되어 있는 이유?
> 
> - 스레드를 무한정 만들게 되면 CPU시간, 메모리 공간 같은 시스템 자원이 고갈되게 된다
>     - 각 스레드는 CPU시간을 나누어서 사용해야 하기 때문에 CPU가 많아지면 성능이 떨어짐
>     - 스레드는 고유한 스택 메모리를 가지기 때문에 스레드가 너무 많으면 메모리가 고갈 될 수 있음
> - Context Switching 오버헤드가 발생한다
>     - Context Switching을 하게 되면 작업 전환에 필요한 정보를 저장하고 복원하는데 시간을 소비하기 때문에 스레드가 많아지면 Context Switching이 빈번하게 발생하여 오버헤드가 증가하고 시스템의 성능이 저하될 수 있음
> - 동기화 문제가 발생한다
>     - 스레드 간의 자원 경쟁이 심해져 동기화 문제가 발생하게 되고 이런 동기화 문제를 해결하기 위해 동기화 메커니즘이 복잡해지고 데드락 같은 문제가 발생할 가능성이 높아짐

### 스레드 풀

자바 ExecutorService는 태스크를 제출하고 나중에 결과를 수집할 수 있는 인터페이스를 제공하며 newFixedThreadPool 같은 팩토리 메소드 중 하나를 이용해 스레드 풀을 만들어 사용할 수 있다

쓰레드 풀이란 쓰레드를 미리 생성하고, 작업 요청이 발생할 때 마다 미리 생성된 쓰레드로 해당 작업을 처리하는 방식을 의미한다 이때 작업이 끝난 쓰레드는 종료되지 않으며 다음 작업 요청이 들어올 때까지 대기한다

스레드 풀 장점

1. 새 스레드를 만들어 주는것이 아닌 기존 스레드를 사용하므로 더 빠르다
2. 스레드를 생성하고 종료하는 오버헤드를 줄일 수 있다
3. 스레드의 갯수를 제한하여 동시에 처리할 수 있는 작업의 갯수를 제한할 수 있다 
    - 스레드 풀의 크기를 조절하여 과도한 작업 요청에 대한 성능 저하를 방지함
4. 다수의 요청을 빠르게 처리할 수 있다

스레드 풀 단점

1. 스레드를 많이 만들게 되면 메모리 낭비가 심하게 될 수 있다

> 스레드 풀 사용 시 주의 사항
> 
> 1. 스레드가 잠자거나 I/O를 기다리거나 네트워크 연결을 기다리는 테스크는 주의해야 한다
>     - 잠자거나 I/O를 기다리거나 하게 되면 해당 태스크는 스레드에 할당 되었지만 실질적으로 작업은 이루어지지 않는 상태
>     - 이렇게 되면 스레드 풀에 남은 스레드 들로 실행이 되어야 하기 때문에 효율이 떨어지게 되며 심한 경우 데드락이 발생할 수 있음
>     - 이런 태스크는 스레드 풀에 제출하지 않는 것이 좋음(하지만 이를 항상 지킬 수 있는 건 아님)
> 2. 스레드를 사용하면 종료하는 습관을 들여야한다 
>     - 자바는 중요 코드를 실행하는 스레드가 죽는 것을 방지하기 위해 main이 반환하기 전에 모든 스레드의 작업을 기다림
>     - 이렇기 때문에 사용이 완료된 스레드는 종료하는 습관을 들여야함
>     - 장기간 실행하는 스레드는 Thread.setDaemon으로 실행하여 프로그램이 종료될 때 같이 종료되록 함(main이 리턴될 때 같이 종료되도록)

<aside>
💡

데몬 스레드란?
데몬 스레드는 백그라운드에서 동작하며 주로 서비스 스레드의 보조 역할을 수행하거나 특정 작업을 주기적으로 처리하는 목적으로 사용됨

데몬 스레드는 프로그램이 종료될 때(메인 스레드가 종료되면) 자동으로 종료되어 명시적으로 종료하지 않아도됨

</aside>

### 스레드로 무엇을 바라는가?

모든 하드웨어 스레드를 활용해 병렬성의 장점을 극대화하도록 프로그램 구조를 만드는 것 즉 프로그램을 작은 태스크 단위로 구조화하는 것이 목표이다

### 동기 API와 비동기 API

아래와 같은 코드를 실행한다고 가정해보자 
아래와 같이 메소드를 실행하게 되면 동기적으로 하나의 메소드가 처리될 때 까지 다음 작업을 실행하지 않고 대기하게 된다 이러한 작업을 비동기적으로 변경해본다

```java
int f(int x); // 매우 오래걸리는 작업 1
int g(int x); // 매우 오래걸리는 작업 2 
...
int y = f(x);
int z = g(x);
System.out.println(y + z);
```

### Runnable을 활용한 코드

```java
class ThreadRunnableExample{ 
	public static void main(String[] args){
		int x = 1337;
		
		Result result = new Result();
		Thread t1 = new Thread(() -> {result.left = f(x)});
		Thread t2 = new Thread(() -> {result.right = g(x)});
		
		t1.start();
		t2.start();
		try {
			t1.join(); // 특정 스레드가 종료될 때까지 현재 스레드를 대기 상태로 만듬
			t2.join();
		} catch (InterruptedException e) {
        e.printStackTrace();
    }
		System.out.println(result.left + result.right);
	}
	
	private static class Result{
		private int left;
		private int right;
	}
}
/*
	단점
	 - 코드가 복잡하다
	
	join()
		- 특정 스레드가 종료될 때까지 현재 스레드를 대기 상태로 만듬
			즉, join()을 호출한 스레드가 완료되기 전까지는 다음 코드가 실행되지 않음
		- 여러 스레드를 동시에 실행할 때 특정 스레드가 종료된 후에만 
			다음 작업을 실행하고 싶을 때 사용됨(동기화 목적)
*/
```

### Future을 활용한 코드

```java
class ExecutorServiceExample{ 
	public static void main(String[] args){
		int x = 1337;
		Result result = new Result();
		
		ExecutorService executorService = Executor.newFixedThreadPool(2);
		Future<Integer> y = executorService.submit(() -> f(x));
		Future<Integer> x = executorService.submit(() -> g(x));
		System.out.println(x.get() + y.get());
		executorService.shutdown(); // 자원해제
	}
}
/*
	단점
		- 명시적인 submit(task) 같은 메소드 호출(불필요한 코드)가 추가 되었다
		- 작업의 결과를 얻어오려면 잠시라도 블로킹 상태로 들어가야 한다
		
	비동기 작업
	- executorService.submit(() -> f(x))와 executorService.submit(() -> g(x))는 
		각각 비동기적으로 실행됨 즉, 두 작업은 동시에(또는 거의 동시에) 시작되어 
		별도의 스레드에서 실행됨
		
	동기적 결과 처리
		- y.get()와 x.get()을 호출하면 해당 작업이 완료될 때까지 메인 스레드는 대기(블로킹)함 
			get()은 작업이 완료될 때까지 기다렸다가 결과를 반환(블로킹)
			즉 모두 완료될 때까지 메인 스레드는 다음 줄의 코드를 실행하지 못함
		
	Future
		- 비동기 작업의 결과를 나타내는 객체로 비동기적으로 실행된 작업의 결과를 얻을 수 있음
		- isDone() : 작업 완료여부 확인
		- cancel() : 완료되지 않은 작업 취소
		- get() : 작업의 결과 리턴, 작업이 완료될 때 까지 대기하며 완료된 후 결과 리턴
		- get(long timeout, TimeUnit unit) : 지정된 시간까지 기다린 후 결과를 반환하지 못하면
			타임아웃 발생
*/
```

위의 단점들은 스트림을 이용해 내부반복으로 바꾼것처럼 비슷한 방법으로 이 문제를 해결해야한다 문제의 해결은 비동기 API라는 기능으로 API를 바꿔서 해결할 수 있다

### 비동기 API

첫번째 방법인 자바의 Future를 이용하면 이 문제를 조금 개선 할 수 있다 이 대안을 적용 시키면 f, g의 시그니처가 다음처럼 바뀐다

```java
Future<Integer> f(int x);
Future<Integer> g(int x);
```

메서드 f와 g는 호출 즉시 자신의 원래 바디를 평가하는 태스크를 포함하는 Future을 반환한다 하지만 조금 큰 프로그램에서는 두가지로 이유로 이런 방식을 사용하지 않는다

- 다른 상황에서는 g에도 Future형식이 필요할 수 있으므로 API의 형식을 통일하는 것이 바람직하다
- 병렬 하드웨어로 프로그램 실행속도를 극대화하려면 여러 작은 하지만 합리적인 크기의 태스크로 나누는 것이 좋다

결국 f 가 오래걸리는 작업이라면 결국 병합 get()과정에서 블록이 되어 기다려야 한다 과연 좋은 걸까?

### **리액티브 형식 API**

핵심은 f, g의 시그니처를 바꿔 콜백 형식의 프로그래밍을 이용하는 것이다

```java
void f(int x, IntConsumer dealWithResult);
...

public static void main(String[] args) {
    int x = 1337;
    Result result = new Result();
    f(x, (y) -> {
        result.left = y;
        System.out.println(result.left + result.right);
    });
    g(x, (y) -> {
        result.right = y;
        System.out.println(result.left + result.right);
    });
}

static void f(int x, IntConsumer dealWithResult) {
    dealWithResult.accept(x);
    System.out.println("x 실행");
}

static void g(int x, IntConsumer dealWithResult) {
    dealWithResult.accept(x);
    System.out.println("g 실행");
}
...

class Result {
    public int left;
    public int right;
}

/* result 
		1337
		x 실행
		2674
		g 실행
*/
```

하지만 결과가 정확하게 출력되지 않는다 이유는 상황에 따라 먼저 계산을 실행하고 그 결과를 출력하기 때문이다 이 문제를 보완할 수 있는 방법은 없을까?

- if-then-else를 이용하여 적절하게 락을 이용해 두 콜백이 모두 호출 되었는지 확인한 다음 println을 실행할 수 있다
- 리액티브 형식의 API는 보통 한 결과가 아니라 일련의 이벤트에 반응하도록 설계되었으므로 Future을 이용하는 것이 더 적절하다

### 잠자기(기타 블로킹 동작)는 해로운 것으로 간주

우리는 sleep()이라는 스레드의 잠자기 메서드를 종종 사용하는 것을 보았다 과연 이 잠자기는 좋은 것일까? 사람이 잠을 잘 때에도 몸은 쉬지 않고 일을 한다 스레드도 마찬가지다 잠을 자도 자신이 점유하는 자원을 반환하지 않는다 가지고 자는 것이다 이것은 자원점유로 인하여 낭비로 이어진다 또 자바에선 블록이 있다 잠자기와 비슷하지만 스레드가 일을 하는 동안 다른 스레드는 잠시 멈춘다 이것또한 낭비다 그럼 어떻게 해야할까?

```java
// sleep()을 사용한 경우
public class Main {
    public static void main(String[] args) throws InterruptedException {
        work1();
        Thread.sleep(2000); // 자는동안도 스레드 자원 낭비 중
        work2();
    }

    static void work1() {
        System.out.println("work 1 실행");
    }

    static void work2() {
        System.out.println("work 2 실행");
    }
}

// 스케줄링을 이용한 경우
public class Main {

    public static void main(String[] args) throws InterruptedException {
        ScheduledExecutorService scheduledExecutorService =
                Executors.newScheduledThreadPool(1);
        work1();
        scheduledExecutorService.schedule(Main::work2, 2, TimeUnit.SECONDS);
        scheduledExecutorService.shutdown();
    }

    static void work1() {
        System.out.println("work 1 실행");
    }

    static void work2() {
        System.out.println("work 2 실행");
    }
}

/*
	두 코드 모두 스레드 풀에서 실행된다고 가정
	
	sleep 사용
		- work1()을 실행하고 10초 동안 잠에 든 후 일어나서 
			work2() 작업을 실행한 뒤 종료 및 워커 스레드를 해제
		- 10초동안 아무일도 하지 않고 자원을 점유
		
	스케줄러 사용
		- work1() 실행 후 종료한 뒤 work2()가 10초 뒤에 실행될 수 있도록 큐에 추가
	  - 다른 작업을 허용할 수 있도록 함(대신 큐에 저장을 했기 때문에 메모리는 조금 더 사용)
	  
	태스크를 만들 때 주의해야할 점
		1. 태스크를 실행하면 자원을 점유하므로 태스크가 끝날 때 까지 계속 실행시켜야 한다
		2. 태스크를 블록하는 것 보다 다음 작업을태스크로 제출하고 현재 태스크를 종료하는 것이
		   바람직하다
		3. 하지만 태스크를 모두 비동기로 만드는 것은 좋지 않다
			 개선된 동시성 API를 사용하는 것도 방법이다
*/
```

### 비동기 API에서 예외는 어떻게 처리하는가?

Future나 리액티브 형식의 비동기 API에서 호출된 메소드의 실제 바디는 별도의 스레드에서 호출되며 이때 발생하는 어떤 에러는 이미 호출자의 실행 범위와는 관계가 없는 상황이 된다 이렇게 예상치 못한 상황이 발생하면 예외를 발생시켜 다른 동작이 실행되도록 해야한다 어떻게 이를 실현할 수 있을까?

```java
void f(int x, Consumer<Integer> dealWithResult, Consumer<Throwable> dealWithException)
void f(int x, Subscript<Integer> s) // 보통이렇게 인수가 많아지면 한 객체로 감싼다 

/*
	Future을 구현한 CompletableFuture에서는 런타임 get() 메소드에 예외를 처리할 수 있는
	기능을 제공하며 예외에서 회복할 수 있도록 exceptionally() 같은 메소드도 제공한다
*/
```

## **CompletableFuture와 콤비네이터를 이용한 동시성**

Java 8에서는 Future 인터페이스의 구현인 CompletableFuture를 이용하여 Future를 조합할 수 있는 기능을 추가했다 일반적으로 Future는 실행하여 get()으로 결과를 얻을 수 있는 Callable로 만들어진다 하지만 CompletableFuture는 실행할 코드 없이 Future를 만들수 있고 complete() 메서드를 활용하여 나중에 어떤 값을 이용해 다른 스레드가 이를 완료할 수 있고 get()으로 결과를 얻을수 있도록 허용한다

thenCombine 사용 전

```java
public class Main {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService executorService = Executors.newFixedThreadPool(10);
        int x = 1337;

        CompletableFuture<Integer> a = new CompletableFuture<>();
        executorService.submit(() -> a.complete(f(x)));

        int b = g(x);
        System.out.println(a.get() + b);

        executorService.shutdown();
    }

    private static int f(int x) {
        return 0;
    }

    private static int g(int x) {
        return 0;
    }
}

/*
	f가 끝나지 않는다면 get()을 가다려야 하므로 프로세싱 자원 낭비 가능성이 있다
	CompletableFuture를 사용하면 해결할 수 있다
	thenCombine() 메서드를 사용함으로써 두 연산 결과를 효과적으로 사용할 수 있다
*/

... 
CompletableFuture<V> thenCombine(CompletableFuture<U> other, BiFunction<T, U, V> fn)
/*
	두 개의 CompletableFuture를 받아 한개의 새 값을 만든다
	처음 두 작업이 끝나면 두 결과 모두에 fn을 적용하고 블록하지 않은 상태로 
	결과 Future를 반환한다
*/
```

thenCombine 사용 후 

```java
public class Main {

    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService executorService = Executors.newFixedThreadPool(10);
        int x = 1337;

        CompletableFuture<Integer> a = new CompletableFuture<>();
        CompletableFuture<Integer> b = new CompletableFuture<>();
        CompletableFuture<Integer> c = a.thenCombine(b, Integer::sum);
				
				// a,b의 연산이 마무리가 되어야 c연산 수행
        executorService.submit(() -> a.complete(f(x)));
        executorService.submit(() -> b.complete(g(x)));

        System.out.println(c.get());
        executorService.shutdown();
    }

    private static Integer f(int x) {
        return 0;
    }

    private static Integer g(int x) {
        return 0;
    }
}
/*
	Future a, Future b의 결과를 알지 못한 상태에서 thenCombine()은 두 연산이 끝났을 때 
	스레드 풀에서 실행된 연산을 만든다
	결과를 추가하는 세 번째 연산 c는 두 작이 끝날 때까지는 스레드에서 실행되지 않는다
		
	동작 순서 
	1. a.complete(f(x));
	2. b.complete(g(x));
	3. c = a.thenCombine(b, Integer::sum);
	4. c.get();
	
	차이점 
		- 기존에 Future을 사용할때는 f와 g의 실행이 종료되지 않으면 
		  합산 연산은 블록된 상태로 자원을 잡아먹고 있는다
		  하지만 이 방법의 경우 실행이 되지 않고 f와 g가 끝나면 그때 실행이 이루어 지는 것이다
		  결국 자원을 아예 사용하지 않는 것이다
*/
```

### **발행-구독 그리고 리액티브 프로그래밍**

Future와 CompletableFuture은 독립적 실행과 병렬성이라는 정식적 모델에 기반한다 연산이 끝나면 get()으로 Future의 결과를 얻을 수 있다. 따라서 Future은 한번만 실행된다

반면 리액티브 프로그래밍은 시간이 흐르면서 여러 Future 같은 객체를 통해 여러 결과를 제공한다

자바 9에서는 java.util.concurrent.Flow의 인터페이스에 발행-구독 모델을 적용해 리액티브 프로그래밍을 제공한다 자바 9 플로 API는 좀 더 자세히 살펴보겠지만 간단히 다음 3가지로 API를 정리할 수 있다

- 구독자가 구동할 수 있는 발행자
- 이 연결을 구독이라 한다
- 이 연결을 이용해 메시지를 전송한다

두 플로우 합치는 예제

```java
public static void main(String[] args) {
    SimpleCell c1 = new SimpleCell("c1");
    SimpleCell c2 = new SimpleCell("c2");
    ArithmeticCell c3 = new ArithmeticCell("c3");
		// Subscriber 구현체를 가져옴
    Subscriber<? super Integer> left = c3::setLeft; 
    Subscriber<? super Integer> right = c3::setRight;
    
		// Subscriber 구현체를 저장
    c1.subscribe(left);
    c2.subscribe(right);
	
    c1.onNext(10);
    c2.onNext(120);
}
...
public interface Publisher<T>{
    void subscribe(Subscriber<? super T> subscriber);
}
...
public interface Subscriber<T>{
    void onNext(T t);
}

... // 셀 객체
public class SimpleCell implements Publisher<Integer>, Subscriber<Integer>{
	  private int value = 0;
    private String address;
    private List<Subscriber> subscribers = new ArrayList<>();

    public SimpleCell(String address) {
        this.address = address;
    }

    private void notifyAllSubscribers(){
        subscribers.forEach(subscribers -> subscribers.onNext(this.value));
        // onNext() 실행시에 Subscriber의 setLeft, setRight 실행
    };
    @Override
    public void subscribe(Subscriber<? super Integer> subscriber) {
        subscribers.add(subscriber);
    }

    @Override
    public void onNext(Integer integer) {
        this.value = integer;
        System.out.printf("%s : %d%n", this.address, this.value);
        notifyAllSubscribers();
    }

    void setNewValue(Integer newValue){
        System.out.println("setNewValue 진입");
        this.value = newValue;
        System.out.printf("%s : %d%n", this.address, this.value);
    }
}

... // 합쳐지는 셀 객체
class ArithmeticCell extends  SimpleCell{
    private int left;
    private int right;

    public ArithmeticCell(String address) {
        super(address);
        System.out.println("ArithmeticCell 생성자");
    }

    public void setLeft(int left) {
        System.out.print("setLeft");
        this.left = left;
        setNewValue(left + this.right);
    }

    public void setRight(int right) {
        System.out.println("setRight");
        this.right = right;
        setNewValue(this.left + right);
    }
}
```

<aside>
💡

데이터가 발행자(생산자)에서 구독자(소비자)로 흐름에 착안해 개발자는 이를 업스트림 또는 다운스트림이라 부른다 위 예제에서 데이터는 newValue는 업스트림 onNext메서드로 전달되고 notifyAllSubscriber 호출을 통해 다운스트림 onNext 호출로 전달된다

</aside>

### 역압력

매 초마다 수천개의 메시지가 onNext로 전달된다면 빠르게 전달되는 이벤트를 아무 문제 없이 처리할 수 있을까? 이러한 상황을 압력(pressure)이라 부른다 이럴때는 정보의 흐름 속도를 제어하는 역압력 기법이 필요하다 역압력은 Subscriber가 Publisher로 정보를 요청할 때만 전달할수 있도록 한다

```java
 interface Subscriber<T> {
		 void onSubscribe(Subscription subscription) // 여기를 통해서 정보를 보낼 수 있음
 }
 /*
	 Publisher와 Subscriber 사이에 채널이 연결되면(구독을 하게 된다면) 
	 첫 이벤트로 이 메소드가 호출된다
	 Subscription 객체는 Subscriber와 Publisher와 통신할 수 있는 메소드를 포함한다
 */
 
 
interface Subscription {
		void cancel();
		void request(long n);
}

/*
	콜백을 통하여 '역방향' 소통 효과에 주목하자
	Publisher는 Subscription 객체를 만들어 Subscriber로 전달하면 
	Subscriber는 이를 이용해 Publisher로 정보를 보낼 수 있다
*/
```

### 실제 역압력의 간단한 형태

- Subscriber가 OnSubscribe로 전달된 Subscription(객체이름) 객체를 subscription(지역변수) 같은 필드에 로컬로 저장한다
- Subscriber가 수많은 이벤트를 받지 않도록 onSubscribe, onNext, onError의 마지막 동작에 channel.request(1)을 추가해 오직 한 이벤트만 요청한다
- 요청을 보낸 채널에만 onNext, onError 이벤트를 보내도록 Publisher의 notifyAllSubscribers 코드를 바꾼다 (보통 여러 Subscriber가 자시만의 속도를 유지할 수 있도록 Publisher는 새 Subscription을 만들어 각 Subscriber와 연결한다)

구현이 간단해 보일 수 있지만 역압력을 구현하려면 여러가지 장단점을 생각해야한다

- 여러 Subscriber가 있을 때 이벤트를 가장 느린 속도로 내보낼 것인가? 아니면 각 Subscriber애게 보내지 않은 데이터를 저장할 별도의 큐를 가질 것인가?
- 큐가 너무 커지면 어떻게 해야할까?
- Subscriber가 준비가 안 되었다면 큐의 데이터를 폐기할 것인가?

**정리**

- 자바의 동시성 지원은 계속 진화하고 있다 스레드 풀은 보통 유용하지만 블록되는 태스크가 많다면 다시 생각해보자
- 메소드를 비동기(결과를 처리하기전에 반환)로 만들면 병렬성을 추가할 수 있으며 부수적으로 루프를 최적화한다
- 박스와 채널모델을 이용해 비동기 시스템을 시각화할 수 있다
- 자바 8 CompletableFuture 클래스와 자바 9 플로우 API 모두 박스와 채널 다이어그램으로 표현 할 수 있다
- CompletableFuture 클래스는 한 번의 비동기 연산을 표현한다 콤비네이터로 비동기 연산을 조합함으로 Future를 이용할 때 발생했던 기존의 블로킹 문제를 해결 할 수 있다
- 플로우 API는 발행-구독 프로토콜, 역압력을 이용하면 자바의 리액티브 프로그래밍의 기초를 제공한다
- 리액티브 프로그래밍을 이용해 리액티브 시스템 즉, 반응성, 회복성, 탄력성을 가진 시스템을 구현할 수 있다
