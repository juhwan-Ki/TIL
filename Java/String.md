## String

자바에서 문자열을 사용할 때 String을 사용한다 하지만 String은 int boolean 같은 기본형이 아니라 참조형이다 하지만 문자열은 자주 사용하기 때문에 편의상 쌍따옴표로 문자열을 감싸면 자바에서 newString()으로 자동으로 변경해준다

```java
String a = "hello";
String b = new String("hello");

// 둘다 같은 값이 나옴
```

String에서는 필드 값으로 char[]을 가지고 문자 데이터를 보관한다
String은 개발자가 직접 다루기 불편한 char[]을 내부에  감추고 String을 사용하는 개발자가 편리하게 문자열을  다룰 수 있도록 다양한 기능을 제공한다

```java
public final class String {
	//문자열 보관
	private final char[] value;// 자바 9 이전 
	private final byte[] value;// 자바 9 이후
	
	//여러 메서드
	public String concat(String str) {...} public int length() {...}
	...
}

/*
	자바 9 이후 String 클래스 변경 사항
	자바 9부터 String 클래스에서 char[] 대신에 byte[] 을 사용한다
	자바에서 문자 하나를 표현하는 char 는 2byte 를 차지한다
	그런데 영어, 숫자는 보통 1byte 로 표현이 가능 하다 
	그래서 단순 영어, 숫자로만 표현된 경우 1byte를 사용하고
	(정확히는 Latin-1 인코딩의 경우 `byte 사 용), 
	그렇지 않은 나머지의 경우 2byte인 UTF-16 인코딩을 사용한다
	따라서 메모리를 더 효율적으로 사용할 수 있게 변경되었다
*/
```

### String 비교

String 클래스 비교할 때는 == 비교가 아니라 항상 equals() 비교를 해야한다

- 동일성(Identity): == 연산자를 사용해서 두 객체의 참조가 동일한 객체를 가리키고 있는지 확인
- 동등성(Equality): equals() 메서드를 사용하여 두 객체가 논리적으로 같은지 확인

```java
public static void main(String[] args) {
	String str1 = new String("hello");
	String str2 = new String("hello");
	
	System.out.println("new String() == 비교: " + (str1 == str2)); 
	System.out.println("new String() equals 비교: " + (str1.equals(str2)));
	
	String str3 = "hello";
	String str4 = "hello";
	System.out.println("리터럴 == 비교: " + (str3 == str4));
	System.out.println("리터럴 equals 비교: " + (str3.equals(str4)));
} 

/*
	결과
	new String() == 비교: false
	new String() equals 비교: true
	리터럴 == 비교: true
	리터럴 equals 비교: true
*/ 
```

str1과 str2는 new String()을 사용해서 각각 인스턴스를 생성했다 서로 다른 인스턴스 이므로 동일성
(==) 비교에 실패한다 둘은 내부에 같은 "hello" 값을 가지고 있기 때문에 논리적으로 같다
 따라서 동등성(equals()) 비교에 성공한다

### 문자열 풀(StringPool)

자바는 문자열 리터럴을 사용할 경우 메모리 효울성과 성능 최적화를 위해 문자열 풀을 사용한다
문자열 풀은 자바가 실행되는 시점에 클래스를 확인하여 문자열 리터럴이 있으면 문자열 풀에 미리 해당 문자열을 만들어 놓고 같은 문자열이 있으면 새로 만들지 않는다
문자열 풀을 사용하기 때문에 == 비교를 하게되면 같은 문자열일 경우 같은 주소값을 반환하기 때문에 true값을 리턴하게 된다

> 풀(Pool)은 자원이 모여있는 곳을 의미한다 프로그래밍에서 풀(Pool)은 공용 자원을 모아둔 곳을 뜻한다 여러 곳에서 함께 사용할 수 있는 객체를 필요할 때 마다 생성하고, 제거하는 것은 비효율적이다 대신에 이렇게 문자열 풀에 필요한 `String` 인스턴스를 미리 만들어두고 여러곳에서 재사용할 수 있다면 성능과 메모리를 더 최적화 할 수 있다 참고로 문자열 풀은 힙 영역을 사용한다 그리고 문자열 풀에서 문자를 찾을 때는 해시 알고리즘을 사용하기 때문 에 매우 빠른 속도로 원하는 `String` 인스턴스를 찾을 수 있다
> 

### String 불변 객체

String은 불변 객체이다 따라서 생성 이후에 절대로 내부의 문자열 값을 변경할 수 없다 그 이유는 문자열 풀에 있는 값이 변경되면 해당 문자열을 참조하고 있는 다른 변수의 값도 변경되기 때문이다 

### StringBuilder

String은 불변이기 때문에 문자열을 합치게 되면 새로운 문자열 객체를 만들게 된다
이렇게 되면 매번 객체를 생성해야 하고 GC를 하게 된다 결과적으로 메모리를 많이 사용하게 된다
또한 문자열의 크키가 크거나 문자열을 자주 변경할 수록 시스템의 자원을 더 많이 소모한다

```java
String str = "A" + "B" + "C" + "D";
String str = String("A") + String("B") + String("C") + String("D");
String str = new String("AB") + String("C") + String("D");
String str = new String("ABC") + String("D");
String str = new String("ABCD"); 

// 계속 객체를 생성하게 되어 메모리를 많이 사용하게 됨
```

이 문제를 해결하는 방법은 단순하다 바로 불변이 아닌 가변 `String` 이 존재하면 된다 가변은 내부의 값을 바로 변경 하면 되기 때문에 새로운 객체를 생성할 필요가 없다 따라서 성능과 메모리 사용면에서 불변보다 더 효율적이다 이런 문제를 해결하기 위해 자바는 `StringBuilder` 라는 가변 `String` 을 제공한다 물론 가변의 경우 사이드 이펙트에 주의해서 사용해야 한다

```java
// StringBuilder는 내부에 final이 아닌 변경할 수 있는 byte[]을 가지고 있다
public final class StringBuilder { 
	char[] value;// 자바 9 이전
	byte[] value;// 자바 9 이후
	//여러 메서드
	public StringBuilder append(String str) {...} 
	public int length() {...}
	...
}
```

> **StringBuilder vs StringBuffer**
StringBuilder와 똑같은 기능을 수행하는 StringBuffer클래스도 있다
StringBuffer는 내부에 동기화가 되어 있어서, 멀티 스레드 상황에 안전하지만 동기화 오버헤드로 인해 성능이 느리다 StringBuilder는 멀티 쓰레드에 상황에 안전하지 않지만 동기화 오버헤드가 없으므로 속도가 빠르다
>
