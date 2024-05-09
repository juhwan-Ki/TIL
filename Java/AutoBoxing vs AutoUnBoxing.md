### 기본 타입(primitve type) 과 래퍼 클래스(wrapper class)

- 자바에는 기본 타입과 기본 타입을 객체로 다루기 위해서 사용하는 클래스인 래퍼 클래스(Wrapper Class) 가 존재한다
- 기본 타입은 객체로 생성될 수 없기 때문에 값을 한번 감싸 객체 형식으로 만든다
- 래퍼 클래스는 참조타입이기 때문에 null 값을 가질 수 있다
- 래퍼 클래스는 값을 포장하여 객체로 만들었기 때문에 값을 더하는 등 변환시켜야 할 필요가 있는 경우 포장한 값을 다시 뜯어야 한다
- 이러한 행위를 Boxing과 UnBoxing 이라고 한다
    - Boxing : 기본 타입의 데이터 → 래퍼 클래스의 인스턴스로 변환
    - UnBoxing : 래퍼 클래스의 인스턴스에 저장된 값 → 기본 타입의 데이터로 변환

```java
// 박싱
Integer num = new Integer(20);// Integer 래퍼 클래스 num 에 21 의 값을 저장

// 언박싱 
(intValue) int n = num.intValue();// 래퍼 클래스 num 의 값을 꺼내 가져온다.

// 재 포장(박싱)
n = n + 100; // 120
num = new Integer(n);

/*
	기본 타입에 따라 메소드가 존재한다
	booleanValue() -> boolean
	byteValue() -> byte
	doubleValue() -> double 등등
*/
```

### AutoBoxing & AutoUnBoxing

- JDK 1.5 부터는 박싱과 언박싱이 필요한 상황에 자바 컴파일러가 자동으로 처리해주기 시작했다
- 이러한 자동화된 박싱과 언박싱을 오토박싱과  오토언박싱 이라고 부른다
- 기본타입 값을 직접 박싱, 언박싱하지 않아도 래퍼 클래스 변수에 대입만 하면 자동으로 박싱과 언박싱이 된다

```java
/* 기존 박싱 & 언박싱 */
Integer num = new Integer(17); // 박싱
int n = num.intValue();        // 언박싱

/* 오토 박싱 & 언박싱 */
Integer num = 17; // new Integer() 생략
int n = num; // intValue() 생략

/* 오토 박싱 & 언박싱 기능을 이용하여 래퍼 객체 직접 연산 가능 */
Integer num1 = new Integer(7); // 박싱
Integer num2 = new Integer(3); // 박싱

int int1 = num1.intValue();    // 언박싱
int int2 = num2.intValue();    // 언박싱

// 박싱된 객체를 오토 언박싱하여 연산하고 다시 박싱하여 저장
Integer result1 = num1 + num2; // 10 
Integer result2 = int1 - int2; // 4
int result3 = num1 * int2;     // 21
```

### 래퍼 클래스 동등 비교

- 래퍼 클래스는 객체 형태이기 때문에 래퍼클래스 끼리의 동등비교를 하기 위해서는 `==` 을 사용해서는 안된다
- `==` 연산을 사용하게 되면 객체의 참조 값을 비교하기 때문에 같은 값을 가진다고 할 지라도 다른 결과를 나타낸다
- 동등 비교를 하려면 `equals()` 메소드를 사용해야 한다
- 대신 래퍼 클래스와 기본 자료형과의 비교는 오토박싱과 언박싱을 해주기 때문에 `==` 비교가 가능하다

```java
Integer num = new Integer(10); // 래퍼 클래스1
Integer num2 = new Integer(10); // 래퍼 클래스2
int i = 10; // 기본타입

// 래퍼클래스 == 기본타입
System.out.println(num == i); // true

// 래퍼클래스.equals(기본타입)
System.out.println(num.equals(i)); // true

// 래퍼클래스 == 래퍼클래스
System.out.println(num == num2); // false (invalid)

// 래퍼클래스.equals(래퍼클래스)
System.out.println(num.equals(num2)); // true
```

### AutoBoxing & AutoUnBoxing 성능

- AutoBoxing과 AutoUnBoxing은 편의성을 위해 제공되고 있지만, 내부적으로 기본 타입의 값을 Wrapper 클래스 객체로 변환하는 과정에서 새로운 객체가 생성된다
- 이러한 추가적인 연산이 지속적으로 반복된다면 필요없는 메모리를 점유하게 되고 해당 메모리는 가비지 컬렉터가 알아서 회수를 해주지만 가비지 컬렉션의 부하가 발생하므로 성능 저하가 발생할 수 있다
