### 컬렉션 팩토리

자바 9에서는 작은 컬랙션 객체를 쉽게 만들 수 있는 몇 가지 방법을 제시한다
팩토리 메소드를 사용하면 기존에 사용하던 코드를 간단하게 줄일 수 있다
하지만 팩토리 메소드를 사용해도 내부적으로 불필요한 객체를 할당하고 있다 
이러한 문제점을 해결하기 위해 새로운 팩토리 메소드가 등장하였다

```java
// 팩토리 메소드 사용 x
List<String> list = new ArrayList<>();
list.add("test");
list.add("dev");
list.add("prod");

// 팩토리 메소드 사용 
List<String> list = List.asList( "test", "dev", "prod" );

/*
	asList는 고정된 길이의 list를 생성하기 때문에 변경은 가능하나 추가는 불가능하다
	추가를 하게 되면 UnsupportedOperationException이 발생한다
*/

```

### 리스트 & 집합 팩토리

리스트는 `List.of` 메소드를 통해 간단하게 리스트를 생성할 수 있다
하지만 `List.of`  메소드는 변경할 수 없는 리스트로 만들어 졌기 때문에 요소 추가 및 변경을 하게 되면 예외가 발생하게 된다 또한 null 요소를 금지하기 때문에 의도치 않은 버그를 방지할 수 있다

```java
// 실제 of 메소드를 열어보면 ImmutableCollection을 반환한다
static <E> List<E> of(E e1, E e2, E e3, E e4, E e5) {
    return ImmutableCollections.listFromTrustedArray(e1, e2, e3, e4, e5);
}

// 가변 인자 사용
static <E> List<E> of(E... elements)
// 가변 인자를 사용할 때 해당 자료형의 배열로 값을 받아옴
/*
	of 메소드는 최대 10개까지 매개 변수를 갖는 메소드가 오버로딩 되어 있다
	10개 이상의 인자를 받는 메소드는 가변 인자 메소드를 사용한다
	이렇게 하는 이유는 가변 인자 버전은 가변 인자를 받게 될 때 내부적으로 
	인자를 배열 형식으로 받기 때문에 배열을 할당하고 초기화하며 나중에 가비지 컬렉션을 하는
	비용을 지불해야 하기 때문에 10개 이상일 경우에만 가변 인자를 사용하도록 하였다
*/
```

 `Set.of` 메소드는 중복된 요소가 저장되지 않는 집합을 만드는 팩토리 메소드이다
중복된 요소가 들어오게 되면 `Illegalargumentexception`이 발생한다

```java
// 중복된 값이 들어오면 Illegalargumentexception 발생
Set<String> set = Set.of("kevin", "oliver", "Kevin"); 
```

### 맵 팩토리

맵은 key value 한쌍으로 만들어야 하기 때문에 리스트보다 만들기가 복잡하다 하지만 자바9부터 맵을 편리하게 만드는 2가지 방법인 `Map.of` 와 `Map.Entries` 가 추가되었다

```java
// Map.of
Map<String, String> map = Map.of("key1","1", "key2", "2");

/*
	key value 값을 번갈아서 입력하여 Map을 생성하게 해주는 메소드로 10개 이하의 key value을 가진
	Map을 만들 때 유용하게 사용할 수 있다
*/

// Map.Entries
Map<String, String> map = Map.ofEntries(
                Map.entry("key1", "val1"),
                Map.entry("key2","val2"));
            
/*
	Map.Entry에 값을 인자로 받아서 여러개의 key value를 가진 Map을 생성할 수 있다
*/
```

### 리스트 & 집합 처리

자바8에서는 List와 Set에 3가지의 메소드가 추가되었다

1. removeIf : 프레디케이트를 만족하는 요소를 삭제한다 List와 Set을 구현하거나 상속 받은 구현체에서 사용이 가능하다
2. replaceAll : 리스트에서 사용할 수 있는 기능으로 UnaryOperator 함수를 이용하여 요소를 변경한다
3. sort : List에서 요소들을 정렬하는 기능을 제공한다

### removeIf

아래의 코드는 숫자로 시작하는 값을 삭제하는 코드이다 하지만 아래의 코드를 실행하게 되면 `ConcurrentModificationException` 이 발생하게 된다 자바8에서 `removeIf`를 사용하여 편리하게 해결 할 수 있다

```java
// list에 있는 값 중 숫자인 값을 삭제하는 로직
List<String> list = new ArrayList<>();
list.add("1");
list.add("A");
list.add("가");

// ConcurrentModificationException 발생
for (String s : list) {
    if(Character.isDigit(s.charAt(0))) {
        list.remove(s);
    }
}

/*
	ConcurrentModificationException이 발생하는 원인
	1. 반목문을 통해서 요소가 삭제 되면서 List의 index가 변경되어 
	현재 가리키고 있는 index가 List의 크기보다 커지게 되어
	2. 수정 불가능한 객체에 동시 수정을 하게 될 경우
	ex) Iterator가 컬렉션을 반복하는 중에 이 컬렉션에 대한 수정이 일어날 때 
			Fail-Fast이면 ConcurrentModificationException 예외를 발생함
*/

// removeIf를 통해 안전하게 삭제할 수 있음
list.removeIf(str -> Character.isDigit(str.charAt(0)));

// removeIf 내부 코드(iterator를 명시적으로 사용해서 에러가 발생하지 않도록 함)
default boolean removeIf(Predicate<? super E> filter) {
    Objects.requireNonNull(filter);
    boolean removed = false;
    final Iterator<E> each = iterator();
    while (each.hasNext()) {
        if (filter.test(each.next())) {
            each.remove();
            removed = true;
        }
    }
    return removed;
}

```

### replaceAll

`replaceAll` 메소드를 사용해서 리스트의 각 요소를 새로운 요소로 변경할 수 있다

```java
// 이렇게 하게 되면 변경이 되는 것이 아닌 새로운 문자가 새로 생성이 된다
list.stream()
     .map(str -> str.charAt(0) + str.charAt(1))
     .forEach(System.out::println);

// replaceAll을 사용
list.replaceAll(str -> String.valueOf(str.charAt(0) + str.charAt(1)));
```

### Map 처리

자바8에서 Map 인터페이스에 몇 가지 디폴트 메소드를 추가했다 자주 사용되는 패턴을 직접 구현하지 않고 메소드로 구현된 것을 사용하면 된다

### forEach

Map에서 key와 value 값을 반복하면서 확인하는 작업은 귀찮은 작업이다 `Map.Entry<K,V>`를 통해 반복을 할 수 있다 자바8에서는 이 작업을 보다 편리하게 하도록 `forEach` 메소드를 지원한다
또한 정렬 메소드를 통해 Map의 key or value를 기준으로 정렬을 할 수 있다

```java
// 기존에 사용하던 Map.Entry
Map<String, Integer> map = Map.of("key1", 1, "key2", 2, "key3", 3);

for (Map.Entry<String, Integer> entry : map.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
}

// 새로 추가된 forEach
map.forEach((k, v) -> System.out.println(k + ": " + v));

/*
	두 메소드 모두 정렬이 되지 않음(계속 돌리면 값이 달라짐)
	새로 추가된 comparingByKey or comparingByValu를 통해 정렬을 할 수 있다
*/ 

map.entrySet()
      .stream()
      .sorted(Map.Entry.comparingByKey())
      .forEach(System.out::println);
```

### getOrDefault

기존에 찾으려는 key가 존재하지 않으면 null이 반환되므로  `NullPointerException` 을 방지하려면 요청 결과가 null인지 확인해야 했다 하지만 이러한 방식 대신 기본값을 반환하는 메소드인 `getOrDefault` 를 사용해서 값이 null이면 기본값을 값이 있으면 key에 해당하는 값을 반환할 수 있다 여기서 주의해야할 점은 **key값이 존재 해도 value가 null이면 기본값을 리턴한다**는 점이다

```java
Map<String, Integer> map = Map.of("key1", 1, "key2", 2, "key3", 3);

System.out.println(map.getOrDefault("key1", 100)); // 값이 있기 때문에 1출력
System.out.println(map.getOrDefault("key100", 100)); // 값이 없기 때문에 100출력
```

### 계산 패턴

Map에 key가 존재하는지 여부에 따라 어떤 동작을 실행하고 결과를 저장해야 하는 상황이 필요할 때가 있는데 새로 추가된 메소드를 통해 도움을 받을 수 있다

1. computeIfAbsent : key에 해당하는 값이 없거나 null이면 key에 새로운 값을 계산하고 Map에 추가
2. computeIfPresent : key가 존재하면 새 값을 계산하고 Map에 추가
3. compute : 입력된 key로 새로운 값을 계산고 Map에 추가

```java
// 메소드 사용 전 
Map<String, List<String>> map = new HashMap<>();

List<String> list = map.get("key");
if(list == null) {
    list = new ArrayList<>();
    map.put(str, list);
}

// computeIfAbsent 사용 후 
List<String> list = map.computeIfAbsent("key", k -> new ArrayList<>());

// computeIfPresent
List<String> list2 = map.computeIfPresent("key", (k, v) -> new ArrayList<>());

/*
	computeIfPresent는 Map에 key가 존재하고 value가 null이 아닐 때만 새로운 값으로 계산
	null이면 key 제거 후 null 반환
*/
```

### 삭제 패턴

자바8에서는 특정한 값과 연관 되었을 때 만 항목을 제거하는 오버라이드 버전 remove 메소드를 제공한다

```java
if(map.containsKey("key1") && Objects.equals(map.get("key1"), "value")) {
    map.remove("key1");
} 
// remove 사용
map.remove("key1","value");
```

### 합침

두 개의 Map을 합칠 때 `merge` 메소드를 이용하여 값을 합칠 수 있다
`merge` 메소드는 지정된 key와  연결된 값이 없거나 null이면 null이 아닌 값과 연결한다 
또한 주어진 매핑 함수의 결과 값으로 대치하거나 결과가 null이면 항목을 제거한다

```java
Map<String,Integer> friends = Map.ofEntries(
        Map.entry("박경찬", 30),
        Map.entry("한국민", 27),
        Map.entry("두반장", 50)
);

Map<String, Integer> family = Map.ofEntries(
        Map.entry("이로아", 30),
        Map.entry("어두칠", 27)
);

Map<String,Integer> everyone = new HashMap<>(family); // everyone 으로 모두 합침
everyone.putAll(friends);

/*
	이렇게 하면 everyone으로 모든 map들이 합쳐진다 
	하지만 여기서 문제가 되는건 중복된 key가 있게 되면 중복된 key의 값은 저장되지 않는다
*/

Map<String,Integer> everyone = new HashMap<>(family);
friends.forEach((k,v) ->
        everyone.merge(k,v,(v1,v2) -> v1 + v2) // friends와 everyone에 중복 키가 존재하면 두 값을 합침
);
System.out.println(everyone);

// merge를 통해서 중복된 key가 있으면 해당 key의 값을 더하여서 저장하도록 할 수 있다

default V merge(K key, V value,
        BiFunction<? super V, ? super V, ? extends V> remappingFunction) {
    Objects.requireNonNull(remappingFunction);
    Objects.requireNonNull(value);
    V oldValue = get(key); // 동일 키를 가진 값 찾기
    V newValue = (oldValue == null) ? value :
               remappingFunction.apply(oldValue, value); // 새값 생성
    if (newValue == null) {  // 새로운 값이 널이면 제거, 
        remove(key);
    } else {
        put(key, newValue);
    }
    return newValue;
}
```

### 개선된 ConcurrentHashMap

ConcurrentHashMap 클래스는 동시성 친화적이며 최신 기술을 반영한 HashMap이다
ConcurrentHashMap은 내부 자료구조의 특정 부분만 잠궈 동시 추가 및 갱신 작업을 허용한다
따라서 동기화된 HashTable 버전에 비해 읽기 쓰기 연산 성능이 월등하다

### **리듀스와 검색**

- forEach : 각 (키, 값) 쌍에 주어진 액션을 수행
- reduce : 모든 (키, 값) 쌍을 제공된 리듀스 함수를 이용해 결과로 합침
- search : 널이 아닌 값을 반환할 때까지 각 (키, 값) 쌍에 함수를 적용

또한 연산에 병렬성 기준값(threshold)를 정해야 한다 맵의 크기가 기준값보다 작으면 순차적으로 연산을 진행한다 기준값을 1로 지정하면 공통 스레드 풀을 이용해 병렬성을 극대화할 수 있다

### **계수**

맵의 매핑 개수를 반환하는 mappingCount 메서드를 제공한다 기존에 제공되던 size 함수는 int형으로 반환하지만 long 형으로 반환하는 mappingCount를 사용할 때 매핑의 개수가 int의 범위를 넘어서는 상황에 대하여 대처할 수 있을 것이다

### **집합뷰**

ConcurrentHashMap을 집합 뷰로 반환하는 keySet 메서드를 제공한다 맵을 바꾸면 집합도 바뀌고 반대로 집합을 바꾸면 맵도 영향을 받는다 newKeySet이라는 메서드를 통해 ConcurrentHashMap으로 유지되는 집합을 만들 수도 있다

### 정리

- 자바9는 원소를 포함하며 바꿀 수 없는 List, Set, Map을 쉽게 만들도록 `List.of` `Set.of`  `Map.of` `Map.ofEntries` 등의 컬렉션 팩토리를 지원한다
- remove, removeIf, replaceAll, replace, sort, computeIfAbsent 등 다양한 메소드가 추가되었다
