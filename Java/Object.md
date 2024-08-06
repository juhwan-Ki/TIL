### Object

자바의 최상위 클래스는 Object 클래스이다 클래스에 상속 받을 부모가 없을 경우 묵시적으로 Object 클래스를 상속 받는다 그렇기 때문에 Object가 가지고 있는 메소드들을 따로 정의하지 않아도 사용할 수 있다 
ex ) toString(), equals() 등

### Object가 최상위 객체인 이유?

1. 공통 기능 제공
    - 객체의 기본적인 기능을 만들 때 마다 항상 새로운 메소드를 정의해서 만들어야 한다면 상당히 번거로우며 만든다고 했을 때도 메소드의 이름이 일관성이 없을 것이다 이러한 이유로 Object는 모든 객체에 필요한 공통 기능을 제공한다 Object는 최상위 부모 객체이기 때문에 상속받아 사용할 수 있다
2. 다형성의 기본 구현
    - 부모는 자식을 담을 수 있다 즉 Object 객체는 모든 객체를 참조할 수 있다
    Object 클래스는 다형성을 지원하는 기본적인 메커니즘을 제공하며 타입이 다른 객체들을 Object로 담아서 사용할 수 있다

### Object의 다형성

Object는 최상위 클래스이기 때문에 모든 객체를 참조할 수 있다 하지만 Object는 하위 클래스가 가지고 있는 메소드를 사용할 수 없다 사용하기 위해서는 다운 캐스팅을 하여서 사용해야 한다

```java
public class Car {
    public void move() {
        System.out.println("이동!");
    }
}

...
public static void main(String[] args) {
	Car car = new Car();
	methodCall(car);
}

public static void methodCall(Object obj) {
	// obj.move(); 해당 메소드는 Object에 존재 하지 않기 때문에 사용할 수 없음!
	if(obj instanceof Car car) {
		car.move(); // 사용하려면 다운 캐스팅을 해야함
	}
}
```

### toString

Object가 제공하는 toString() 메소드는 기본적으로 패키지를 포함한 객체의 이름과 참조값(해시코드)를 16진수로 제공한다 또한 System.out,println() 메소드에서 Object의 toString()를 자동으로 호출해준다

```java
public String toString() {
	return getClass().getName() + "@" + Integer.toHexString(hashCode());
}

...
public static void main(String[] args) {
    Car car = new Car();
    System.out.println(car); // car.toString()을 하지 않아도 자동으로 toString 출력
}

// Object가 가지고 있는 toString를 재정의 하여 객체의 정보를 좀 더 자세히 나타내도록 할 수 있다

...
public class Car {

    private String name;
    private String color;
    private int speed;

    public Car(String name, String color, int speed) {
        this.name = name;
        this.color = color;
        this.speed = speed;
    }

    public void move() {
        System.out.println("이동!");
    }

    @Override
    public String toString() {
        return "Car{" +
                "name='" + name + '\'' +
                ", color='" + color + '\'' +
                ", speed=" + speed +
                '}';
    }
}

...
public static void main(String[] args) {
    Car car = new Car("벤츠","검정색",200);
    System.out.println(car);
}

// 결과값 
// Car{name='벤츠', color='검정색', speed=200}

/*
	toString과 hashCode는 객체의 참조값을 보여주는 역할을 한다
	toString과 hashCode를 오버라이딩 하여 재정의를 하게 되면 객체의 참조값을 알 수 없다
	이럴 때 System.identityHashCode()를 사용하여 객체의 참조값을 얻을 수 있다
	
	String refValue = Integer.toHexString(System.identityHashCode());
	Integer.toHexString은 16진수로 변환하는 메소드
*/
```

### Object와 OCP

만약 Object 클래스가 없다면 Car나 Dog와 같은 구체적인 타입을 사용하여야 할 것이다
이런것을 특정 클래스에 의존한다고 표현한다 이런 구체적인 클래스에 의존하게 되면 의존해야 할 클래스가 10개가 늘어난 다면 늘어나는 클래스에 맞추어 중복된 코드가 계속 증가하게 될 것이다

Object를 사용하면 Car와 Dog와 같은 구체적인 클래스에 의존하는 것이 아니라 추상적인 것에 의존하여 변경에는 닫혀있고 확장에는 열려있는 OCP에 맞는 설게를 할 수 있을 것이다

<aside>
💡 추상적이란 뜻은 추상클래스, 인터페이스만을 뜻하는 것이 아니라 부모 타입으로 올라갈 수록 개념은 더욱 추상적이게 되고 하위 타입으로 내려갈 수록 개념은 더욱 구체적이게 된다는 의미이다

</aside>

- OCP 원칙
    - Open : 새로운 클래스를 추가하고, toString()을 오버라이딩 하여 확장할 수 있다
    - Close : 새로운 클래스를 추가해도 Object와 toString()을 사용하는 클라이언트 코드는 변하지 않는다

> 정적 의존관계 vs 동적 의존관계
> 
> - 정적 의존관계는 컴파일 시간에 결정되며, 주로 클래스간의 관계를 의미한다 
> 즉 프로그램을 실행하지 않고 클래스 내에서 사용하는 타입만 보면 쉽게 의존관계를 파악할 수 있다
> - 동적 의존관계는 프로그램을 실행하는 런타임에 확인할 수 있는 의존관계를 의미한다
> 즉 런타임에 어떤 인스턴스를 사용하는지에 따라 바뀌는 형태를 의미한다

### equals()

Object는 동등성 비교를 위한 equals() 메소드를 제공한다

> 동일성 vs 동등성
> 
> - 동일성(Identity) : == 연산자를 사용해서 두 객체의 참조가 동일한 객체를 가르키고 있는지 확인
> - 동등성(Equality) : equals() 메소드를 사용하여 두 객체가 논리적으로 동등한지 확인
> - 즉 동일성은 같은 메모리에 있는 객체 인스턴스 인지 참조값을 확인하는 것이고 동등성은 논리적으로 같은지 확인하는 것이다

```java
// hello 문자열이 다른 메모리에 존재할 수 있지만 논리적으로 같은 문자열이다
String s1 = "hello";
String s2 = "hello"; 
```

- 동일성 & 동등성 비교

```java
User user1 = new User("홍길동");
User user2 = new User("홍길동");

System.out.println(user1 == user2); // false
System.out.println(user1.equlas(user2)); // false

/*
	equals()는 논리적으로 같은지 확인한다고 했는데 값이 다르다!??!
	그 이유는 Object의 equals()는 기본적으로 == 비교를 하기 때문이다!
	그럼 논리적으로 같은지 확인하려면 equals()를 재정의 해야한다! 
*/

... 
// Object의 기본 equals()
public boolean equals(Object obj) {
        return (this == obj);
}
```

- equals() 메소드 재정의 후 동일성 & 동등성 비교

```java
public class User {
    private String id;

    public User(String id) {
        this.id = id;
    }
    
    @Override
    public boolean equals(Object obj) {
        User user = (User) obj;
        return id.equals(user.id);
    }
}

...
public static void main(String[] args) {
   User user1 = new User("id-100");
   User user2 = new User("id-100");
   System.out.println("identity = " + (user1 == user2)); // false
   System.out.println("equality = " + user1.equals(user2)); //. true
}
```

### 정확한 equals() 구현

구현한 equals()는 매우 간단히 만든 버전이고, 실제로 정확하게 동작하려면 다음과 같이 구현해야 한다
정확한 equals() 메서드를 구현하는 것은 생각보다 쉽지 않다

```java
//변경 - 정확한 equals 구현, 
public boolean equals(Object o) {
     if (this == o) return true;
     if (o == null || getClass() != o.getClass()) return false;
     User user = (User) o;
     return Objects.equals(id, user.id);
}

/*
	equals() 메서드를 구현할 때 지켜야 하는 규칙
	1. 반사성(Reflexivity): 객체는 자기 자신과 동등해야 한다 (x.equals(x)는 항상 true)
	2. 대칭성(Symmetry): 두 객체가 서로에 대해 동일하다고 판단하면, 이는 양방향으로 동일해야 한다 
		(x.equals(y)가 true 이면 y.equals(x)도 true)
	3. 추이성(Transitivity): 만약 한 객체가 두 번째 객체와 동일하고, 두 번째 객체가 세 번째 객체와 동일하다면,
		 첫 번째 객체는 세 번째 객체와도 동일해야 한다
	4. 일관성(Consistency): 두 객체의 상태가 변경되지 않는 한, equals() 메소드는 항상 동일한 값을 반환해야 한다
	5. null에 대한 비교: 모든 객체는 null과 비교했을 때 false 를 반환해야 한다
*/
```
