### 병렬 스트림

병렬 스트림이란 각각의 스레드에서 처리할 수 있도록 스트림 요소를 여러 청크로 분할한 스트림을 말한다 따라서 병렬 스트림을 이용하면 모든 멀티코어 프로세서가 각각의 청크를 처리하도록 할당할 수 있다

```java
// 병렬 스트림 사용 x
private static long sequentialSum(long n) {
    return Stream.iterate(1L, i -> i + 1)
            .limit(n)
            .reduce(0L, Long::sum);
}

// 병렬 스트림 사용
private static long parallelSum(long n) {
    return Stream.iterate(1L, i -> i + 1)
            .limit(n)
            .parallel() // 병렬 스트림으로 변환
            .reduce(0L, Long::sum);
}

/*
	parallel을 사용하여 병렬 스트림으로 변환 후 리듀싱 연산을 여러 청크에 병렬로 수행 후
	리듀싱 연산으로 생성된 부분 결과를 다시 리듀싱 연산으로 합쳐서 전체 스트림의 결과를 도출
*/

/*
	순차 스트림에 parallel을 호출해도 스트림 자체에는 아무 변화도 일어나지 않는다
	내부적으로 parallel을 호출하면 이후 연산이 병렬로 수행함을 의미하는 불리언 플래그가 
	설정된다 
*/
```

### 스트림 성능 측정

병렬화를 이용하면 순차나 반복 형식에 비해 성능이 더 좋아질 것이라 추측했다

하지만 병렬화를 사용한다 해도 박싱으로 인한 오버헤드가 지속적으로 발생하는 작업이나 반복작업과 같은 독립단위로 나누기 어려운 작업은 병렬화를 사용하는 것이 오히려 성능에 악영향을 끼친다

> 병렬 스트림을 사용하지 말하야 하는 작업
> 
> - 박싱된 객체가 만들어지는 작업
>     - 숫자를 더하는 작업 → 위 코드의 parallelSum 같은 작업
> - 반복작업과 같은 독립단위로 나누기 어려운 작업은 병렬 스트림에 부적합
>     - 공유된 값을(누적자 같은) 사용하는 경우 → ex) result += num
> - 완성되지 못한 컬렉션을 이용해야하는 경우

> 병렬 스트림을 잘 사용하는 방법
> 
> - 박싱과 언박싱 오버헤드가 발생하지 않도록 함
>     - ex) LongStream과 같은 기본 특화형 스트림 사용
> - 코어간 데이터 전송 시간보다 훨씬 오래걸리는 작업만 병렬로 수행

### 병렬 스트림 효과적으로 사용하기

- 직접 측정하라, 확신이 서지 않으면 직접측정을 통하여 실제로 더 빠르고 효율적인 것을 찾아라
- 박싱을 주의하라 되도록이면 기본형 특화 스트림을 사용하자
- 순차 스트림보다 병렬 스트림에서 성능이 떨어지는 연산들을 피하라 limit나 findFirst처럼 요소의 순서에 의존하는 연산을 병렬 스트림에서 수행하려면 비싼 비용을 치러야 한다
- 스트림에서 수행하는 전체 파이프라인 비용을 고려하라 처리해야할 요소 수가 N, 하나의 요소를 처리하는 데 드는 비용이 Q라하면 전체 스트림 파이프라인 처리비용은 N*Q로 예상할 수 있다 Q가 높아진다는 것은 병렬 스트림으로 성능을 개선할 수 있는 가능성이 있음을 의미한다.
- 소량의 데이터에서는 병렬 스트림이 도움되지 않는다 병렬화 과정에서 생기는 부가 비용을 상쇄할 수 있을 만큼의 이득을 얻지 못하기 때문이다
- 스트림을 구성하는 자료구조가 적절한지 확인하라 예로, ArrayList는 LinkedList보다 효율적으로 분할할 수 있다 또한 range 팩토리 메서드로 만든 기본형 스트림도 쉽게 분해할 수 있다 또한 **Spliterator**를 구현해서 분해 과정을 완벽하게 제어할 수 있다
- 병합비용을 고려하라, 병합비용이 복잡하다면 병렬 스트림을 이용한 이득은 줄어들 것이다 (예를 들어 병합을 위해 객체를 생성하고 캐스팅이 필요하다면?) 기본형에 맞는 작업이 우선적으로 병렬 스트림을 적용하는 곳이 될 것이다

### 포크/조인 프레임워크

포크/조인 프레임워크는 병렬화할 수 있는 작업을 재귀적으로 작은 작업으로 분할한 다음에 서브태스크 각각의 결과를 합쳐서 전체 결과를 만들도록 설계되었다 그래서 포크조인 프레임워크는 서브태스크를 스레드 풀의 작업자 스레드에 분산하는 **ExecutorService인터페이스**를 구현해야한다

### RecursiveTask 활용

쓰레드 풀 이용을 위해선 RecursiveTask의 서브클래스 혹은 RecursiveAction의 서브클래스를 만들어야 한다 RecursiveTask의 R은 병렬화된 태스크가 생성하는 결과 형식을 의미하면 RecursiveAction은 결과 형식이 없을 경우에 사용한다

RecursiveTask를 이용하기 위해선 compute 메소드를 구현해야 한다 compute 메소드는 태스크를 서브태스크로 분할하는 로직과 더 이상 분할할 수 없을 때 개별 서브태스크의 결과를 생산할 알고리즘을 정의한다

> ForkJoinFramework API
> 
> - ForkJoinTask<V> :  ForkJoinPool 내에서 실행되는 작업을 정의하는 추상 클래스
> - ForkJoinPool : ForkJoinTask 실행을 관리하는 스레드 풀
> - RecursiveAction : 반환값이 없는 작업을 구현할 때 사용하는 ForkJoinTask 하위 클래스
> - RecursiveTask : 반환값이 있는 작업을 구현할 때 사용하는 ForkJoinTask 하위 클래스

### ForkJoinPool

ForkJoinTask를 실행하기 위한 스레드 풀이다 태스크를 생성하면 생성한 ForkJoinTask를 ForkJoinPool의 invoke 메소드로 전달한다 일반적으로 애플리케이션은 둘 이상의 ForkJoinPool을 사용하지 않는다 소프트웨어의 필요한 곳에서 언제든 가져다 쓸 수 있도록 ForkJoinPool을 한 번만 인스턴스화해서 정적 필드에 싱글턴으로 저장한다 기본 풀의 크기는 `Runtime.availableProcessors`의 반환값으로 결정된다 이 값은 사용할 수 있는 프로세서의 갯수를 말하지만, 실제로는 프로세서외에 하이퍼스레딩과 관련된 가상 프로세스도 개수에 포함된다 

요약
- 일반적으로 애플리케이션에서 둘 이상의 ForkJoinPool을 사용하지 않는다
- ForkJoinPool을 한번만 인스턴스화해서 정적필드에 싱글턴으로 저장 사용
- RunTime.availableProcessors의 반환값으로 풀에 사용할 스레드 수 결정
- 포크/조인 프레임워크는 분할 정복 알고리즘의 병렬화 버전이다

```java
// 구현
public class Task extends RecursiveTask<Long> {
    private final long[] numbers;
    private final int start;
    private final int end;
    private final long THRESHOLD = 20;

    public Task(long[] numbers){
        this(numbers, 0, numbers.length);
    }
    private Task(long[] numbers, int start, int end){
        this.numbers = numbers;
        this.start = start;
        this.end = end;
    }

    @Override
    protected Long compute() {
        int length = end - start;
        if (length <= THRESHOLD) {
            return computeSequentially();
        }
        Task left = new Task(numbers, start, start + length / 2); // start부터 절반까지
        left.fork(); // 다른 스레드로 할당
        Task right = new Task(numbers, start + length / 2, end); // 절반부터 end 까지
        Long rightResult = right.compute(); //현재스레드 실행 - 추가 분할 가능성 있음
        Long leftResult = left.join();
        return leftResult + rightResult;
    }

    private long computeSequentially(){
        long sum = 0;
        for (int i = start; i < end; i++){
            sum += numbers[i];
        }
        return sum;
    }

}

// 호출 
public class Main {
    public static void main(String[] args) {
        final long l = forkJoinSum(100);
        System.out.println(l);
    }

    public static long forkJoinSum(long n){
        long[] numbers = LongStream.rangeClosed(1, n).toArray();
        ForkJoinTask<Long> task = new Task(numbers);
        return new ForkJoinPool().invoke(task);
    }
}
```

> fork() & join() & invoke()
> 
> - fork() : 해당 작업을 쓰레드 풀의 작업 큐에 넣는다 -> 업무를 분담 (비동기 메소드)
> - join() : 해당 작업의 수행이 끝날 때까지 기다렸다가, 수행이 끝나면 그 결과를 반환한다 -> 분담한 업무의 결과를 취합 (동기 메소드)
> - invoke() : 단일 호출로 fork() 와 Join()의 결합 작업을 시작하고 작업이 끝날 때까지 기다렸다가 결과를 반환

### 포크/조인 프레임워크를 제대로 사용하는 방법

- join 메소드를 태스크에 호출하면 태스크가 생산하는 결과가 준비될 때까지 호출자를 블록시킨다  join 메소드는 두 서브태스크가 모두 시작된 다음에 호출하자
- RecursiveTask 내에서는 ForkJoinPool의 invoke 메소드를 사용하지 말아야 한다 대신 compute나 fork 메소드를 호출하자
- 왼쪽 작업과 오른쪽 모두에 fork메소드를 사용하는 것대신, 한쪽 작업에 compute를 호출하자 두 서브태스크의 한 태스크에는 같은 스레드를 재사용할 수 있으므로 풀에서 불필요한 태스크를 할당하는 오버헤드를 줄일 수 있다
- 디버깅이 어렵다는 점을 고려하자
- 각 서브태스크의 실행시간은 새로운 태스크를 포킹하는 데 드는 시간보다 길어야 한다

### 작업 훔치기

포크/조인 프레임워크에서는 작업 훔치기(work stealing)라는 기법을 사용한다. 각각의 스레드는 자신에게 할당된 태스크를 포함하는 이중 연결 리스트(doubley linked list)를 참조하면서 작업이 끝날 때마다 큐의 헤드에서 다른 태스크를 가져와서 작업을 처리한다. 이때 한 스레드는 다른 스레드보다 자신에게 할당된 태스크를 더 빨리 처리할 수 있는데, 할일이 없어진 스레드는 유휴 상태로 바뀌는 것이 아니라 다른 스레드의 큐의 꼬리에서 작업을 훔쳐온다. 모든 태스크가 작업을 끝낼 때 까지 이 과정을 반복한다. 따라서 태스크의 크기를 작게 나누어야 작업자 스레드 간의 작업부하를 비슷한 수준으로 유지할 수 있다.

### Spliterator 인터페이스

자바 8에서 Spliterator가 등장했다 Spliterator는 분할할 수 있는 반복자(spliatable iterator)라는 의미다 자바 8은 컬렉션 프레임워크에 포함된 모든 자료구조에서 사용할 수 있는 디폴드 Spliterator 구현을 제공한다

```java
public interface Spliterator<T> {
	boolean tryAdvance(Consumer<? super T> action);
	Spliterator<T> trySplit();
	long estimateSize();
	int characteristics();
}
/*
	T : Spliterator에서 탐색하는 요소의 형식
	tryAdvance : Spliterator의 요소를 하나씩 순차적으로 소비하면서 탐색해야 할 요소가 남아있으면 참을 반환한다
	trySplit : Spliterator의 일부 요소를 분할해서 두 번째 Spliterator를 생성하는 메소드
	estimateSize : 탐색해야 할 요소 수 정보를 제공한다
	characteristics : Spliter의 특성을 의미한다
*/
```

### 정리

- 내부 반복을 이용하면 명시적으로 스레드를 사용하지 않고도 병렬처리를 할 수 있다
- 병렬이 늘 빠른것이 아니다
- 요소처리의 비용이 높을때 병렬처리는 높은 효율을 가진다
- 자료구조가 병렬로 처리하는 것보다 성능에 큰영향을 미칠 수 있다
- 포크조인을 이용하여 병렬화를 이룰 수 있다
- Spliterator을 이용하여 스트림을 어떻게 병렬화할 것인지 정의할 수 있다
