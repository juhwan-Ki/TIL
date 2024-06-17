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

### 코드의 유연성 개선 - **함수형 인터페이스 적용**

람다 표현식을 이용하려면 함수형 인터페이스가 필요하다 따라서 함수형 인터페이스를 코드에 추가 해야한다 

1. **조건부 연기 실행**

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

1. **실행 어라운드**

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
