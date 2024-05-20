### Initialization Block

- 클래스 필드의 초기화만을 담당하는 블록으로 생성자 보다 먼저 호출 되며 static 키워드 유무에 따라 Instance Initialization Block, Static Initialization Block으로 나뉘게 된다
- 초기화 블록에는 다양한 명령문 및 제어문을 사용할 수 있으므로 복잡한 초기화를 해야 할 경우 유용하게 사용된다

### Static Initialization Block (Class Initialization Block)

- 클래스가 JVM에 처음 로드될 때(즉 메모리에 처음으로 로딩 될 때) 클래스 변수가 준비된 후 자동으로 실행되는 블록으로 한 번만 실행된다
- static block은 클래스 수준의 작업에 사용되며, 주로 클래스 변수(static 변수)의 초기화나 클래스 수준의 초기화 작업을 수행할 때 활용된다
- 하나의 클래스 안에 여러개 static block을 넣을 수 있다

```java
public class StaticBlockTest {
	int a;
	int b;
	static {
		System.out.println("static block");
		a = 10;
		b = 5; 	
	}
}
/*
	static block을 통해 클래스가 로딩 된 후 자동으로 변수를 초기화 할 수 있음
*/
```

### Instance Initialization Block

- 생성자와 마찬가지로 인스턴스가 생성될 때마다 실행되며 생성자보다 먼저 실행된다
- 여러 개의 생성자가 있을 때 모든 생성자에서 공통으로 수행되어야 할 코드를 인스턴스 초기화 블록에 포함하여 사용하면 코드의 중복을 막을 수 있다
- 인스턴스 변수의 초기화는 주로 생성자를 사용하기 때문에, 인스턴스 초기화 블럭은 잘 사용되지 않는다

```java
class Car {

    private String modelName;
    private int modelYear;
    private String color;
    private int maxSpeed;
    private int currentSpeed; 
    
		// 인스턴스 블록
    {
        this.currentSpeed = 0;
    }

    Car() {}
    
    Car(String modelName, int modelYear, String color, int maxSpeed) {
        this.modelName = modelName;
        this.modelYear = modelYear;
        this.color = color;
        this.maxSpeed = maxSpeed;
    }

    public int getSpeed() {
        return currentSpeed;
    }

}

public class Test {
  public static void main(String[] args) {
      Car myCar = new Car();
      System.out.println(myCar.getSpeed());

}
```
