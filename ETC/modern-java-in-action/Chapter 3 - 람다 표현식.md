### 람다란 무엇인가?

메소드로 전달할 수 있는 익명 함수를 단순화 한 것이라고 할 수 있다
람다를 사용하면 동작 파라미터 형식의 코드를 더 쉽게 구현할 수 있다

```java
// 람다 사용 전
Comparator<Apple> byWeight = new Comparator<Apple>() {
 public int compare(Apple a1, Apple a2) {
	 return a1.getWeight().compareTo(a2.getWeight());
  }
};

// 람다 사용 후 
Comparator<Apple> byWeight = (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
```

람다는 크게 세 부분으로 이루어 진다

1. 파라미터 리스트
    - 위 코드에서는 Apple a1, Apple a2 으로 메소드 파라미터를 의미
2. 화살표
    - 화살표는 람다의 파라미터 리스트와 바디를 구분
3. 람다 바디
    - 람다의 반환값에 해당하는 표현식
- 람다 문법
    - (타입 매개변수, ...) -> {실행문; ...}
        - 타입 생략 가능
        - 매개변수 생략 가능
        - 실행문이 하나면 {} 생략 가능
        - 매개변수가 없다면 () 생략 가능

### 함수형 인터페이스

함수형 인터페이스란 오직 하나의 추상 메소드를 가지고 있는 인터페이스를 의미한다
자바 8부터는 디폴트 메소드(인터페이스 내부에서 로직을 포함한 메소드를 의미)가 추가되었지만 함수형 인터페이스는 하나의 추상 메소드를 가진 것을 의미 하기 때문에 디폴트 메소드가 있어도 추상 메소드가 하나 이면 함수형 인터페이스로 인정한다

람다를 사용하게 되면 함수형 인터페이스의 추상 메소드 구현을 직접 전달할 수 있으므로 전체 표현식을 함수형 인터페이스의 인스턴스로 취급할 수 있다

```java
Runnable r1 () -> System.out.println("Hello world"); // 람다 사용

Runnable r2 = new Runnable() { // 익명 클래스 사용
	public void run() {
		System.out.println("Hello world");
	}
};

public static void process(Runnable r) {
	r.run();
}

process(r1);
process(r2);
process(() -> System.out.println("Hello world")); // 직접 람다로 전달
```

### 함수 디스크립터

함수형 인터페이스의 추상 메소드 시그니처(메서드 명과 파라미터의 순서, 타입, 개수를 의미)는  람다 표현식의 시그니처를 가리킨다 람다 표현식의 시그니처를 서술하는 메서드를 함수 디스크립터라고 한다 즉 함수가 어떤 입력값을 받고 어떤 반환값을 주는지에 대한 설명을 람다 표현식 문법으로 표현한 것을 의미한다

```java
// 함수 디스크립터 예제
() -> void // 인수가 없고 void 형식
(Apple, Apple) -> int // 2개의 Apple 인자를 받고 int를 반환
```

> @FunctionalInterface란?
> 
> - 함수형 인터페이스를 가르키는 어노테이션으로 함수형 인터페이스로 선언했지만 추상 메소드가 1개 이상이라면 컴파일 에러를 발생시킨다

### 람다 활용 : 실행 어라운드 패턴

실제 자원을 처리하는 코드를 설정과 정리 두 과정이 둘러싸는 형태를 실행 어라운드 패턴이라고 한다 

```java
// java 7부터 추가된 try-with-resources 
// try-with-resources는 finally에서 close를 해주지 않아도 자동으로 자원을 close 해줌
public String processFile() throws IOException {
    try (BufferedReader br = new BuffueredReader(new FileReader("data.txt"))) {
        return br.readLine(); // 실제 작업하는 코드
    }
}
```

현재 코드는 1줄을 읽어오는 형태이다 하지만 1줄 이상을 읽어오는 등 여러가지 요구사항에 대응할 수 없다 변화에 대응이 가능한 형태로 변경해야 한다

1. 함수형 인터페이스 생성
    
    함수형 인터페이스를 사용하여 동작 파라미터 방식을 적용한다
    
    ```java
    // 람다는 코드를 넘기는 기술로 코드를 넘기는 그릇인 함수형 인터페이스를 생성
    @FunctionalInterface // 함수형 인터페이스 생성
    public interface BufferedReaderProcessor {
        String process(BufferedReader bufferedReader) throws IOException;
    }
    ```
    
2. 동작 단계 실행
    
    BufferedReaderProcessor에 (BufferedReader) → String과 일치하는 메소드 시그니처를 전달할 수 있다
    
    ```java
     public String processfile(BufferedReaderProcessor brp) throws IOException {
    		//SetUp & CleanUp
        try(BufferedReader br = new BufferedReader(new FileReader("data.txt"))){ 
            return brp.process(br); // Process
        }
    }
    ```
    
3. 람다 전달
    
    람다를 사용하여 메소드 시그니처와 일치하는 람다 표현식을 사용할 수 있다
    
    ```java
    processFile((BufferedReader br) -> br.readLine());
    processFile((BufferedReader br) -> br.readLine() + br.readLine());
    ```
    

### 함수형 인터페이스 사용

함수형 인터페이스의 추상 메서드 시그니처를 함수 디스크립터라고 한다. 람다 표현식을 다양하게 사용하려면 공통 함수 디스크립터를 기술하는 함수형 인터페이스 집합이 필요하다. 자바 8 에서는 이를 위해 java.util.function 패키지로 다양한 함수형 인터페이스를 제공하고 있다.

### Predicate

java.util.function.Predicate<T> 는 제네릭 형식의 T 객체를 인수로 받아 불리언을 반환하는 test 메소드를 정의한다.

```java
@FunctionalInterface
public interface Predicate<T> {
    boolean test(T t);
}

Predicate<String> nonEmptyStringPredicate = (String s) -> !s.isEmpty();
```

### Consumer

java.util.function.Consumer<T> 제네릭 형식 T 객체를 받아서 void를 반환하는 accept라는 추상 메소드를 정의한다 T형식의 객체를 인수로 받아서 어떤 동작을 수행하고 싶을 때 사용할 수 있다

```java
@FuncationalInterface
public interface Consumer<T> {
	void accept(T t);
}

public void <T> void forEach(List<T> list, Consumer<T> c) {
    for (T t : list) {
        c.accept(t);
    }
}

forEach(
  Arrays.asList(1,2,3,4,5),
  (Integer i) -> System.out.println(i); // <- Consumer의 accept 메서드를 구현하는 람다
);
```

### Function

java.util.function.Consumer<T, R> 인터페이스는 제네릭 형식 T를 인수로 받아서 제네릭 형식 R 객체를 반환하는 추상 메소드 apply를 정의한다 입력을 출력으로 매핑하는 람다를 정의할 때 사용할 수 있다

```java
@FunctionalInterface
public interface Function<T, R> {
	R apply(T t);
}

public <T, R> List<R> map(List<T> list, Function<T, R> f) {
    List<R> result = new ArrayList<>();
    for (T t : list) {
        result.add(f.apply(t));
    }
    return result;
}

List<Integer> l = map(
    Arrays.asList("lambdas", "in", "action"),
    (String s) -> s.length(); // <- Function의 apply 메서드를 구현하는 람다
);
```

### 기본형 특화

자바의 모든 형식은 참조형(Integer, List 등) 아니면 기본형(int, double 등)에 해당하며 제네릭 파라미터( ex T) 에는 참조형만 사용할 수 있다 자바에서 기본형을 참조형으로 변화하는 기능을 박싱이라 하고 참조형을 기본형으로 변환하는 기능을 언박싱이라고 한다 또한 프로그래머가 편리하게 코드를 구현할 수 있도록 박싱과 언방식이 자동으로 이루어지는 오토 박싱이 존재한다

하지만 오토박싱을 하게 되면 결국 비용이 소요되게 된다 자바 8에서는 기본형을 입출력으로 사용하는 상황에서 오토박싱을 피할수 있도록 함수형 인터페이스 제공한다

```java
public interface IntPredicate {
	boolean test(int t);
}

IntPredicate evenNumbers = (int i) -> i % 2 == 0;
evenNumbers.test(1000); // 참 (박싱 없음)

Predicate<Integer> oddNumbers = (Integer i) -> i % 2 != 0;
oddNumbers.test(1000); // 거짓 (박싱)
```

### 형식 검사

람다가 사용되는 콘텍스트(context)를 이용해서 람다의 형식(type)을 추론할 수 있다 어떤 콘텍스트에서 기대되는 람다 표현식의 형식을 대상 형식(target type)이라고 부른다 형식검사는 다음과 같은 과정으로 진행된다

```java
 List<Apple> list = filter(inventory, (Apple apple) -> apple.getWeight() > 150);
```

1. 람다가 사용된 메소드의 선언을 확인한다 (위 코드에서는 filter 메소드)
2. 람다가 사용된 메소드의 파라미터로 대상 형식을 기대한다 (현재는 Predicate<Apple> 형식)
3. 기대하는 파라미터의 함수형 인터페이스를 파악한다 (Predicate의 test 메소드)
4. 함수형 인터페이스의 함수 디스크립터(메소드 시그니처)를 묘사한다
5. 전달받은 인수의 람다가 그 요구사항을 만족해야한다

### 형식 추론

람다를 사용할 때 컴파일러는 람다 표현식의 파라미터 형식에 접근할 수 있으므로 람다 문법에서 이를 생략할 수 있다 명시적으로 형식을 포함하는 것과 생략하는 것은 상황에 따라 가독성을 향상시킬 수 있는 방향으로 개발자가 결정하면 된다

```java
// 형식 추론
List<Apple> greenApples = filter(inventory, a-> GREEN.equals(a.getColor()));

// 형식 추론 사용 X
List<Apple> greenApples = filter(inventory, Apple a-> GREEN.equals(a.getColor()));
```

### 메소드 참조

메소드 참조를 이용하면 기존의 메소드 정의를 재활용해서 람다처럼 전달할 수 있다
메소드 참조는 특정 메소드만을 호출하는 람다의 축약형이라고 할 수 있으며, 메소드명 앞에 구분자(::)를 붙이는 방식으로 활용할 수 있다

```java
// 메소드 참조 전
inventory.sort((Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight())); 

//메소드 참조 후
inventory.sort(Comparing(Apple::getWeight)); 
```

- 메소드 참조 만드는 방법
    1. 정적 메소드 참조
        - Integer의 parseInt 메소드 → Integer::parseInt
    2. 다양한 형식의 인스턴스 메소드 참조
        - String의 length 메소드 → String::length
    3. 기존 객체의 인스턴스 메소드 참조
        - Transaction 객체에 getValue 메서드가 있고, 이 객체를 할당받은 expensiveTransaction 지역 변수가 있다면expensiveTransaction::getValue로 표현 가능

### 생성자 참조

ClassName::new 처럼 클래스명과 new 키워드를 이용해서 기존 생성자의 참조를 만들 수 있다.

```java
Supplier<Apple> c1 = () -> new Apple();
Supplier<Apple> c2 = Apple::new;

Apple a1 = c1.get();
Apple a2 = c2.get();
```

### 람다, 메소드 참조 활용하기

람다와 메소드 참조를 활용하여 기존 코드를 간소화 하는 예제이다

1. 코드 전달
    
    ```java
    public class AppleComparator implements Comparator<Apple> {
      public int compare(Apple a1, Apple a2) {
        return a1.getWeight().compareTo(a2.getWeight());
      }
    }
    
    inventory.sort(new AppleComparator());
    ```
    
2. 익명 클래스 사용
    
    ```java
    inventory.sort(new Comparator<Apple>() {
      public int compare(Apple a1, Apple a2) {
        return a1.getWeight().compareTo(a2.getWeight());
      }
    }
    ```
    
3. 람다 표현식 사용
    
    ```java
    // 타입 추론 사용 x
    inventory.sort((Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()));
    
    // 타입 추론 사용
    inventory.sort((a1, a2) -> a1.getWeight().compareTo(a2.getWeight()));
    
    // comparing 사용으로 코드를 간소화
    import static java.util.Compartor.comparing;
    inventory.sort(comparing(apple -> apple.getWeight());
    ```
    
4. 메소드 참조 사용
    
    ```java
    inventory.sort(comparing(Apple::getWeight));
    ```
    

### 람다 표현식을 조합할 수 있는 유용한 메소드

여러 개의 람다 표현식을 조합해서 복잡한 람다 표현식을 만들 수 있다
함수형 인터페이스가 제공하는 디폴트 메소드를 이용하면 or 연산을 수행하는 등 여러가지 값이 나오도록 조합할 수 있다

1. Comparator
    - 역정렬 : reversed() 메소드를 사용하여 역정렬을 할 수 있다
    
    ```java
    inventory.sort(comparing(Apple::getWeight).reversed());
    ```
    
    - Comparator 연결 : 여러 개의 결과값을 연결해서 적용할 수 있다
    
    ```java
    // 무게가 같다면 제조사 별로 정렬
    inventory.sort(comparing(Apple::getWeight)
      .reversed()
      .thenComparing(Apple::getCountry));
    ```
    
2. Predicate 
    - negate : 특정 프레디케이트를 반전시킬 때 사용
    
    ```java
    // 빨간색이 아닌 사과
    Predicate<Apple> notRedApple = redApple.negate();
    ```
    
    - and : and연산을 할 때 사용
    
    ```java
    Predicate<Apple> RedHeavyApple = redApple.and(apple -> apple.getWeight > 150);
    ```
    
    - or : or 연산을 할 때 사용
    
    ```java
    Predicate<Apple> RedHeavyOrGreenApple = 
      redApple.and(apple -> apple.getWeight > 150)
              .or(apple -> GREEN.equals(a.getColor()));
    ```
    
3. Function
    - andThen : 주어진 함수를 먼저 적용한 결과를 다른 함수의 인수로 전달
    
    ```java
    Function<Integer, Integer> f = x -> x + 1;
    Function<Integer, Integer> g = x -> x * 2;
    Function<Integer, Integer> h = f.andThen(g); //g(f(x))
    int result = h.apply(1); // 4를 반환
    ```
    
    - compose : 인수로 주어진 함수를 먼저 실행한 다음 그 결과를 외부 함수의 인수로 전달
    
    ```java
    Function<Integer, Integer> f = x -> x + 1;
    Function<Integer, Integer> g = x -> x * 2;
    Function<Integer, Integer> h = f.compose(g); //f(g(x))
    int result = h.apply(1); // 3을 반환
    ```
