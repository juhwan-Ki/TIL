### 컬렉터란 무엇인가?

Collector는 java.util.stream에 있는 인터페이스로, 리듀싱 연산을 어떻게 구현할지 제공하는 메소드의 집합인 인터페이스이다 

collect 메소드에 Collector를 인자로 받아서 리듀싱 연산을 처리한다

- 리듀싱 → 모든 스트림 요소를 처리해서 값으로 도출

> collect, collector, collectros 차이
> 
> - collect : 최종 스트림 연산 메소드 중 하나
> - collector : collect에서 필요한 메소드를 정의해 놓은 인터페이스
> - collectors : collector를 구현한 클래스들을 제공(toList, toSet 등등)

```java
// collect
<R, A> R collect(Collector<? super T, A, R> collector);

// collectors
// collect를 구현한 클래스 toList
public static <T>
  Collector<T, ?, List<T>> toList() {
      return new CollectorImpl<>(ArrayList::new, List::add,
                                 (left, right) -> {left.addAll(right); return left;}
                                 , CH_ID);
}

/*
	실제로 Collectors는 Collector를 구현하고 있지 않다 (실제 구현체는 CollectorImpl)
	Collectros는 어떻게 Collector의 사용할 수 있나? 
		-> Collectors가 inner class로 가지고 있다 CollectorImpl 가지고 있고 toList와 같이
			 정적 메소드가 호출 될 때마다 CollectorImpl을 만들어서 반환하는 형태
		-> inner class로 구현함으로써 코드의 구조화, 접근 제어, 
			 네임스페이스 관리, 코드 응집성 유지 등의 이점을 얻을 수 있음
*/
```

Collectors에서 제공하는 메소드의 기능은 크게 세 가지로 구분할 수 있다

- 스트림 요소를 하나의 값으로 리듀스하고 요약
- 요소 그룹화
- 요소 분할

### 리듀싱과 요약

스트림에 있는 객체의 숫자 필드의 합계나 평균 등을 반환하는 연산에도 리듀싱 기능이
자주 사용된다 이러한 연산을 요약 연산이라 부른다

```java
public static void main(String[] args) {
		// Collector.counting()
    Dish.DISHES.stream().collect(counting());
		Dish.DISHES.stream().count(); // 위의 결과를 생략한 방법

		// Collector.maxBy()
		Dish.DISHES.stream()
								.collect(maxBy(Comparator.comparingInt(Dish::getCalories)));
    Optional<Dish> maxCalories =
				             Dish.DISHES.stream()
												.max(Comparator.comparingInt(Dish::getCalories));
		// 위의 결과를 생력하는 방법
}
```

### 요약 연산

Collectors 클래스는 Collectors.summingInt() 라는 특별한 요약 팩토리 메소드를 제공한다

```java
// summing 사용 전
int reduce = Dish.DISHES.stream()
        .mapToInt(Dish :: getCalories)
        .reduce(Integer :: sum)
        .getAsInt();

// summing 사용 후
int summingInt = Dish.DISHES.stream()
                            .collect(summingInt(Dish::getCalories));

/*
	CsummingLong과 summingDouble 메소드도 같은 방식으로 동작하며 
	long 또는 double 형식의 데이터로 요약한다는 점만 다르다
	이러한 연산말고도 averaging[Type], summing[type] 등 다양한 요약연산이 가능하다
	또한 이러한 결과연산들이 모두 필요할 시점이 있다 아니라면 일부 연산이 필요하다
	그럼 모두 summing, averaging 을 써야한다 
	하지만 이럴때를 대비해서 summarizing 메소드를 제공해준다.
*/

// summarizing 
IntSummaryStatistics collect =
                Dish.stream()
                        .collect(summarizingInt(Dish :: getCalories));
System.out.println(collect.toString());
/* result
	IntSummaryStatistics{count=8, sum=3670, min=120, average=458.750000, max=800}
*/
// int뿐만 아니라 long double에 대응하는 summarizingLong, summarizingDouble도 있다
```

### 문자열 연결

joining은 내부적으로 StringBuilder를 이용하여 문자열을 하나로만든다
문자열을 만들 클래스가 toString을 가지고 있으면 map 연산을 생략할 수 있다
또한 구분자를 통해서 값을 구분할 수 있다

```java
// joining
String collect = Dish.DISHES.stream().map(Dish :: getName).collect(joining(", "));
System.out.println(collect);

// joining 메소드
public static Collector<CharSequence, ?, String> joining() {
      return new CollectorImpl<CharSequence, StringBuilder, String>(
              StringBuilder::new, StringBuilder::append, 
              (r1, r2) -> { r1.append(r2); return r1; },
              StringBuilder::toString, CH_NOID);
 }
```

### 범용 리듀싱 요약 연산

지금까지 살펴본 모든 컬렉터는 reducing 팩토리 메소드로도 정의가 가능하다 
즉, 범용 Collectors.reducing으로도 충분히 구현할 수 있다는 거다 
그럼 왜 범용대신 특화된 Collectors 메소드를 사용하는 것일까? 바로 편의성 때문이다

```java
// reducing 을 이용한 방법
Optional<Dish> collect = Dish.DISHES.stream()
        .collect(reducing((x, y) -> x.getCalories() > y.getCalories() ? x : y));
// Collectors.maxBy 를 이용한 방법
Optional<Dish> collect1 = Dish.DISHES.stream()
                .collect(maxBy(Comparator.comparingInt(Dish :: getCalories)));
```

> **reduce vs collect**
> 
> - collect 메소드는 도출하려는 결과를 누적하는 컨테이너를 바꾸도록 설계된 메소드인 반면 reduce는 두 값을 하나로 도출하는 불변형 연산이라는 점에서 의미론적인 문제가 일어난다
> - reduce 메소드는 누적자로 사용된 리스트를 변환시킨다
> - reduce 메소드를 잘못 사용하면 실용성 문제도 발생하게 된다 여러 스레드가 동시에 같은 데이터 구조체를 고치면 리스트 자체가 망가져버리므로 리듀싱 연산을 병렬로 수행할 수 없다는 점도 문제다 이 문제를 해결하려면 매번 새로운 리스트를 할당해야 하고 따라서 객체를 할당하느라 성능이 저하될 것이다
> - 가변 컨테이너 관련 작업이면서 병렬성을 확보하려면 collect 메서드로 리듀싱 연산을 구현하는 것이 바람직하다

### 그룹화

데이터 집합을 하나 이상의 특성으로 분류해서 그룹화하는 연산을 명령형 코드에서 직접 그룹화를 구현하려면 까다롭고, 할일이 많으며, 에러도 많이 발생한다 함수형으로는 가독성있는 한줄의 코드로 그룹화를 구현할 수 있다 다음처럼 팩토리 메소드 Collectors.groupingBy를 이용해서 쉽게 메뉴를 그룹화할 수 있다

```java
// stream을 이용
Map<Dish.Type, List<Dish>> collect =
        Dish.DISHES.stream().collect(groupingBy(Dish :: getType));

// 외부 for을 이용
Map<Dish.Type,List<Dish>> dishMap = new HashMap<>();
for (Dish dish : Dish.DISHES){
    dishMap.put(dish.getType(),new ArrayList<>());
}
for (Dish dish : Dish.DISHES){
    dishMap.get(dish.getType()).add(dish);
}

/*
	이렇듯 Dish.Type과 일치하는 모든 요리를 추출하는 함수를 groupingBy 메소드로 전달 했다
	이 함수를 기준으로 스트림이 그룹화되므로 이를 분류 함수라고 부른다
*/
```

### 다수준 그룹화

두 인수를 받는 팩토리 메소드 Collectiors.groupingBy 메소드를 이용해서 항목을 다수준으로 그룹핑 할 수 있다 Collectors.groupingBy는 일반적은 분류 함수와 컬렉터를 인수로 받는다 
즉, 바깥쪽 groupingBy 메소드에 스트림의 항목을 분류할 두번째 기준을 정의하는 내부 groupingBy를 전달해서 두 수준으로 스트림의 항목을 그룹화할 수 있다

```java
Map<Dish.Type, Map<CaloricLevel, List<Dish>>> dishesByTypeCaloricLevel = 
  menu.stream().collect(
      groupingBy(Dish::getType,  // 첫번째수준 분류함수
        groupingBy(dish -> {     // 두번째수준 분류함수
        	if (dish.getCalories() <= 400) {
              return CaloricLevel.DIET;
            } else if (dish.getCalories() <= 700) {
              return CaloricLevel.NORMAL;
            } else {
              return CaloricLevel.FAT;
            }
          })
        )
      );
/* {
		 MEAT={DIET=[chicken], NORMAL=[beef], FAT=[pork]}, 
		 FISH={DIET=[prawns], NORMAL=[salmon]}, 
	   OTHER={DIET=[rice, seasonal fruit], NORMAL=[french fries, pizza]}
	 }
	 값이 없으면 Map에 데이터를 담지 않는다
/*
```

### 서브그룹 수준 그룹

실제 groupingBy(f) 는 groupingBy(f, toList()) 의 축약형이다 또한 groupingBy로 넘겨주는 컬렉터의 형식, 즉 toList의 자리에 들어가는 Collector의 형식은 제한이 없다 그래서 counting(), maxBy() 등 다양하게 활용할 수 있다.

```java
Map<Dish.Type, Optional<Dish>> collect = 
        Dish.DISHES.stream().collect(
                groupingBy(Dish :: getType, 
                        maxBy(Comparator.comparingInt(Dish :: getCalories))));

/*
	Optional<Dish>의 형으로 들어간다 
	하지만 groupingBy의 경우 없는 값은 Map의 키로 담지 않는다
	그렇기에 Optional 자체가 필요없다
*/

Map<Dish.Type, Dish> collect =
Dish.DISHES.stream().collect(
    groupingBy(Dish :: getType,
      collectingAndThen( // collecting이 끝난후 작업명시
            maxBy(Comparator.comparingInt(Dish :: getCalories)),
                  Optional::get))); // get 호출
```

### 분할

분할 함수라 불리는 프레디케이트를 분류 함수로 사용하는 특수한 그룹화 기능이다
분할 함수는 Boolean을 반환하므로 맵의 키 형식은 Boolean이며 결과적으로 그룹화 맵은 최대(참 아니면 거짓) 두 개의 그룹으로 분류된다

```java
Map<Boolean, List<Dish>> usePartitioningCollect = Dish.DISHES.stream()
        .collect(partitioningBy(Dish :: isVegetarian));
/* 
	{
		 false=[pork, beef, chicken, prawns, salmon], 
		 true=[french fries, rice, season fruit, pizza]
	 }
*/

Map<Boolean, Map<Dish.Type, List<Dish>>> vegetarianDishesByType = menu.stream()
			.collect(
		    partitioningBy(Dish::isVegetarian,  // 분할함수
		                   groupingBy(Dish::getType)));  // 두번째 컬렉터
/* 
	{
		 false={FISH=[prawns, salmon], MEAT=[pork, beef, chicken]}, 
		 true={OTHER=[french fries, rice, season fruit, pizza]
	 }
*/

/*
	분할의 장점은 무엇일까?
	기본적으로 filter를 이용하면 참, 거짓 요소의 리스트를 유지하는 
	힘들다. 하지만 partitioning을 이용하면 true={}, false={} 요소
	로 나타낼 수 있다.
*/
```

### **Collectors 클래스의 정적 팩토리 메소드**

```java
// toList()
// return : List<T>
// 스트림의 모든 항목을 리스트로 수집
List<Dish> collect = Dish.DISHES.stream().collect(Collectors.toList());

// toSet()
// return : Set<T>
// 스트림의 모든 항목을 집합으로 수집
Set<Dish> collect = Dish.DISHES.stream().collect(Collectors.toSet());

// toCollection()
// return : Collection<T>
// 스트림의 모든 항목을 발행자가 제공하는 컬렉션으로 수집
Collection<Dish> collect = Dish.DISHES.stream().collect(toCollection(HashSet ::new));

// counting()
// return : Long
// 스트림의 항목 수 계산
Long collect = Dish.DISHES.stream().collect(counting());

// summing()
// return : Integer
// 스트림의 항목에서 정수 프로퍼티 값을 더함
Integer collect = Dish.DISHES.stream().collect(summingInt(Dish :: getCalories));

// averaging()
// return : Double
// 스트림의 항목에서 정수 프로퍼티 값의 평균 계산
Double collect = Dish.DISHES.stream().collect(averagingInt(Dish :: getCalories));

// summarizing()
// return : IntSummaryStatistics
// 스트림의 항목에서 최대, 최소, 합계, 평균, 등 통계수집
IntSummaryStatistics collect = Dish.DISHES.stream()
	.collect(summarizingInt(Dish :: getCalories));

// joining()
// return : String
// 스트림의 항목에서 문자열 항목에서 toString()을 호출한 결과 결합
String collect = Dish.DISHES.stream().map(Dish :: getName).collect(joining(", "));

// maxBy(), minBy()
// return : Optional<T>
// 스트림의 항목에서 주어진 비교자를 이용하여 스트림 요소의 최대값, 최소값을 Optional<T> 형태로 반환, 없을땐 Optional.empty() 반환
Optional<Dish> collect = Dish.DISHES.stream().collect(maxBy(Comparator.comparingInt(Dish :: getCalories)));
Optional<Dish> collect = Dish.DISHES.stream().collect(minBy(Comparator.comparingInt(Dish :: getCalories)));

// reducing()
// return : T
// 누적자를 초깃값으로 설정, 다음에 BinaryOperator로 스트림의 각 요소를 반복적으로 누적자와 합쳐 스트림을 하나의 값으로 리듀싱
Integer collect = Dish.DISHES.stream().collect(reducing(0, Dish :: getCalories, Integer :: sum));

// collectingAndThen()
// return : ?
// 다른 컬렉터를 감싸고 그 결과에 변환 함수 적용
Long collect = Dish.DISHES.stream().collect(
collectingAndThen(
        summarizingInt(Dish :: getCalories), IntSummaryStatistics :: getSum));

// groupingBy()
// return : Map<K, List<T>>
// 하나의 프로퍼티값을 기준으로 스트림의 항목을 그룹화하며 기준 프로퍼티값을 결과 맵의 키로 사용
Map<Dish.Type, List<Dish>> collect = Dish.DISHES.stream()
.collect(
        groupingBy(Dish :: getType)
);

// partitioningBy()
// return : Map<Boolean, List<T>>
// 프레디케이트를 스트림의 각 항목에 적용한 결과로 항목 분할
Map<Dish.Type, Map<Boolean, List<Dish>>> collect = Dish.DISHES.stream()
.collect(
        groupingBy(Dish :: getType, partitioningBy(Dish :: isVegetarian))
);
```

### **Collector 인터페이스**

Collector 인터페이스는 리듀싱 연산(컬렉터)을 어떻게 구현할지 제공하는 메소드 집합으로 구성된다 toList, groupingBy 등 Collector인터페이스를 구현하는 많은 컬렉터 연산이 존재했다 이중 Collector 인터페이스를 구현하는 리듀싱 연산을 만들 수 있다

```java
// Collector 인터페이스
/*
	T 수집될 스트림 항목의 형식
	A 누적자, 수집과정에서 중간결과를 누적하는 객체의 형식
	R 수집 연산의 결과 객체 형식
*/
public interface Collector<T, A, R> {
		/*
			빈 누적자 인스턴스를 만드는 함수 즉, A를 만드는 과정
		*/		
    Supplier<A> supplier(); //reducing 연산을 수행하는 함수를 반환(빈 객체를 반환)
	
    BiConsumer<A, T> accumulator(); // 조합자로서 두 누적자가 결과를 어떻게 처리할지 결정
    
    BinaryOperator<A> combiner(); 
    // 스트림 탐색을 끝내고 누적자 객체를 최종 결과로 **변환**하면서 
	  // 누적 과정을 끝낼 때 호출할 함수를 반환, 
	  // 같은 타입이면 객체 변환이 필요없기 때문에 Fuction.identity() 항등함수를 반환
	  // Fuction.identity() -> 항상 같은 인스턴스를 반환하는 메소드
		
    Function<A, R> finisher(); //collect 메서드가 최적화(병렬화 같은)를 이용해서 리듀싱 연산을 수행할지 결정하도록 돕는 힌트
		
    Set<Characteristics> characteristics();
/*
	UNODERED : 리듀싱 결과는 순서의 영향을 받지않는다.
	CONCURRENT : 다중 스레드에서 accumulator 함수를 동시 호출가능 병렬 리듀싱 가능 UNODERED와 같이 사용하거나 사용하지 않는다면 순서와 상관없어야함
	IDENTITY_FINISH : identity를 적용할 뿐이므로 이를 생략가능.
*/
```
