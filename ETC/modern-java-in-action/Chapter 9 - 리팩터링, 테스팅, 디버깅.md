### 리팩터링

람다 표현식을 이용해서 간결하고 유연한 코드를 구현할 수 있었다 또한 이미 정의된 메소드가 있을 때는 메소드 참조를 사용해서 람다보다 더 간결하게 코드를 작성할 수 있었다 람다와 메소드 참조 스트림 등을 이용하여 더 가독성이 좋은 코드로 리팩토링 할 수 있다

### 가독성이 좋은 코드란?

일반적으로 가독성이 좋은 코드란 어떠한 코드를 다른 사람도 쉽게 이해할 수 있는 코드를 말한다 즉 가독성이 높은 코드를 작성하려면 다른 사람이 쉽게 이해하고 유지보수 할 수 있도록 코드를 작성해야하는 것을 의미한다 코드의 가독성을 높이려면 문서화, 표준 코딩 규칙을 준수하는 등의 노력이 필요하다

### 익명 클래스를 람다 표현식으로 리팩터링

하나의 추상 메소드를 구현하는 익명 클래스는 람다 표현식으로  리팩터링 할 수 있다
하지만 모든 익명 클래스를 람다 표현식으로 변환할 수 있는 것은 아니다

람다 표현식으로 변환할 수 없는 상황

1. **익명 클래스에서 this와 super를 사용할 때**
    - 익명 클래스의 this는 익명 클래스 자신을 가르키지만 람다에서 this는 람다를 감싸는 클래스(람다 표현식이 정의 된 클래스)를 가르킴
    - 익명 클래스의 super는 익명 클래스의 부모를 가르키고 외부에서 부모 클래스를 호출 할 수 없지만 람다의 super는 람다가 정의된 클래스의 부모 클래스를 가르킴
    
    ```java
    	// 익명 클래스
    class Outer {
        void display() {
            System.out.println("Outer display");
        }
        
        void testAnonymousClass() {
            Runnable runnable = new Runnable() {
                @Override
                public void run() {
                    System.out.println(this); // 익명 클래스의 인스턴스를 가리킴
                    Outer.super.display(); // 외부 클래스의 부모 클래스 메서드 호출 불가능, 컴파일 오류 발생
                }
            };
            runnable.run();
        }
    }
    
    // 람다 
    class Parent {
        void display() {
            System.out.println("Parent display");
        }
    }
    
    class Outer extends Parent {
        void display() {
            System.out.println("Outer display");
        }
        
        void testLambda() {
            Runnable runnable = () -> {
                System.out.println(this); // Outer 클래스의 인스턴스를 가리킴
                super.display(); // Parent 클래스의 display() 메서드를 호출
            };
            runnable.run();
        }
    }
    
    public class Main {
        public static void main(String[] args) {
            Outer outer = new Outer();
            outer.testLambda(); // "Outer display"와 "Parent display"가 출력됨
        }
    }
    ```
    
2. **익명 클래스는 감싸고 있는 클래스의 쉐도잉을 할 수 있지만 람다는 쉐도잉을 할 수 없다**
    
    <aside>
    💡 쉐도잉이란?
    밖의 블록에서 선언된 변수와 안의 블록에서 선언된 변수의 이름이 같을 시, 안의 블록 안에선 안의 블록에서 선언된 변수가 우선권을 갖는 것을 의미
    
    </aside>
    
    ```java
    // 쉐도잉 예시
    int a = 10;
    Runnable anonymousRunnable = new Runnable() {
        @Override
        public void run() {
            int a = 2; // 잘 돌아간다.
            System.out.println(a);
        }
    };
    
    Runnable lambdaRunnable = () -> {
        int a = 3; // 컴파일 에러
        System.out.println("lambda");
    };
    
    ```
    
3. **익명 클래스를 람다 표현식으로 바꾸면 콘텍스트 오버라이딩에 따른 모호함이 초래 될 수 있다**
    - 익명 클래스는 인스턴스화할 때 명시적으로 형식이 정해지는데 람다는 콘텍스트에 따라 달라짐
    
    ```java
    // 콘텍스트 예시
    public class Readability {
    
        public static void main(String[] args) {
            doSomething(new Task() { //Task 가 들어간다는 점에서 명시적
                @Override
                public void execute() {
                    System.out.println("Task!!");
                }
            });
    			
    				doSomething(() -> System.out.println("Task"));
    				// dosomething 이 두가지 버전이 존재한다. 어떤걸 해야할지 모호함 발생
    				// doSomething((Task) () -> System.out.println("Task")); 이렇게 해결가능
    
        }
    
        public static void doSomething(Runnable r){r.run();}
        public static void doSomething(Task task){task.execute();}
    
    }
    
    interface Task{
        public void execute();
    }
    ```
    
    결론
    
    - 변수를 받아 사용하는 형태의 익명 클래스는 람다를 사용할 때 이점이 더 많음
    - 콘텍스트가 달라질 염려가 없는 익명 클래스는 람다로 사용할 때 이점이 더 많음
    - 변수를 가릴 일이 없는 실행, 계산 등의 역할을 수행하는 익명 클래스는 람다가 더 이점이 많음

### 람다 표현식을 메소드 참조로 리팩터링하기

람다 표현식은 쉽게 전달할 수 있는 짧은 코드지만 메소드 참조를 이용하면 람다 보다 가독성을 높일수 있다
그 이유는 메소드 참조는 코드의 의도를 명확하게 파악할 수 있기 때문이다

```java
// 메소드 참조 사용 전 
public static void main(String[] args) {
    Map<Salary.SalaryLevel, List<Salary>> collect
            = workers.stream()
            .collect(Collectors.groupingBy((salary) -> {
        if (salary.getSalary() >= 10000) return Salary.SalaryLevel.고액연봉자;
        else if (salary.getSalary() >= 3000) return Salary.SalaryLevel.일반연봉자;
        else return Salary.SalaryLevel.저임금노동자;
    }));

    System.out.println(collect);
}

// 메소드 참조 사용 후
public static void main(String[] args) {
    Map<Salary.SalaryLevel, List<Salary>> collect
            = workers.stream()
            .collect(Collectors.groupingBy(Salary::getSalaryLevel)); // 훨씬 좋은 가독성을 보인다.
    System.out.println(collect);
}

class Salary {
	// 메소드 참소에 사용할 메소드를 추가해줌
	public Salary.SalaryLevel getSalaryLevel(){ // 추가 해줌
	    if (getSalary() >= 10000) return SalaryLevel.고액연봉자;
	    else if (getSalary() >= 3000) return SalaryLevel.일반연봉자;
	    else return SalaryLevel.저임금노동자;
}

```

### 명령형 데이터 처리를 스트림으로 리팩터링하기

이론적으로 반복자를 이용한 기존의 모든 컬렉션 처리 코드를 스트림 API로 바꿔야한다. 이유가 뭘까? 스트림 API는 데이터 처리 파이프라인의 의도를 명확하게 보여준다. 또한 스트림은 쇼트서킷과 게으름이라는 강력한 최적화뿐 아니라 멀티코어 아키텍처를 활용할 수 있다 결국 스트림을 사용하는 것이 **의도**를 명확하게 보여주고 고수준의 **활용**을 할 수 있기 때문이다

```java
// 명령형 코드
List<Salary> list = new ArrayList<Salary>();
for(Salary salary : list){ //추출
	if (salary.getSalary() > 5000) //필터링
		list.add(salary.getName());  // 결과
}
/*
	위와 같은 명령형 코드의 문제는 필터링(봉급의 5000초과)와 추출(봉급 가져오기)로 엉켜있다는 점이다.
	이코드의 문제는 이뿐아니라 병렬로 실행하기엔 다소 무리가 있다는 것이다.
*/

// 스트림 리팩터링
salaryList.parallelStream() // 병렬실행
					.filter(salary -> salary.getSalary() > 5000) // 필터링
					.map(Salary::getName) // 추출
					.collect(Collector.toList()); // 결과
```

### 코드의 유연성 개선 - 함수형 인터페이스 적용

람다 표현식을 이용하려면 함수형 인터페이스가 필요하다 따라서 함수형 인터페이스를 코드에 추가 해야한다 

- **조건부 연기 실행**
    
    ```java
    if(logger.isLoggable(Log.FINER)) logger.finer("Problem : " + generateDiagnostic());
    /*
    	평범해 보이는 코드지만 이코드에는 두가지의 문제점이 있다고 한다.
    	1. logger의 상태가 isLoggable() 이라는 메서드에 의해 클라이언트 코드로 노출된다
    	2. 메시지를 로깅할 때마다 logger 객체의 상태를 매번확인해야 하며 로깅의 수준을 변경하려면
    		 모든 분기문에 로깅 수준을 변경해야한다
    	* 클라이언트 코드란?
    		-> 클라이언트 코드란 코드를 호출하는 코드를 의미
    		-> 서버코드는 호출을 당하는 코드를 의미
    */
    
    logger.log(Level.FINER, "Problem : " + generateDiagnostic());
    /*
    	내부적으로 logger 객체가 적절한 수준으로 설정되어 있는지 확인하는게 더 좋다
    	1. 더이상 isLoggable()이 클라이언트 코드에 존재하지 않는다
    	2. 객체 내부에 로깅을 하는 부분은 내부적으로 호출된다 단지 클라이언트 코드에선 수준과 내용만 정의
    	3. 다른 곳에서도 다 불러 사용할 수 있는 코드가 된다
    */
    
    class Logger {
    	public void log(Level level, Supplier<String> msgSupplier) { // 함수형 인터페이스가 적용된 사례
        if (!isLoggable(level)) { // 내부에서 로거의 상태 확인
            return;
        }
        LogRecord lr = new LogRecord(level, msgSupplier.get());
        doLog(lr);
    }
    ```
    
- **실행 어라운드**
    
    매번 같은 준비, 종료 과정을 반복적으로 수행하는 코드가 있다면 이를 람다로 변환할 수 있다 로직을 재사용한다는 점에서 중복코드를 제거할 수 있다
    
    ```java
    /*
    	만약 시작과 종료를 알리는 라인을 제작해야한다. (아래의 예시)
    ----------시작----------
    프로그램 실행
    ----------종료----------
    	프로그램의 실행에 필요한 부분은 호출하는 부분에 정의할 수 있고 나머지 불필요하고 동일한 로직은 메소드에
    	적용시켜 중복코드를 제거했다.
    */
    
    printUi("프로그램 실행", System.out :: println);
    
    public static void printUi(String content, Consumer<String> consumer){
            System.out.println("----------시작----------");
            consumer.accept(content);
            System.out.println("----------종료----------");
    }
    
    ```
    

### 람다로 객체지향 디자인 패턴 리팩터링하기

- 전략
    - 한 유형의 알고리즘을 보유한 상태에서 런타임에 적절한 알고리즘을 선택하는 기법
    - 다양한 기준을 갖는 입력값 검증, 다양한 파싱 방법 사용, 입력 형식 설정 등에 활용 가능
    - 전략 패턴 구성
        1. 알고리즘을 나타내는 인터페이스
        2. 다양한 알고리즘을 나타내는 인터페이스 구현
        3. 전략 객체를 사용하는 클라이언트
    
    ```java
    // ValidationStrategy
    public interface ValidationStrategy {
        boolean execute(String s);
    }
    
    // IsAllLowerCase
    public class IsAllLowerCase implements ValidationStrategy {
        public boolean execute(String s) {
            return s.matches("[a-z]+");
        }
    }
    
    // IsNumeric
    public class IsNumeric implements ValidationStrategy {
        public boolean execute(String s) {
            return s.matches("[0-9]+");
        }
    }
    // Validator 
    public class Validator {
        private final ValidationStrategy validationStrategy;
    
        public Validator(ValidationStrategy validationStrategy) {
            this.validationStrategy = validationStrategy;
        }
    
        public boolean validate(String s) {
            return validationStrategy.execute(s);
        }
    }
    // 람다 사용 전 Main
    public class Main {
        public static void main(String[] args) {
            Validator numericValidator = new Validator(new IsNumeric());
            System.out.println(numericValidator.validate("aaaa"));    // false
            Validator lowerCaseValidator = new Validator(new IsAllLowerCase());
            System.out.println(numericValidator.validate("bbbb"));    // true
        }
    }
    
    // 람다 사용 후 Main
    public class Main {
        public static void main(String[] args) {
            Validator lowerCaseValidator = new Validator(s -> s.matches("[0-9]+"));
            System.out.println(numericValidator.validate("aaaa"));    // false
            Validator numericValidator = new Validator(s -> s.matches("[a-z]+"));
            System.out.println(numericValidator.validate("bbbb"));    // true
        }
    }
    
    /*
    	람다를 이용하면 새로운 클래스를 구현할 필요 없이 
    	직접 코드를 전달하여 불필요한 코드를 줄일 수 있다
    */ 
    ```
    
    **결론**
    
    - 결국 ValidatorStrategy는 FunctionalInterface와 같이 동작한다
    - 같은 디스크립터를 가지고 있다면 람다로 구현하는 것이 더 가독성을 높일 수 있다
    - 람다 표현식은 코드 조각을 캡슐화 할 수 있다
- 템플릿 메소드
    - 알고리즘의 개요를 제시한 다음에 알고리즘의 일부를 고칠 수 있는 유연함을 제공해야 할 때 사용하는 패턴
    - 즉 알고리즘의 기본적인 템플릿을 제공하고 세부적인 사항을 고칠 수 있도록 하는 패턴
    
    ```java
    // 메인이 되는 템플릿 메소드를 가진 추상 클래스
    public abstract class OnlineBanking{
    		// 템플릿 메소드
        public void processCustomer(String id){
            Customer customer = DataBase.getCustomerWithId(id);
            CustomerPrinter(customer);
        }
        protected abstract void CustomerPrinter(Customer customer);
    }
    
    // 상속 받은 클래스
    public class PrintTax extends OnlineBanking{
    
        @Override
        protected void CustomerPrinter(Customer customer) {
            System.out.println("TAX : " + customer.getMoney());
        }
    }
    
    public class Balance extends OnlineBanking{
    
        @Override
        protected void CustomerPrinter(Customer customer) {
            System.out.println("balance : " + customer.getMoney());
        }
    }
    // 람다 사용 전
    public static void main(String[] args) {
          OnlineBanking PrintTaxOnlineBanking = new PrintTax();
          PrintTaxOnlineBanking.processCustomer("김");
    
          OnlineBanking BalanceOnlineBanking = new Balance();
          BalanceOnlineBanking.processCustomer("김");
    }
    
    // 람다 사용
    public static void main(String[] args) {
            new OnlineBanking()
    					.processCustomer("김",customer -> System.out.println("Tax :" + customer.getMoney()));
            new OnlineBanking()
    					.processCustomer("김",customer -> System.out.println("Balance :" + customer.getMoney()));
    }
    // 람다를 사용하도록 메소드 인자를 변경
    class OnlineBanking{
        public void processCustomer(String id, Consumer<Customer> CustomerPrinter){ 
            Customer customer = DataBase.getCustomerWithId(id);
            CustomerPrinter.accept(customer);
        }
    }
    ```
    
    결론
    
    - 템플릿 메서드 패턴에 람다를 적용시킨다면 자잘한 코드조각을 분리할 수 있다
    - 상속없이 직접 동작을 추가할 수 있다 (컴포넌트 형식의 클래스를 상속하여 추가하는 것이 아닌 직접 람다로 구현)
- 옵저버
    - 어떤 **이벤트가 발생했을 때 하나의 객체가 다른 객체에 알림**을 보내는 상황에 사용
    
    ```java
    // 옵저버 인터페이스
    interface Observer {
        void notify(String tweet);
    }
    // 주체 인터페이스
    interface Subject{
        void registerObserver(Observer observer);
        void notifyObserver(String tweet);
    }
    
    // 주체를 상속받은 클래스
    class Feed implements Subject{
        List<Observer> observers = new ArrayList<>();
    
        @Override
        public void registerObserver(Observer observer) {
            observers.add(observer);
        }
    
        @Override
        public void notifyObserver(String tweet) {
            observers.forEach(observer -> observer.notify(tweet));
        }
    }
    // 옵저버를 상속받은 클래스
    class ConcreteObserver {
    
        public static class NyTimes implements Observer{
            @Override
            public void notify(String tweet) {
                System.out.println("NyTimes : " + tweet);
            }
        }
    
        public static class Guardian implements Observer{
            @Override
            public void notify(String tweet) {
                System.out.println("Guardian : " + tweet);
            }
        }
    
        public static class LeMonde implements Observer{
            @Override
            public void notify(String tweet) {
                System.out.println("LeMonde : " + tweet);
            }
        }
    }
    
    // 람다 사용 전
    public static void main(String[] args) {
        Subject f = new Feed();
        f.registerObserver(new ConcreteObserver.NyTimes());
        f.registerObserver(new ConcreteObserver.Guardian());
        f.registerObserver(new ConcreteObserver.LeMonde());
    
        f.notifyObserver("트윗 전송");
    }
    
    // 람다 사용
    public static void main(String[] args) {
          Subject f = new Feed();
          f.registerObserver(tweet -> System.out.println("NyTimes : " + tweet));
          f.registerObserver(tweet -> System.out.println("Guardian : " + tweet));
          f.registerObserver(tweet -> System.out.println("LeMonde : " + tweet));
    
          f.notifyObserver("트윗 전송");
    }
    ```
    
    **결론**
    
    - 옵저버 패턴에서의 람다는 불필요한 코드 또는 상속을 통한 구현을 제거하는데 도움이 된다
    - 단점으로 생각해볼 것은 register에 추가하는 과정에 람다 표현식이 간결하다면 충분히 사용하지만 만약 엄청나게 복잡한 경우 상속을 통한 구현이 조금 더 좋은 코드라는 생각을 할 수 있다
    - 옵저버가 상태를 가진다면 (consumer가 아닌) 다른 형태의 로직이 작동한다면 상속을 생각해볼 필요가 매우 있다
- 의무 체인
    - 작업 처리 객체의 체인(다음 동작)을 만들 때 의무 체인을 패턴을 사용한다
    - 즉 한 객체가 어떤 작업을 처리하고 다른 객체에게 결과를 전달하는 식이다
    
    ```java
    // 체인을 걸 추상 클래스 추가
    public abstract class ProcessingObject<T> {
        protected ProcessingObject<T> successor;
        public void setSuccessor(ProcessingObject<T> successor) {
            this.successor = successor;
        }
    
        public T handler(T input) {
            T output = work(input);
            if (successor != null) return successor.handler(output);
            return output;
        }
    
        abstract protected T work(T input);
    }
    
    /*
    	handler 메서드를 보면 해야할 작업을 처리(work)를 한 후 
    	설정된 successor이 있으면 객체를 넘겨 successor에 handler메서드를 실행시킨다
    */
    
    // 예제 클래스
    public class Car {
        public boolean hasTire; // 타이어 조립 여부
        public boolean hasDoor; // 문 조립 여부
        public Car() {
        	this.hasTire = false;
            this.hasDoor = false;
        }
        
        public void setHasDoor(boolean hasDoor) {
            this.hasDoor = hasDoor;
        }
    
        public void setHasTire(boolean hasTire) {
            this.hasTire = hasTire;
        }
    
        @Override
        public String toString() {
            return "Car{" +
                    "hasTire=" + hasTire +
                    ", hasDoor=" + hasDoor +
                    '}';
        }
    }
    
    // 체인을 상속받는 클래스
    public class TireFactory extends ProcessingObject<Car> { // 타이어 조립 공장
        @Override
        protected Car work(Car car) {
            car.setHasTire(true);
            return car;
        }
    }
    
    public class DoorFactory extends ProcessingObject<Car> { // 문 조립 공장
        @Override
        protected Car work(Car car) {
            car.setHasDoor(true);
            return car;
        }
    }
    
    // 람다 사용 전
    public static void main(String[] args) {
    		ProcessingObject<Car> factory1 = new TireFactory();
    		ProcessingObject<Car> factory2 = new DoorFactory();
    		factory1.setSuccessor(factory2);
    		Car car = factory1.handler(new Car());
    }
    
    // 람다 사용 후 
    public static void main(String[] args) {
    		UnaryOperator<Car> factory1 = (Car car) -> { car.setHasTire(true); return car; };
    		UnaryOperator<Car> factory2 = (Car car) -> { car.setHasDoor(true); return car; };
    		Function<Car, Car> pipeline = factory1.andThen(factory2);
    		Car car = pipeline.apply(new Car());
    }
    ```
    
    **결론**
    
    - 의무체인 또한 간결하게 줄일 수 있는 코드가 될 수 있다.
    - 의무체인의 구현은 andThen으로 매우 잘 이루어져 있다.
- 팩토리 메서드
    - 인스터스화 로직(객체 생성로직, new 호출)을 클라이언트에 노출하지 않고 객체를 만들 때 사용
    
    ```java
    // 예제 클래스
    public class Car {
        private String name;
        private CarType type;
        public Car(String name, CarType type) {
            this.name = name;
            this.type = type;
        }
    }
    
    // 상속받은 자식 클래스
    public class SmallCar extends Car {
        public SmallCar(String name) {
            super(name, CarType.SMALL);
        }
    }
    
    public class SedanCar extends Car {
        public SedanCar(String name) {
            super(name, CarType.SEDAN);
        }
    }
    
    public class SuvCar extends Car {
        public SuvCar(String name) {
            super(name, CarType.SUV);
        }
    }
    
    // 람다 사용 전 -> 팩토리 클래스를 통해 팩토리 메소드 구현
    public class CarFactory {
        public Car createCar(CarType carType) {
            switch (carType) {
                case SMALL:
                    return new SmallCar("모닝");
                case SEDAN:
                    return new SedanCar("아반테");
                case SUV:
                    return new SuvCar("투싼");
                default:
                    return null;
            }
        }
    }
    
    // 람다 사용 후
    public class CarFactory {
        private final Map<CarType, Supplier<Car>> map = Map.ofEntries(
            Map.entry(CarType.SMALL, () -> new SmallCar("모닝")),
            Map.entry(CarType.SEDAN, () -> new SedanCar("아반테")),
            Map.entry(CarType.SUV, () -> new SuvCar("투싼"))
        );
    
        public Car createCar(CarType carType) {
            Supplier<Car> supplier = map.get(carType);
            if (supplier != null) return supplier.get();
            throw new IllegalArgumentException("No Such Car type");
        }
    }
    ```
    
    **결론** 
    
    - 팩토리메서드를 이용하면 더 이상 클라이언트에 생성자 호출, 인스턴스화로직을 노출할 필요가 없다
    - 중요한 점은 **Factory 만 고친다면 다른 부분의 수정은 더 이상 필요없다**
    - 팩토리 메서드를 이용하면 여러 곳에서 Factory만 불러와서 사용하면 더 이상의 로직 수정이 필요없다
    - **여러 인수를 받거나 받아야 하는 상황이라면 새로운 함수형 인터페이스를 만들어서 인자로 사용하면 된다**
