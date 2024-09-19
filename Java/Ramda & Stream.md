### Ramda란?

- 함수(메소드)를 간단한 식으로 표현하는 방법

> 함수와  메소드의 차이
> 
- 근본적으로 동일. 함수는 일반적인 용어, 메소드는 객체지향개념 용어
- 함수는 클래스에 독립적, 메소드는 클래스에 종속적
(자바는 모든 함수들이 클래스 안에 있어야 하기 때문에 메소드라고 부른다)

### 람다식 작성법

1. 메서드의 반환타입을 제거하고 `->` 를 블록({})앞에 추가한다.

```java
// 일반 메소드
int max (int a, int b) {
	return a > b ? a : b;
}

// 람다식
(int a, int b) -> { return a > b ? a : b;} 
```

1. 반환값이 있는 경우, 식이나 값만 적고 `return문` 생략가능( ;도 생략)

```java
(int a, int b) ->  a > b ? a : b
```

1. 매개변수의 타입이 추론 가능하면 생략이 가능하다(대부분의 경우 생략 가능)

```java
(a, b) ->  a > b ? a : b
```

### 람다식 작성 주의사항

1. 매개변수가 하나인 경우 괄호 생략가능(타입이 없는 경우에만)

```java
a -> a * a // ok
(int a) -> a * a // error
```

1. 블록 안의 문장이 하나뿐일 때 괄호 생략가능(끝에 ; 안붙임)

```java
(int i) - > { System.out.println(i) }  => (int i) - > System.out.println(i)
```

<aside>
💡 람다는 익명 객체이다
</aside>

- why?
    - 자바에서는 메소드 혼자 존재 할 수 없기 때문에 하나의 객체나 클래스 안에 존재해야 한다.

### 함수형 인터페이스    
  - 단 하나의 추상 메소드만 선언된 인터페이스
  - @FunctionalInterface라는  어노테이션을 달아주는게 좋다

### Stream이란?

- 다양한 데이터 소스(컬랙션, 배열 등)를 표준화된 방법으로 다루기 위한 것
- 스트림을 만들면 중간 연산(여러번 가능) 최종 연산(한번만 가능)을 거친다.
- 중간 연산
    - 연산결과가 스트림인 연산으로 반복적으로 적용가능
    - stream.distinct().limit(5).sorted().forEach(System.out::println)
- 최종 연산
    - 연산 결과가 스트림이 아닌 연산으로 단 한번만 적용가능(스트림의 요소를 소모)
- 스트림의 특징
    - 데이터 소스로 부터 데이터를 읽기만할 뿐 변경하지 않음
    - Iterator처럼 일회용(다시 필요하면 다시 스트림을 생성해야함)
    - 최종 연산 전까지 중간연산이 수행되지 않음 (지연된 연산)
    - 작업을 내부 반복으로 처리
    - 작업을 병렬로 처리(병렬 스트림)
        - parallel()을 사용하면 병렬 스트림으로 변경 한 후 기존에 하던 대로 처리하면 됨
    - 기본형 스트림(IntStream, LongStream, DoubleStream)
        - Stream<Integer> 대신 IntStream을 사용하여 오토박싱&언박상의 비효율 제거

## Stream 만들기

### Stream 만들기

- Collection 인터페이스의 stream()으로 컬렉션을 스트림으로 변환

```java
List<Integer> list = Arrays.asList(1,2,3,4,5);
Stream<Integer> integerStream = list.stream();
integerStream.forEach(System.out::print);

// stream은 1회용 stream에 대해 최종연산을 수행하면 stream이 닫힌다.
//integerStream.forEach(System.out::print); 에러발생 스트림이 종료 되었기 때문
integerStream = list.stream();
integerStream.forEach(System.out::print);

  
```

### 객체 배열로 부터 스트림 생성

- Steam<T> Stream.of(T..values)  → 가변인자
- Steam<T> Stream.of(T[])  → 배열
- Steam<T> Arrays.steam(T[])  → 배열

```java
Stream<String> stringStream = Stream.of("a", "b", "c");
stringStream.forEach(System.out::println);

// 기본형 스트림은 count, sum, average와 같은 기능을 제공 
// 스트림은 count만 제공
int[] arr = {1,2,3,4,5};
IntStream intStream = Arrays.stream(arr);
System.out.println("sum = " + intStream.sum());
```

### 임의의 수(난수)

- 난수를 요소로 갖는 스트림 생성
- Ramdom.ints() → int형
- Ramdom.longs() → long형
- Ramdom.doubles() → double형

```java
IntStream intStream = new Random().ints();
        intStream
                .limit(5) // 5개만 짜르기
                .forEach(System.out::println);
```

### 람다식 iterate(), generate()

- iterate()는 이전 요소 seed로 해서 다음 요소를 계산
- generate()는 seed 사용 x

### 중간 연산

- distinct() : 중복 제거
- filter(predicate<T> predicate) : 조건에 안 맞는 요소 제외
- limit(long maxSize) : 일부를 잘라냄
- skip (long n) : 일부를 건너뜀
- peek (Consumer<T> action) : 요소에 작업수행
- sorted(), sorted(Comparator<T> comparator) : 요소를 정렬
- map, flapMap : 요소를 반환

### 최종 연산

- forEach : 반복
- forEachOrdered : 순서 유지하여 반복

