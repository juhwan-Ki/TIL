### 제네릭
- 제네릭이란 클래스 외부에서 사용자의 의해 타입이 지정되는 것을 의미한다 <br>
한마디로 특정 타입을 미리 정해두는 것이 아닌 필요에 의해 지정할 수 있도록 하는 것을 말한다
- 배열의 타입을 지정하듯 컬렉션 클래스나 메소드에서 사용할 내부 데이터 타입을 파라미터 주듯이 <br> 
외부에서 지정하는 타입을 변수화 하는 기능이라고 생각하면 된다
- 제네릭을 사용하게 되면 하나의 값이 여러 다른 데이터 타입을 가질 수 있도록 할 수 있다 
``` java

String[] strArr = new String[10]; // String type array

ArrayList<String> strList = new ArrayList<String>(); // String type list
ArrayList<Integer> intList = new ArrayList<Integer>(); // Integer type list

```

### 타입 파라미터
- 제네릭에서 `<>` 안에 식별자 기호를 지정함으로써 파라미터화 할 수 있다
- 이것을 메소드의 매개변수와 비슷하여 타입 파라미터 / 타입 변수라고 한다
- 타입 파라미터를 사용해서 해당 타입을 지정해주면 해당 타입으로 변환되어 클래스의 타입이 지정되게 된다
- 타입 파라미터는 반드시 Reference 타입을 입력해야 하고 primitive 타입을 넘기게 되면 <br>
`Type argument cannot be of primitive type` 에러가 발생한다 <br>
사용자가 정의한 클래스도 당연히 들어올 수 있다
- 타입 파라미터는 여러개 넣어줄 수도 있다

``` java

class FruitBox<T> {
    List<T> fruits = new ArrayList<>(); // 자바 1.7 부터 생성자에 타입 파라미터를 생략할 수 있도록 변경됨

    public void add(T fruit) {
        fruits.add(fruit);
    }
}

// 제네릭 타입 매개변수에 정수 타입을 할당
FruitBox<Integer> intBox = new FruitBox<>(); 

// 제네릭 타입 매개변수에 실수 타입을 할당
FruitBox<Double> intBox = new FruitBox<>(); 

// 제네릭 타입 매개변수에 문자열 타입을 할당
FruitBox<String> intBox = new FruitBox<>(); 

// 클래스도 넣어줄 수 있다. (Apple 클래스가 있다고 가정)
FruitBox<Apple> intBox = new FruitBox<Apple>();

// primitive 타입을 넣게 되면 Type argument cannot be of primitive type 오류 발생
FruitBox<int> test = new FruitBox<int>();

// 복수의 타입 파라미터도 적용할 수 있음
class Animal<T,U> {
    List<T> dogList = new ArrayList<>();
    List<U> catList = new ArrayList<>();

    public void add(T dog, U cat) {
        dogList.add(dog);
        catList.add(cat);
    }
}

```

### 타입 파리미터 네이밍
- 타입 파라미터의 식별자는 문법적으로 따로 정해진 것은 없다 하지만 암묵적으로 정해진 네이밍 규칙이 있다

|   타입   |   설명    |
|:---------:|:-------:|
|`<T>`|Type|
|`<E>`|Element|
|`<K>`|Key|
|`<V>`|Value|
|`<N>`|Number|


### 제네릭 장점
1. 제네릭을 사용하면 컴파일 단계에서 잘못된 타입이 들어오는 것을 방지할 수 있다
    - 제네릭에 타입을 지정하면 해당 타입으로 변환되어 타입이 지정되게 된다
    - 이를 통해 다른 타입이 들어오게 되면 에러가 발생하게 된다

2. 외부에서 지정한 타입을 사용하기 때문에 타입을 체크하고 변환해줄 필요가 없다
    -  미리 타입을 지정 & 제한해 놓기 때문에 형 변환(Type Casting)의 번거로움을 줄일 수 있다

3. 비슷한 기능을 지원하는 경우 코드의 재사용성이 높아진다
    - 제네릭은 위에서도 언급했듯이 여러 다른 데이터 타입을 가질 수 있도록 할 수 있기 때문에 
    비슷한 기능을 지원하는 경우 타입 마다 각각의 코드를 작성하는 것이 아닌 코드를 재사용할 수 있게 된다

### 제네릭 사용 주의사항
1. 제네릭 타입의 객체 생성 불가
    - 제네릭 타입을 지정하여 객체를 생성할 수 없다
   
    ``` java
    class Test<T> {
        public void testMethod() {
            //  // Type parameter 'T' cannot be instantiated directly
            T t = new T();
        }
    }
    ```

2. 클래스 멤버(필드, 메소드, 블록)에 제네릭 타입이 사용 불가
    - static은 클래스 로드 시점에 메모리에 올라가며 특정 인스턴스에 종속되어 있지 않다
    - 클래스 로드 시점에 자료 타입이 지정되어 있어야 하는데 제네릭을 사용하게 되면 타입이 지정 되어 있지 않기 때문에 
    실행되는 시점에 타입을 알 수 없게 된다

    ``` java
    class Test<T> {
        // static 메소드의 매개변수 타입으로 사용 불가
        public static void testMethod(T t) {
        }
    }
    ```

### 제네릭 클래스, 인터페이스
- 제네릭을 사용하여 클래스와 인터페이스를 만들 수 있다 
- 클래스와 인터페이스 선언문 옆에 제네릭 타입 매개변수가 쓰이면 이를 제네릭 클래스, 인터페이스라고 한다
- 제네릭 인터페이스를 implement한 클래스에서도 오버라딩한 메소드를 제네릭 타입에 맞춰서 똑같이 구현해야 한다

``` java

// 제네릭 클래스
class Test<T> {
    private T t;

    public setT(T t) {
        this.t = t;
    }

    public getT() {
        return t;
    }
}

// 제네릭 인터페이스
interface ISample<T> {
    public void addElement(T t, int index);
    public T getElement(int index);
}

class Sample<T> implements ISample<T> {
    private T[] array;

    public Sample() {
        array = (T[]) new Object[10];
    }

    @Override
    public void addElement(T element, int index) {
        array[index] = element;
    }

    @Override
    public T getElement(int index) {
        return array[index];
    }
}

public static void main(String[] args) {

    Test<String> test = new Test<>();
    test.setT("String Type!");
    test.getT();

    Sample<String> sample = new Sample<>();
    sample.addElement("This is string", 5);
    sample.getElement(5);
}

```

### 제네릭 메소드
- 제네릭 메소드는 클래스, 인터페이스와 다르게 반환타입 전에 제네릭 타입을 선언하며 
파라미터로 받는 변수의 타입에 따라 해당 메소드의 타입이 정해진다
- 즉 클래스에서 사용하는 제네릭 유형과 독립적으로 타입이 정해진다
- 이런 방식이 필요한 이유는 static을 사용하기 위함이다
- 위에서 언급했듯이 클래스 멤버에 static을 사용할 수 없다 
하지만 제네렉 메소드를 사용하면 사용시 파라미터로 클래스와 별도의 타입을 지정할 수 있기 때문에 static을 사용할 수 있게 된다

``` java
class ClassName<E> {
	
	private E element;	// 제네릭 타입 변수
	
	void set(E element) {	// 제네릭 파라미터 메소드
		this.element = element;
	}
	
	E get() {	// 제네릭 타입 반환 메소드 
		return element;
	}
	
	<T> T genericMethod(T o) {	// 제네릭 메소드
		return o;
	}
 	
}

```
