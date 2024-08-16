전통적인 자바에서 인터페이스와 관련 메소드는 한 몸처럼 구성된다 인터페이스를 구현하는 클래스는 인터페이스에서 정의하는 모든 메소드를 구현하거나 슈퍼클래스의 구현을 상속받아야 한다 평소에는 이 규칙을 지키는 것이 문제가 되지 않지만 인터페이스에 새로운 메소드를 추가하는 등 인터페이스를 바꾸고 싶을 때는 문제가 발생하게 된다 인터페이스를 변경하게 되면 이전에 해당 인터페이스를 구현한 모든 클래스의 구현도 고쳐야 하기 때문이다

이러한 문제를 해결하기 위해 자바8에서는 기본 구현을 포함하는 인터페이스를 정의하는 두 가지 방법을 제공한다 첫 번째는 인터페이스 내부에 정적 메소드를 사용하는 것이고 두 번째 방법으로는 디폴트 메소드를 사용하는 것이다 즉 자바8에서는 메소드 구현을 포함하는 인터페이스를 정의 할 수 있다 디폴트 메소드를 사용하게 되면 기존의 코드 구현을 변경하지 않으면서 인터페이스를 변경할 수 있다

```java
// default 메소드
default void sort(Comparator<? super E> C) {
	Collections.sort(this, c);
}

/*
	자바8 이전에는 List 인터페이스에 sort가 없었지만 자바8에 default 메소드로 추가되어
	list에서 바로 sort 메소드를 사용할 수 있게 되었다(stream도 마찬가지)
*/
...
List<Integer> numbers = Arrays.asList(3, 5, 1, 2, 6);
numbers.sort(Comparator.naturalOrder());

/*
	naturalOrder는 표준 알파벳 순서로 요소를 정렬할 수 있도록 
	Comparator 객체를 반환하는 새로운 정적 메소드이다 
	
	이렇게 되면 결국 인터페이스가 아니라 추상 클래스 아닌가? 라는 생각이 든다
	인터페이스와 추상 클래스는 같은 점이 많아 졌지만 여전히 다른 점도 있다
	- 추상 클래스는 다중 상속 x 인터페이스는 다중 상속 O
	- 추상 클래스는 인스턴스 변수(필드)를 가질 수 있지만 인터페이스는 변수를 가질 수 없음 
	- 추상 클래스는 클래스를 상속, 확장을 위해 사용하고 
		인터페이스는 구현 객체들에 대한 동일한 사용방법과 동작을 보장하기 위해 사용
*/
```

<aside>
💡 **디폴트 메서드의 장점**

- 호환성 유지 : 인터페이스만 고치면 문제를 해결할 수 있다
- 구현 클래스의 수정 없음 : 기존 클래스를 고치는 것이 아니다 인터페이스만 고치면 된다
- 유틸 클래스의 제거 : 기존에 정적 유틸클래스를 만들어 조작하는 일을 많이 했다 자주 사용 해야하는데 모든 코드에 유틸 메소드를 다 정의해주기 힘들었기 때문이다 하지만 이제는 인터페이스에 하나만 정의할 수 있기 때문에 유틸클래스를 제거할 수 있다
</aside>

### 변화하는 API

API를 바꾸는 것이 왜 어려운 것인지 예제를 통해서 볼 필요성이 있다

```java
/*
	가상의 라이브러리 Resizable은 모양과 크기를 조정할 수 있다고 가정한다
	그리고 인기를 얻어 사용자가 직접 아래의 Ellipese라는 클래스를 구현해서 사용한다
*/
public interface Resizable extends Drawable{ // 나의 라이브러리
    int getWidth();
    int getHeight();
    void setWidth();
    void setHeight();
    void setAbsoluteSize();
}

...
public class Ellipse implements Resizable{ // 라이브러리 사용자들이 구현해서 사용한다
 ...
}

// 또한 이러한 API를 사용할때 이러한 모습으로 사용한다

List<Resizable> resizables = 
		Arrays.asList(new Shapes.Square(), new Shapes.Rectangle(), new Shapes.Ellipse());
resizables.forEach(s ->{
    s.setAbsoluteSize(42,42);
    s.draw();
});

/*
	하지만 몇 개월이 지나고 Resizable을 구현하는 Square, Rectangle 개선 요청이 많이 생겼다
	그래서 초기 버전에 SetRelativeSize 메소드를 추가했다
	그러자 수많은 문제가 터지기 시작했다
*/
...
public interface Resizable extends Drawable{ // 나의 라이브러리
    int getWidth();
    int getHeight();
    void setWidth();
    void setHeight();
    void setAbsoluteSize();
		void SetRelativeSize(); // 추가
}
```

**문제점**

1. Resizable을 구현하는 클래스(Square, Rectangle)을 고쳐야한다
    - 중요한 점은 사용자들이 커스텀한 Ellipse 클래스도 고쳐야한다
2. 사용자가 만약 재빌드를 한다면 컴파일 에러가 발생한다
3. 호환성이 깨진다

이러한 문제점이 발생하여 default 메소드가 등장하게 되었다

<aside>
⚠️ **호환성?**
프로그램을 바꾸는 것과 관련된 호환성 문제는 크게 바이너리 호환성, 소스 호환성, 동작 호환성 세가지로 분류 할 수 있다

1. **바이너리 호환성** : 뭔가를 바꾼 이후에도 에러 없이 기존 바이너리가 실행될 수 있는 상황, 예를 들어 인터페이스에 메소드를 추가했을 때 추가된 메소드를 호출하지 않는 한 문제가 일어나지 않는 것, 물론 추가하면 구현은 해야하지만 아무것도 없는 구현이라면 또 달라진다
2. **소스 호환성** : 코드를 고쳐도 기존 프로그램을 성공적으로 재컴파일 할 수 있는 상황, 예를 들어 인터페이스에 메소드를 추가하면 소스 호환성이 아니다 구현해야 컴파일이 가능하기 때문이다
3. **동작 호환성** : 코드를 바꾼 이후에도 같은 입력값이 주어지면 프로그램이 같은 동작을 실행하는 상황, 예를 들어 인터페이스에 메소드를 추가하더라도 프로그램에서 추가된 메소드를 호출할 일이 없으므로 동작이 가능하다

</aside>

### 디폴트 메소드란 무엇인가?

자바8에서 호환성을 유지하면서 API를 바꿀 수 있도록 새로운 기능인 디폴트 메소드를 제공한다 이제 인터페이스는 자신을 구현하는 클래스에서 메소드를 구현하지 않아도 이미 구현된 메소드를 사용할 수 있다

```java
public interface Resizable extends Drawable{
    int getWidth();
    int getHeight();
    void setWidth();
    void setHeight();
    void setAbsoluteSize();
    default void SetRelativeSize(int w,int h){
        ...
    };
}
... 
// 더 이상 setAbsoluteSize()를 구현할 필요가 없다
// 또한 Ellipse에서도 디폴트 메소드를 사용할 수 있다
public static class Ellipse implements Resizable{ 
      @Override
      public int getWidth() {
          return 0;
      }

      @Override
      public int getHeight() {
          return 0;
      }

      @Override
      public void setWidth() {

      }

      @Override
      public void setHeight() {

      }
      @Override
      public void setAbsoluteSize(int w, int h) {

      }
      @Override
      public void draw() {

      }
}
```

### 디폴트 메소드 활용 패턴

- 선택형 메소드
    
    Iterator 인터페이스는 사용자들이 remove기능은 잘 사용하지 않으므로 자바 8이전에는 remove기능을 그냥 무시하고 사용했다 결과적으로 Iterator을 구현하는 클래스가 많아지면서 자바 8이전에는 빈 구현으로 놔두는 경우가 많았다 하지만 아래와 같이 디폴트 메소드의 지원으로 다음과 같은 형태로 빈 구현을 지원할 필요가 사라졌다
    
    ```java
    	public interface Iterator<E> {
    ...
    	boolean hasNext();
    	E next();
    	default void remove() {
    		  throw new UnsupportedOperationException("remove");
    	}
    }
    ...
    // remove를 구현할 필요가 사라졌다
    Iterator<Integer> iterator = new Iterator<Integer>() { 
        @Override
        public boolean hasNext() {
        }
    
        @Override
        public Integer next() {
            return null;
        }
    };
    ```
    
- 동작 다중 상속
    
    클래스는 다중 상속을 지원하지 않기 때문에 여러 기능을 가진 클래스를 상속하려면 해당 기능을 상속받는 클래스를 만든 후 상속을 진행해야 한다 
    
    ```java
    // 클래스의 다중 상속
    public class Class1 {
    	public add(int i) {
    		....
    	}
    }
    
    public class Class2 extends Class1 {
    	public remove(int i) {
    		....
    	}
    }
    
    public class Class3 extends Class2 {
    	public change(int i) {
    		....
    	}
    }
    
    ...
    // 최종으로 사용하는 클래스
    public class ExtendClass extends Class3 {
    	public used(int i) {
    	  ....
    	}
    }
    
    /*
    	클래스는 다중 상속이 안되기 때문에 해당 방법을 통해 다중 상속이 되도록 할 수 있었다
    	이때 class1의 기능을 수정하고 싶다면 자식에서 기능을 찾아가기 어렵고 
    	중간에 여러번을 거치기 때문에 잘못될 가능성이 높아진다
    */
    
    // 인터페이스의 다중 상속
    public interface Inter1 {
    	void add(int i) {
    		....
    	}
    }
    
    public interface Inter2 {
    	void remove(int i) {
    		....
    	}
    }
    
    public interface Inter3 {
    	void change(int i) {
    		....
    	}
    }
    
    ...
    public class ImplementClass implements Inter1, Inter2, Inter3 {
    	....
    }
    
    /*
    	하지만 인터페이스 형태라면 이런식의 상속이 가능해진다
    	또한 여기에 기능이 default 메서드라면 모든 부모의 기능을 구현할 필요없어지며 
    	자연스럽게 부모의 기능을 모두 사용할 수 있게 된다
    */
    ```
    
- 디폴트 메소드 활용
    
    만약 3개의 기능을 가진 캐릭터를 만든다고 가정한 후 3개의 기능이 들어간 인터페이스를 먼저 정의한다 그 후 클래스에 3개의 기능을 추가할 수 있다
    
    ```java
    public interface 공격{
        default void 평타(){
            System.out.println(get평타속성());
        }
        String get평타속성();
    }
    ...
    public interface 방어{
        default void 막기(){
            System.out.println(get막기속성());
        }
        String get막기속성();
    }
    ...
    public interface 스킬{
        default void 스킬(){
            System.out.println(get스킬속성());
        }
        String get스킬속성();
    }
    ...
    @AllArgsConstructor
    public class 전사 implements 공격, 방어, 스킬{
        private String 공격;
        private String 방어;
        private String 스킬;
    
        @Override
        public String get평타속성() {
            return 공격;
        }
    
        @Override
        public String get막기속성() {
            return 방어;
        }
    
        @Override
        public String get스킬속성() {
            return 스킬;
        }
    }
    ```
    
    하지만 이후 방어 스킬이 빠진 도적을 만들게 된다면 방어 인터페이스를 빼면 된다
    
    ```java
    @AllArgsConstructor
    class 도적 implements 공격, 스킬{ // 방어를 빼면된다.
        private String 공격;
        private String 회피;
        private String 스킬;
    
        @Override
        public String get평타속성() {
            return 공격;
        }
    
        @Override
        public String get스킬속성() {
            return 스킬;
        }
    }
    ```
    
    이렇게 디폴트 메소드를 사용하게 되면 이미 구현된 메소드가 있기 때문에 기능이 추가 되거나 삭제가 되어도 해당 인터페이스를 추가 혹은 삭제하여 효율적으로 코드를 재사용할 수 있게 된다(디폴트 메소드가 없었으면 해당 기능이 구현된 코드를 복사 붙여 넣기로 인터페이스를 상속받은 클래스에 모두 적용했어야 함)
    

<aside>
💡 옳지 못한 상속?
상속으로 코드를 재사용하는 것으로 모든 문제를 해결할 수 있는 것은 아니다 예를 들어 한 개의 메소드를 재사용하려고 100개의 메소드와 필드가 정의된 클래스를 상속받는 것은 좋은 생각이 아니다 이럴 때는 델리게이션, 즉 멤버변수를 이용하여 클래스에서 필요한 메소드를 직접 호출하는 메소드를 작성하는 것이 좋다

</aside>

### 해석 규칙

자바의 클래스는 하나의 부모 클래스만 상속받을 수 있지만 여러 인터페이스를 동시에 구현할 수 있다 자바 8에는 디폴트 메소드가 추가되었으므로 같은 시그니처를 갖는 디폴트 메소드를 상속받는 상황이 생길 수 있다 이러한 문제를 다이아몬드 문제라고 한다

<aside>
💡 다이아몬드 문제란?
n개의 인터페이스를 상속 받았을 때, n개의 인터페이스에 동일한 메소드 시그니처를 가지는 메소드를 디폴트 메소드로 구현하고 있다면 자바의 다중상속 문제와 같이 어떠한 메소드가 호출될 지 결정할 수 없고 충돌하는 문제를 의미한다

</aside>

> 해석 규칙
> 
> 1. 클래스가 항상 이긴다 클래스나 슈퍼클래스에서 정의한 메소드가 디폴트 메소드 보다 항상 우선권을 가진다
> 2. 1번 규칙 이외의 상황(클래스, 슈퍼클래스가 없는 상황)에서는 서브 인터페이스가 이긴다 상속관계를 갖는 인터페이스에서 같은 시그니처를 갖는 메소드를 정의할 때는 서브 인터페이스가 이긴다
> 3. 여전히 디폴트 메소드의 우선순위가 결정되지 않았다면 여러 인터페이스를 상속받는 클래스가 명시적으로 디폴트 메소드를 오버라이드하고 호출해야한다
- 디폴트 메소드를제공하는 서브 클래스가 이긴다
    
    ```java
    public interface A {
    	default void hello() {
    		System.out.println("Hello from A");
    	}
    }
    
    public interface B extends A{
    	default void hello() {
    		System.out.println("Hello from B");
    	}
    }
    
    ... 
    public class C implements A, B{
    	public static void main(String[] args) {
    		new C().hello(); // 무엇이 출력이 되나?
    	}
    }
    
    // 2번 규칙이 적용 되어 Hello From B가 출력된다
    
    ...
    public class D implements A {} 
    public class C extends D implements A, B {
    	public static void main(String[] args) {
    		new C().hello(); // 무엇이 출력이 되나?
    	}
    }
    
    /*
    	D에서 메소드를 재정의 하지 않았기 때문에 2번 규칙이 적용되어 Hello From B가 출력된다
    	  - 클래스나 슈퍼 클래스에 메소드 재정의 하였다면 재정의 한 메소드가 사용됨
    */ 
    ```
    
- 메소드 충돌
    
    ```java
    public interface A {
    	default void hello() {
    		System.out.println("Hello from A");
    	}
    }
    
    public interface B {
    	default void hello() {
    		System.out.println("Hello from B");
    	}
    }
    
    ... 
    public class C implements A, B{
    	public static void main(String[] args) {
    		new C().hello(); // 무엇이 출력이 되나?
    	}
    }
    
    /*
    	해당 상황에 경우 상속관계가 없기 때문에 2번 규칙을 적용할 수 없다
    	그러므로 컴파일러는 어떤 메소드를 사용해야할지 알 수 없어 에러를 띄운다
    	"Error: C inherits unrelated defaults for hello() from types A and B"
    */
    
    // 인텔리제이에서는 컴파일 시점에 오류를 띄워서 실행이 안되도록 한다
    public class C implements A, B{
    
    	void hello(){
    		A.super.hello(); // 명시적으로 선택 해줘야 사용이 가능하다
    	}
    }
    
    ...
    public interface test1 {
    	default Number getNumber() {
    		return 10;
    	}
    }
    
    public interface test2 {
    	default Integer getNumber() {
    		return 20;
    	}
    }
    
    ...
    public Class C implements A, B {
    	public static void main(String[] args) {
    		new C().getNumber(); // 메소드를 구분할 수 없어 컴파일 에러 발생
    	}
    }
    ```
    

**결론**

- 자바 8의 인터페이스는 구현 코드를 포함하는 디폴트 메소드, 정적 메소드를 정의할 수 있다
- 디폴트 메소드의 정의는 default 키워드로 시작하며 일반 클래스 메소드처럼 바디를 갖는다
- 공개된 인터페이스에 추상 메소드를 추가하면 소스 호환성이 깨진다
- 디폴트 메소드 덕분에 호환성유지가 가능해졌다
- 해석규칙이라는 상속 충돌 문제를 해결하는 방법이 있다
