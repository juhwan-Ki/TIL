### 필터링

- 프레디케이트 필터링
    
    filter 메소드는 프레디케이트(boolean을 반환하는 함수)를 인자로 받아 프레디케이트와 일치하는 모든 요소를 포함하는 즉 boolean 메소드를 이용해서 해당하는 값을 포함하는 스트림을 반환한다
    
    ```java
    List<Dish> vegetarianMenu = menu.stream()
                    .filter(Dish::isVegetarian)
                    .toList();
    ```
    
- 고유 요소 필터링
    
    고유 요소로 이루어진 스트림을 반환하는 distinct 메소드를 사용하여 중복을 필터링한다
    고유 여부는 스트림에서 만들어진 객체의 hashcode와 equals로 판단한다
    
    ```java
    List<Dish> distinctMenu =  menu.stream()
                    .filter(m -> m.getCalorie() > 300)
                    .distinct()
                    .toList();
    ```
    
### 슬라이싱

- TakeWhile
    - Predicate<T>를 파라미터로 받아서 해당 Predicate가 **false**를 반환할 때까지의 요소를 취하는 메소드
    - filter와 비슷하지만 false를 반환 하는 순간 이후 요소들은 모두 버려짐
    - 정렬되어 있고 많은 요소를 가진 스트림에서 효율적
- DropWhile
    - Predicate<T>를 파라미터로 받아서 해당 Predicate가 **false**를 반환할 때까지의 요소를 모두 버리고 나머지 요소를 반환하는 메소드
    - takeWhile과 정반대로 작업을 수행

### 스트림 축소

- limit
    - 주어진 값 이하의 크기를 갖는 새로운 스트림을 반환하는 메소드
    - long을 파라미터로 받아서 해당 숫자 이하의 요소를 반환
    - 소스가 정렬되어 있지 않았다면 limit의 결과도 정렬되지 않은 상태로 반환됨

### 요소 건너뛰기

- skip
    - 처음 n개 요소를 제외한 스트림을 반환하는 메소드
    - long을 파라미터로 받아서 해당 숫자 이하의 요소를 버리고 나머지를 반환
    - n개 이하의 요소를 포함하는 스트림에 skip(n)을 호출하면 빈 스트림이 반환됨

### 매핑

- map
    - 파라미터로 제공되는 함수(`Function<T, R>`관련된 함수형 인터페이스)를 적용해서 기존 요소를 새로운 요소로 매핑시키는 `map`이라는 메소드를 제공한다
    - 일반 스트림에서는 `Function<T, R>`이 사용되고 있는데 **T**가 변환 전 타입 **R**이 변환 후 타입이다
    - **T**와 **R**의 타입이 같을 수도 있다
    - 기본형 특화 스트림에서는 타입의 변화가 없기 때문에 각각에 맞는 특화 타입의 `UnaryOperator`가 사용되고 있다
        
        ```java
        <R> Stream<R> map(Function<? super T, ? extends R> mapper);
        ```
        
- mapToInt
    - 스트림을 `IntStream`으로 변환해주는 메소드
    - 일반 스트림에서는 `map` 메소드와 다르게 `ToIntFunction<? super T> mapper`를 파라미터로 전달한다
    - `ToIntFunction`라는 인터페이스의 이름에서 알 수 있듯이 기본 `Function<T, R>`에서 **T**만 제네릭 타입으로 전달받고 **R**은 int로 정해져있다
        
        ```java
        IntStream mapToInt(ToIntFunction<? super T> mapper);
        ```
        
- mapToLong
    - 스트림을 `LongStream`으로 변환해주는 메소드
    - mapToInt 메소드와 내용은 유사하다
        
        ```java
        LongStream mapToLong(ToLongFunction<? super T> mapper);
        ```
        
- mapToDouble
    - 스트림을 `DoubleStream`으로 변환해주는 메소드
        
        ```java
        DoubleStream mapToDouble(ToDoubleFunction<? super T> mapper);
        ```
        
- mapToObj
    - 기본형 특화 스트림을 `Stream`으로 변환해주는 메소드
    - 기본형 특화 스트림의 종류에 따라 `LongFunction<U>`, `DoubleFunction<U>`이 사용된다
    - 인터페이스의 이름에서 알 수 있듯이 기본 `Function<T, R>`에서 **T**가 int, long, double이고 **R**이 **U**가 된다
        
        ```java
        <U> Stream<U> mapToObj mapToObj(ToDoubleFunction<? super T> mapper);
        // ToDoubleFunction은 스트림의 종류에 따라 바뀜
        ```
        
- flatMap
    - 파라미터로 제공되는 함수(`Function<T, Stream<R>>`과 관련된 함수형 인터페이스)를 적용해서 스트림의 각 값을 다른 스트림으로 만든 다음에 모든 스트림을 하나의 스트림으로 연결하는 기능을 수행하는 `flatMap`이라는 메소드를 제공한다
    - 추상적으로 설명하면 차원을 낮추는 개념이라고 생각된다
    (ex. `Stream<String[]>` -> `Stream<String>`)
        - 위의 예시의 경우 일반 `map` 메소드를 사용할 경우 `String[]` 배열 자체가 다른 객체로 변환되는데 `flatMap`을 사용하면 `String[]` 내부의 값들 자체를 다른 객체로 변환하는 것이 가능하다
    - 스트림의 종류에 따라 각 메소드의 파라미터로 `Function<T, Stream<R>>`, `IntFunction<IntStream>>`, `LongFunction<LongStream>>`, `DoubleFunction<DoubleStream>>`을 전달받는다
        
        ```java
        <R> Stream<R> flatMap(Function<? super T, ? extends Stream<? extends R>> mapper);
        ```
        

### 매칭

- anyMatch
    - 적어도 한 요소와 일치하는지 확인하는 최종 연산이다 (일치하는 순간 true 반환).
        
        ```java
        boolean anyMatch(Predicate<? super T> predicate);
        // 구현 예제
        public static boolean vegetarianAnyMatch() { // anyMatch 를 이용한 방법
            System.out.println("list안에 채식주의자가 존재하나?");
            return dishs.stream()
                    .anyMatch(Dish::isVegetarian); // 적어도 하나 이상 일치하는 요소가 있는지 검색
        }
        ```
        
- allMatch
    - 모든 요소와 일치하는지 검사하는 최종 연산이다 (일치하지 않는 순간 false 반환).
        
        ```java
        boolean allMatch(Predicate<? super T> predicate);
        // 구현 예제
        public static boolean vegetarianAllMatch() { // allMatch 를 이용한 방법
            System.out.println("list 안에 채식주의자만 있나?");
            return dishs.stream()
                    .allMatch(Dish::isVegetarian); // 모든 요소가 일치 하는지 검사
        }
        ```
        
- noneMatch
    - 모든 요소가 일치하지 않는지 검사하는 최종 연산이다 (일치하는 순간 false 반환).
        
        ```java
        boolean noneMatch(Predicate<? super T> predicate);
        // 구현 예제
        public static boolean vegetarianNoneMatch() { // noneMatch 를 이용한 방법
            System.out.println("list 안에 채식주의자가 없나?");
            return dishs.stream()
                    .noneMatch(Dish::isVegetarian); // 모든요소가 불일치 하는지 검사
        }
        ```
        
- findFirst
    - 첫 번째 요소를 찾아 반환한다. 순서가 정해져 있을 때 사용한다.
        
        ```java
        Optional<T> findFirst();
        ```
        
- findAny
    - 요소를 찾으면 반환한다. 요소의 반환순서가 상관없을 때 findFirst 대신 사용된다.
        
        ```java
        Optional<T> findAny();
        ```
        
        > Optional이란?
        > 
        > - Optional<T>는 null이 올 수 있는 값을 감싸는 Wrapper 클래스로, 참조하더라도 NPE가 발생하지 않도록 도와준다
    
    > 쇼트서킷
    > 
    > - 전체 스트림을 처리하지 않아도 원하는 요소의 결과값을 찾으면 즉시 결과를 반환하는 것
    > - 무한한 요소를 가진 스트림을 유한한 크기로 줄일 수 있는 연산
    > - limit와 위에 보여지는 match연산, find 연산이 그에 해당한다

### 리듀싱

- 모든 스트림 요소를 BinaryOperator로 처리해서 값으로 도출한다
- 두 번째 reduce 메소드와 같은 경우 초기값(identity)가 없으므로 아무 요소가 없을 때를 위해 `Optional<T>`를 반환한다

```java
T reduce(T identity, BinaryOperator<T> accumulator);
Optional<T> reduce(BinaryOperator<T> accumulator);
<U> U reduce(U identity, BiFunction<U, ? super T, U> accumulator, BinaryOperator<U> combiner);
```

```java
public class reducing {
    public static void main(String[] args) {
        sum();
    }
		// 1~10까지의 합을 구하는 문제
    public static void sum() { 
        List<Integer> numbers = Arrays.asList(1,2,3,4,5,6,7,8,9,10);
        int sum = numbers.stream()
                .reduce(0, (a,b) -> a+b);
				/*
					reduce(초기 값, (파라미터1,파라미터2) -> 초기값에 할당 );
					흐름
						1번 루프
							a = 초기값 0
							b = 요소의 첫번째 값 1
						초기값 = reduce 결과 값 1 대입연산 
				*/
        System.out.println(sum);
    }
		// 같은 예제 다른 반환
		public static void sum() {
        List<Integer> numbers = Arrays.asList(); // stream 없음
        Optional<Integer> sum = numbers.stream()
                .reduce((a,b) -> a+b); // 초기 값 없음
        System.out.println(sum);
    }
    /*
			왜 reduce에 초기값 설정을 안한다면 Optinal을 반환할까?
			스트림에 아무요소도 없고 초기값이 없는 상황이라면 reduce는 아무것도 반환하지
			못한다. 그래서 초기값이 없는 상황에서는 이를 대비해서 optional을 반환한다.
		*/
}
```

> reduce 메서드의 장점과 병렬화
> 
> - 기존의 단계적 반복으로 합계를 구하는 것보다 reduce를 이용하면 내부 반복이 추상화되면서 내부 구현에서 병렬로 reduce를 실행할 수 있게 된다. 반복적인 합계에서는 sum 변수를 공유해야 하므로 쉽게 병렬화하기 어렵다. 스트림은 내부적으로 포크/조인 프레임워크(fork/join framework)를 통해 이를 처리한다.

> 스트림 연산
> 
> - 각각의 스트림 연산은 상태를 갖는 연산과 상태를 갖지 않는 연산으로 나뉘어져 있다.
> - map, filter 등은 입력 스트림에서 각 요소를 받아 0 또는 결과를 출력 스트림으로 보낸다. 따라서 이들은 보통 상태가 없는, **내부 상태를 갖지 않는 연산(stateless operation)**이다.
> - 하지만 reduce, sum, max 같은 연산은 결과를 누적할 내부 상태가 필요하다. 예제의 내부 상태는 작은 값이다. 스트림에서 처리하는 요소 수와 관계없이 내부 상태의 크기는 **한정(bounded)**되어 있다.
> - 반면 sorted나 distinct 같은 연산은 스트림의 요소를 정렬하거나 중복을 제거하기 위해 과거의 이력을 알고 있어야 한다. 예를 들어 어떤 요소를 출력 스트림으로 추가하려면 **모든 요소가 버퍼에 추가되어 있어야 한다**. 따라서 데이터 스트림의 크기가 크거나 무한이라면 문제가 생길 수 있다. 이러한 연산을 **내부 상태를 갖는 연산(stateful operation)**이라 한다.

### 기본형 특화 스트림

자바 8에서는 세가지 기본형 특화 스트림을 제공한다. Int, Double, longStream 이 스트림의 특징은 오직 박싱 과정에서 일어나는 효율성과 관련 있으며 스트림에 추가 기능을 제공하지 않는다는 점이다

```java
int calories = Dish.DISHES.stream()
        .mapToInt(Dish::getCalories) // IntStream 반환
        .sum();
/*
	map에서는 stream<Integer>로 반환 -> 래핑이 된 상태 
	mapToInt의 경우 IntStream int 형으로 반환 -> 래핑이 안된 상태
*/
IntStream calories = Dish.DISHES.stream()
                .mapToInt(Dish::getCalories);
Stream<Integer> stream = calories.boxed(); // 박싱도 가능

/*
	스트림에 요소가 없는 상황을 대비하여 기본값을 미리 정해줄 수 있다
	기본특화형의 3가지버전으로 OptionalInt, OptionalDouble, OptionalLong이 있다
	만약에 찾는 값이 없다면 동일하게 noSuchElementException을 발생시킨다
*/
List<String> numbers = Arrays.asList();
OptionalInt optionalInt = numbers.stream()
                .mapToInt(Integer::parseInt)
                .max();
System.out.println(optionalInt.getAsInt()); 

//숫자범위 지정
IntStream evenNumbers = IntStream
								.range(1,100) // 1 ~ 99까지 범위 지정 가능
								//.rangeClosed(1, 100) 1 ~ 100까지
								.filter(n -> n % 2 == 0);
        System.out.println(evenNumbers.count());
```

### 스트림 만들기

- 값으로 스트림 만들기
    - 임의의 수를 인수로 받는 of 메소드를 통해 스트림을 만들 수 있다
    - empty 메소드를 통해 스트림을 비울 수 있다
    
    ```java
    Stream<Integer> stream = Stream.of(1,2,3,4); // Stream<Integer>
    Stream<Integer> streamEmpty = Stream.empty(); // 빈 스트림
    ```
    
- null이 될 수 있는 스트림 만들기
    - 해당 객체에 값이 없는 경우  ofNullable 메소드를 활용하여 스트림을 null로 만들 수 있다
    
    ```java
    Stream<String> nullableStream = 
    	Stream.ofNullable(System.getProperty("nullProperty"));
    ```
    
- 무한 스트림
    - 컬렉션처럼 고정된 크기로 스트림을 만들었던 것과 달리 크기가 고정되지 않는 스트림을 만들 수 있다
    - iterate와 generate를 통해 만들어진 스트림은 요청할 때 마다 주어진 함수의 값을 이용해서 스트림을 만들기 때문에 무제한으로 값을 계산할 수 있다
    - iterate는 초기값을 누적하여 스트림을 만들 수 있다는 특징이 있고 generate는 초기값을 누적하는 것이 아닌 `Supplier<T>`를 인수로 받아 새로운 값을 생산한다는 특징이 있다
    
    ```java
    // 무한 스트림을 사용하여 파보나치 수열을 만드는 예제
    // 파보나치 수열이란 0 1 로 시작하며 이후의 숫자는 이전 두 수를 더한 값이다 
    Stream.iterate(new int[]{0,1}, t -> new int[]{t[1], t[0]+t[1]})
    				.limit(10)
            .collect(Collectors.toList())
            .forEach(s -> System.out.println(s[0]+" "+s[1]));
     /*
    	iterate 메서드는 초깃값 new int[]{0,1}과 람다를 인수로 받아 새로운
    	값을 끊임없이 생산한다 그래서 람다 t -> new int[]{t[1], t[0]+t[1]}
    	에서 람다는 초깃값에 계속 대입된다. 이러한 무한스트림은 언바운드 스트
    	림이라한다.
    */
    
    IntStream.iterate(0, n -> n < 100, n -> n + 4)
    				 .forEach(System.out::println);
    				 
    /* 자바 9에서 itreate 메소드에 프레디케이트를 지원하여
       숫자 생성을 중단하는 코드를 만들 수 있다
    */
    
    Stream.generate(Math::random)
    			.limit(5)
    			.forEach(System.out::println);
    // 새로운 랜덤 값이 생성됨
    ```
    

### 정리

- 스트림 API를 사용하면 복잡한 데이터 처리 질의를 할수 있다
- filter, distinct, takeWhile(java 9), skip, limit, 메소드로 스트림을 필터링 하거나 자를 수 있다
- 소스가 정렬되어 있는 가정하에 takeWhile, dropWhile 메소드를 효과적으로 사용할 수 있다
- map, flatMap 메소드로 스트림의 요소를 추출하거나 변환 할 수 있다
- findFirst, findAny 메소드로 스트림의 요소를 검색할 수 있다.
- allMatch, anyMatch, noneMatch 메소드를 이용해서 주어진 프레디케이트와 일치하는 요소를 스트림에서 검색할 수 있다
    - 이들 메서드는 쇼트 서킷(short circuit)으로 결과를 찾는 즉시 반환하며, 전체 스트림을 처리하지 않는다
- reduce 메소드로 스트림의 모든 요소를 반복 조합하며 값을 도출할 수 있다
- filter, map 등은 상태를 저장하지 않는 상태 없는 연산(stateless operation)이다
- reduce 같은 연산은 값을 계산하는데 필요한 상태를 저장한다
- sorted, distinct 등의 메소드는 새로운 스트림을 반환하기에 앞서 스트림의 모든 요소를 버퍼에 저장해야한다 이런 메소드들을 stateful operation 이라고 부른다
- IntStream, LongStream, DoubleStream은 기본형 특화 스트림이다 이들 연산은 각각의 기본형에 맞게 특화 되어 있다
- 컬렉션뿐 아니라 값, 배열, 파일, iterate와 generate 같은 메소드로 스트림을 만들 수 있다
