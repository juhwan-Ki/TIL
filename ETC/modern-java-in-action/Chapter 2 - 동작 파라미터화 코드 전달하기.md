### 동작 파라미터화

동작 파라미터화란 아직은 어떻게 실행할 것인지 결정하지 않은 코드 블록을 의미한다
코드 블록의 호출은 나중에 프로그램에서 호출 즉 코드 블록의 실행은 나중으로 미뤄진다

### 동작 파라미터화 적용 전

동작 파라미터화를 적용하기 전에는 동작에 따른 코드들이 추가 되었어야 했다
예를 들어 사과의 무게에 따라 list에 추가하는 코드가 있다고 하면 해당 코드는 무게에 따른 필터링만 가능할 것이다 하지만 필터가 추가되어 색깔에 따른 필터링 되어야 한다면 똑같은 코드를 복사하여 색깔로 필터링 하는 코드를 작성해야 한다

```java
// 무게를 가지고 필터링 하는 코드
public List<Apple> filterAppleByWeight(List<Apple> appleList, int weight) {
    List<Apple> result = new ArrayList<>();
    // 무게에 따른 필터링
    for (Apple apple : appleList) {
        if(apple.getWeight() > weight) {
            result.add(apple);
        }
    }
    return result;
}

// 색깔을 가지고 필터링 하는 코드
public List<Apple> filterAppleByColor(List<Apple> appleList, String color) {
    List<Apple> result = new ArrayList<>();
    // 색깔에 따른 필터링
    for (Apple apple : appleList) {
        if(apple.getColor().equals(color)) {
            result.add(apple);
        }
    }
    return result;
}
```

필터가 추가되면 추가 될 수록 반복되는 코드가 지속적으로 추가될 것이다 
이러한 반복된 코드를 줄이기 위해 동작 파라미터화를 사용할 수 있다 

### 동작 파라미터화 적용

동작 파라미터화를 사용하게 되면 파라미터를 통해 여러가지 동작에 유연하게 대응할 수 있다
인터페이스를 통해 boolean 값을 리턴하는 메소드를 만든 후 해당 인터페이스를 상속받아 구현한 구현체에서 메소드를 정의하는 것이다 이러한 것을 전략 디자인 패턴이라고 부른다

> 전략 디자인 패턴이란?
> 
> - 행(런타임) 중에 알고리즘 전략을 선택하여 객체 동작을 실시간으로 바뀌도록 할 수 있게 하는 행위 디자인 패턴
> - 어떤 일을 수행하는 알고리즘(기능 or 동작이 될 수도 있음)이 여러가지 일 때 동작들을 미리 전략으로 정의함으로써 전략을 교체할 수 있는, 즉 알고리즘 변형이 자주 일어나는 곳에 필요한 경우에 적합한 디자인 패턴이다

```java
// 인터페이스 생성
public interface ApplePredicate {
    boolean filter(Apple apple);
}

// 인터페이스를 상속받아 필터 메소드를 구현한 구현체
public class AppleRedAndLight implements ApplePredicate{
    @Override
    public boolean filter(Apple apple) {
        return apple.getColor().equals("RED") && apple.getWeight()> 150;
    }
}

// 해당 구현체를 파라미터로 넘겨 구현한 메소드를 통해 어떻게 동작할지 결정
public List<Apple> filterApple(List<Apple> appleList, AppleRedAndLight ap) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : appleList) {
        if(ap.filter(apple)) { // 이 부분을 구현하여 동작을 결정
            result.add(apple);
        }
    }
    return result;
}
```

이렇게 구현한 클래스들을 파라미터로 넘겨 구현한 메소드를 통해 동작을 결정한다
하지만 동작 마다 구현 클래스를 정의하고 인스턴스화 해야하기 때문에 불편한 점이 있다

### 익명 클래스

익명 클래스란 내부 클래스와 비슷한 개념으로 익명 클래스를 이용하면 클래스 선언과 인스턴스화를 동시에 진행할 수 있다 즉 즉석에서 필요한 구현을 만들어서 사용할 수 있다
하지만 익명 클래스를 사용하게 되면 내부에서 코드를 정의해서 사용해야 하기 때문에 코드가 너무 장황하게 보일 수 있다. 장황한 코드는 유지보수성을 떨어트리며 코드를 이해하는데 시간이 오래 걸리게 된다.

```java
// 익명 클래스를 사용한 방식
List<Apple> greenApples = filterApples(inventory, new AppleGreenColorPredicate() {
		public boolean test(Apple apple) {
			return GREEN.equals(apple.getColor());
		}
});
```

### 람다

람다를 사용하게 되면 기존에 사용했던 코드보다 간단하게 처리할 수 있다

```java
List<Apple> greenApples
	 = filterApples(inventory, (Apple apple) -> GREEN.equals(apple.getColor()));
```

제네릭을 이용한 리스트 클래스로 추상화를 하게되면 다른 객체여도 사용이 가능해진다
제네릭을 사용하면 타입의 안정성과 타입체크 및 형변환을 생략할 수 있어 코드가 간결해진다.

```java
// 제네릭으로 추상화 
public interface Predicate<T> {
  boolean test(T t);
}

public static <T> List<T> filter(List<T> list, Predicate<T> p) {
  List<T> result = new ArrayList<>();
  for(T e : list) {
    if(p.test(e)) {
      result.add(e);
    }
  }
  return result;
}

// 실제 사용
List<Apple> redApples = filter(inventory, (Apple apple) -> RED.equals(apple.getColor()));
List<Integer> evenNumbers = filter(numbers, (Integer i) -> i % 2 == 0);
```

### 정리

동작 파라미터화를 이용하면 변화하는 요구사항에 더 잘 대응하는 코드를 구현할 수 있다

자바 8(람다)을 활용하여 인터페이스를 상속받아 여러 클래스를 구현하는 고생을 덜 수 있다

자바 API의 많은 메서드는 정렬, 스레드, GUI 처리 등을 포함한 다양한 동작으로 파라미터화 할 수 있다