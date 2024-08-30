자바9에서 가장 많이 거론되는 새로운 기능은 모듈 시스템이다 모듈 시스템이 무엇인지 모듈 시스템이 어떤 문제를 해결할 수 있는지 자바 모듈 시스템이 무엇인지 알아본다

### 관심사 분리

관심사 분리는(separation of concerns, SoC) 컴퓨터 프로그래밍을 고유의 기능으로 나눈것을 권장하는 원칙으로 즉 한번에 한 가지 일만 처리할 수 있도록 나누는 것 이다 SoC를 적용하면 각각의 기능을 모듈로 분리하여 서로 거의 겹치지 않는 코드 그룹으로 분리할 수 있다 (코드가 단위별로 하나의 관심사에 충실할 수 있도록 만드는 것) SoC 원칙은 모델, 뷰, 컨트롤러 같은 아키텍처 관점 그리고 복구 기급얼 비즈니스 로직과 분리 하는 등의 하위 수준 등의 상황에 유리하게 적용할 수 있다

SoC의 장점 

1. 개별 기능을 따로 작업할 수 있으므로 쉽게 협업할 수 있다
2. 개별 부분을 재사용 하기 쉽다
3. 전체 시스템을 쉽게 유지보수 할 수 있다
4. 낮은 결합도를 가지기 때문에 각각의 코드가 서로 얽혀있지 않고 독립적으로 잘 분리된다
    - 결합도: 의존성의 정도를 나타내며 다른 모듈에 대해 얼마나 많은 지식을 갖고 있는지를 나타내는 척도, 결합도가 높을 수록 함께 변경해야하는 모듈의 수가 늘어남
5. 높은 응집도를 가지기 때문에 유사한 내용끼리 비슷한 위치에 잘 모여 있음
    - 응집도: 내부 요소들이 연관되어 있는 정도, 하나의 목적을 위해 긴밀히 협력한다면 그 모듈은 높은 응집도를 가짐

### 정보 은닉

정보 은닉은 세부 규현을 숨기도록 장려하는 원칙으로 정보 은닉을 통해 세부 구현을 숨겨 프로그램의 어떤 부분이 변경되더라도 다른 부분까지 영향을 미치는 가능성을 줄일 수 있다 즉 코드를 관리하고 보호하는데 유용한 원칙이다

정보 은닉 기법

1. 객체의 구체적인 타입 은닉(업 캐스팅)
    - 객체의 타입을 부모 객체의 타입으로 형변환 하여 구체적인 객체 타입을 은닉함
    
    ```java
    abstract class Shape{
        abstract public void draw(); 
        // 하나의 공통 메소드로 각 자식 클래스 고유의 메서드 동작이 실행되도록 추상화
    }
    
    class Rectangle extends Shape{
        public void draw(){ 
        	rectangle();
        }   
        private void rectangle(){ 
        	System.out.println("rectangle"); 
        }
    }
    
    class Triangle extends Shape{
        public void draw(){ 
        	triangle();
        }   
        private void triangle(){ 
        	System.out.println("triangle"); 
        }
    }
    
    ...
    public class Myclass {
    	// ...
        public void method() {
            Shape shape = new Rectangle(); // 다형성
            shape.draw();  // "rectangle" - 구체적인 객체 타입에 의존적이지 않게됨
        }
    }
    ```
    
2. 객체의 필드 및 메소드 은닉(캡슐화)
    - 캡슐화를 통하여 객체의 필드 및 메소드를 은닉함(private 사용)
    
    ```java
    class Process{
        private void init(){} // 은닉 메서드
    
        private void process(){} // 은닉 메서드
    
        private void release(){} // 은닉 메서드
    
        public void work(){ // 공개 메서드
            init(); // 은닉한 메서드 실행
            process();
            release();
        }
    }
    ```
    
3. 구현 은닉(인터페이스 & 추상 클래스)
    - 공개 메소드를 은닉 메소드들과 구분하고 통합적으로 관리하기 위해 인터페이스 & 추상 클래스를 사용
    
    ```java
    interface InterProcess {
        public void work(); // 추상 메소드
    }
    
    class Process implements InterProcess {
        private void init(){} // 은닉 메서드
        private void process(){} // 은닉 메서드
        private void release(){} // 은닉 메서드
    
        public void work(){ // 공개 메서드 + 메소드 구체화
            init(); 
            process();
            release();
        }
    }
    
    public class Main {
        public static void main(String[] args) {
            InterProcess c = new Process();
            c.work();
        }
    }
    ```
    

### 자바 모듈 시스템을 설계한 이유

자바9 이전까지는 모듈화된 소프트웨어 프로젝트를 만드는데 한계가 있었다 자바는 클래스 패키지 JAR 세 가지 수준의 코드 그룹화를 제공한다 클래스와 관련해 자바는 접근 제한자와 캡슐화를 지원했다 하지만 패키지와 JAR 수준에서는 캡슐화를 거의 지원하지 않았다

### **제한된 가시성 제어**

네 가지 접근자(public, protected, default, private)가 존재하지만 패키지의 가시성 제어 기능은 무의미하다 한 패키지의 클래스와 인터페이스를 다른 패키지에서 사용하려면 public으로 선언 해야 하는데 이렇게되면 내부적으로 사용할 목적이어도 다른 곳에서 사용할 수 있어서 코드가 노출되어 기존 애플리케이션을 망가뜨리지 않고 라이브러리 코드를 바꾸기가 어려워 진다

- 접근 제어자
    - public : 모든 영역에서 접근 O
    - protected : 같은 패키지 또는 해당 클래스를 상속받은 외부 패키지의 클래스에서 접근 O
    - default : 동일한 패키지 내에서만 접근 O
    - private : 외부에서 접근 불가능 즉 같은 클래스 내에서 접근 O

### **클래스 경로와 JAR 조합의 약점**

자바는 클래스를 모두 컴파일 한 후 한 개의 JAR 파일에 넣고 클래스 경로에 JAR 파일을 추가하여 JVM이 동적으로 클래스 경로에 정의된 클래스를 필요할 때 읽는 방식으로 동작한다 
이러한 동작 방식으로 인한 몇가지 약점이 존재한다

1. 클래스 경로에는 같은 클래스를 구분하는 버전 개념이 없다
    - 같은 라이브러리의 다른 버전을 사용하면 어떤 일이 일어날지 예측할 수 없음
    - ex ) JSONParser 버전이 1.0인지 2.0인지 지정할 수 없기 때문에 두 가지 버전의 라이브러리가 존재하게 되면 어떤 일이 일어날지 알 수 없음
2. 클래스 경로는 명시적인 의존성을 지원하지 않는다
    - 자바, JVM 명시적인 의존성을 정의하지 않아 한 JAR가 다른 JAR에 포함된 클래스 집합을 사용한다고 명시적으로 의존하지 않기 때문에 누락된 JAR를 확인하기 어렵고, 충돌이 발생할 수 있음
    - 정상적으로 실행할 때까지 클래스를 추가하거나 제거해봐야 함
    - 메이븐이나 그레이들 같은 빌드 도구가 해결하는데 도움을 줌

### 거대한 JDK

자바 개발 키트(JDK)는 자바 프로그램을 만들고 실행하는 데 도움을 주는 도구의 집합이다 시간이 흐르면서 JDK의 덩치가 커졌고, 모바일이나 JDK 전부를 필요로 하지 않는 클라우드 환경에서 문제가 되었다 

또한 자바의 낮은 캡슐화 지원 때문에 JDK의 내부 API가 외부에 공개되었고, 여러 라이브러리에서 JDK 내부 클래스를 사용했다 결과적으로 호환성을 깨지 않고는 관련 API를 바꾸기 어려운 상황이 되었다 이런 문제들 때문에 JDK 자체도 모듈화할 수 있는 자바 모듈 시스템 설계의 필요성이 제기되었다 즉 JDK에서 필요한 부분만 골라서 사용하고, 클래스 경로를 쉽게 유추할 수 있으며, 플랫폼을 진화시킬 수 있는 강력한 캡슐화를 제공할 새로운 건축 구조가 필요했다

### 자바 모듈

자바 9부터 Java Platform Module System (JPMS) 또는 줄여서 '모듈'이라고 알려진 새로운 수준의 추상화를 도입한다 모듈에는 종속성(dependency)의 개념이 있으며, Public API를 내보내고 구현 세부 정보를 숨김/비공개 상태로 유지할 수 있다

모듈이란 서로 밀접하게 연관된 패키지들과 리소스들의 그룹이다 즉, "자바 패키지들의 패키지(package of Java Packages)"라고도 볼 수 있는데, 이는 코드의 재사용성을 높이기 위해 추상화 단계를 하나 더 추가한 것이라고 볼 수 있다

모듈은 module이라는 새 키워드에 이름과 바디를 추가해서 정의하고 모듈 디스크립터는 module-info.java라는 특별한 파일에 저장한다 exports 에는 노출하고자 하는 패키지, requires에는 필요로 하는 모듈을 정의한다

<aside>
💡 라이브러리 vs 모듈
	
라이브러리는 외부 프로그램에서 라이브러리 내부에 포함된 모든 패키지에 접근할 수 있지만, 모듈은 일부 패키지를 **은닉**하여 외부 프로그램에서 접근할 수 없게 할 수 있다

또한, 모듈은 자신이 실행할 때 필요로 하는 의존 모듈을 모듈 디스크립터(module-info.java)에 기술하여, 모듈간 의존관계를 쉽게 파악할 수 있도록 도와준다

이처럼 모듈별로 개발하고 조립하는 방식을 사용하면 재사용성 및 유지보수에 유리하기 때문에, 규모가 큰 프로젝트일수록 기능별로 모듈화(modulization)해서 개발할 수 있다

라이브러리와 모듈의 가장 큰 차이는 모듈은 은닉을 제공한다는 점이다

</aside>

### 자바 모듈 시스템 기초

```java
// 프로젝트 디렉터리 구조

|-- expenses.application // 모듈명
  |-- module-info.java // 모듈 디스크립터는 소스 코드 파일 루트에 위치해야함
  |-- com // 여기서 부터 패키지
    |-- example
      |-- expenses
        |-- application
          |-- ExpensesApplication.java

...          
// module-info.java 
module expenses.application {
}

/*
	모듈 디스크립터는 소스 코드 파일 루트에 위치해야 하며 
	모듈의 의존성, 노출할 기능 등을 정의한다 
*/
```

보통 IDE와 빌드 시스템에서 명령을 자동으로 처리해주긴 하지만 모듈 소스 디렉터리에서는 다음 명령을 실행하게 된다 

```java
// 컴파일 과정에 module-info.java 가 새롭게 추가되었다
javac module-info.java
    com/example/expenses/application/ExpensesApplication.java -d target
        
jar cvfe expenses-application.jar
    com.example expenses.application.ExpensesApplication -C target
    
java --module-path expenses-application.jar
     --module expenses/com.example.expenses.application.ExpensesApplication
```

`.class` 파일을 실행할 때에도 다음 두가지 옵션도 추가되었다

- `-module-path` : 어떤 모듈을 로드할 수 있는지 지정 (클래스 파일을 지정하는 `-classpath` 인수와 다름)
- `-module` : 이 옵션을 실행할 메인 모듈과 클래스를 지정

### exports

exports는 다른 모듈에서 사용할 수 있도록 특정 패키지를 공개 형식으로 만든다 모듈 시스템은 화이트 리스트 기법으로 캡슐화를 제공하므로, 다른 모듈에서 사용할 수 있는 기능을 명확하게 명시해야 한다

```java
module expenses.readers {
    exports com.example.expenses.readers;      // 패키지명
    exports com.example.expenses.readers.file;
    exports com.example.expenses.readers.http;  
}
```

### requires

requires는 의존하고 있는 모듈을 지정한다 기본적으로 모든 모듈은 java.base라는 플랫폼 모듈에 의존하는데 이 플랫폼 모듈은 net, io, util등의 자바 메인 패키지를 포함 한다 java.base 는 항상 기본적으로 필요한 모듈이기 때문에 명시적으로 정의할 필요가 없기에 java.base 외의 모듈을 임포트할 때 requires를 사용한다

```java
module expenses.readers {
    requires java.base;  // 패키지 명이 아닌 모듈명을 적어야함
    
    exports com.example.expenses.readers;      // 패키지명
    exports com.example.expenses.readers.file;
    exports com.example.expenses.readers.http;  
}

/*
	java.base는 항상 필요한 모듈이기 때문에 명시적으로 작성하지 않아도 된다
	import java.lang 패키지나 extends Object를 하지 않는 것과 같은 원리이다
*/
```

### 이름 명명규칙

모듈의 이름은 오라클에서 패키지명처럼 인터넷 도메인명을 역순으로 지정하도록 권고한다 (ex : com.iteratrlearning.training) 더욱이 노출된 주요 API 패키지와 이름이 같아야 한다는 규칙도 따라야 한다

### 자동 모듈

모듈화가 되지 않은 외부 라이브러리를 사용하기 위해 지원되는 것을 자동 모듈이라고 말한다 자바는 JAR를 자동 모듈이라는 형태로 적절하게 변환하여 모듈 경로상에 있으나 module-info 파일을 가지지 않는 모든 JAR파일을 자동 모듈화 한다 

자동 모듈은 암묵적으로 자신의 모든 패키지를 노출시키며 자동 모듈의 이름은 JAR 이름을 이용해서 정해진다(jar —describe-module 인수를 사용해서 이름을 변경할 수 있음)

### 모듈 정의와 구문들

1. requries
    - 컴파일 타임과 런타임에 한 모듈이 다른 모듈에 의존함을 정의한다
    
    ```java
    module com.proudct.application {
    	requires com.product.ui;
    }
    
    /*
    	com.product.ui에서 외부로 노출한 공개 형식에 접근하여 
    	com.proudct.application에서 사용할 수 있게 됨
    */
    ```
    
2. exports
    - 지정한 패키지르 다른 모듈에서 이용할 수 있도록 공개 형식으로 만든다
    - 아무 패키지도 공개 하지 않는 것이 기본 설정이며 어떤 패키지를 공개할 것인지를 명시적으로 지정함으로 캡슐화를 높일 수 있다
    
    ```java
    module com.proudct.ui {
    	requires com.product.core; // 모듈명
    	exports com.product.ui.color;  // 패키지명
    }
    
    /*
    	exports는 패키명을 인수로 받지만 requires는 모듈명을 인수로 받음
    */
    ```
    
3. requires transitive
    - 다른 모듈이 제공하는 공개 형식을 한 모듈에서 사용할 수 있다고 지정
    즉 다른 모듈에서 의존하고 있는 모듈도 같이 사용할 수 있다
    
    ```java
    module com.proudct.ui {
    	requires transitive com.product.core; // 모듈명
    	exports com.product.ui.color; 
    }
    
    ...
    module com.proudct.application {
    	requires com.product.ui;
    }
    
    /*
    	requires transitive를 통해 com.proudct.application에서도
    	com.product.core에 있는 공개 형식에 접근하여 사용할 수 있게됨
    	
    	requires transitive를 하지 않았으면 com.proudct.application에서 
    	requires를 통해서 다시 정의를 해야함
    	
    	즉 다른 모듈에서 참조하는 모듈까지 가져다가 사용할 수 있게함
    */
    ```
    
4. exports to
    - 특정 패키지를 특정 모듈에만 공개하도록 제어한다
    
    ```java
    module com.proudct.ui {
    	requires transitive com.product.core; 
    	// 특정 모듈(com.product.ui.frame)에만 com.product.ui.color 패키지를 공개
    	exports com.product.ui.color to com.product.ui.frame;
    }
    
    /*
    	exports to를 사용해서 com.product.ui.frame 모듈에만 
    	com.product.ui.color을 공개하도록 제어한다
    	
    	exports to 는 모듈의 캡슐화를 더욱 강화하기 위해 사용되고 사용하게 되면 
    	코드의 일부가 의도한 모듈에만 접근 가능하고, 
    	불필요한 모듈에서는 접근이 불가능하게 되어 보안과 모듈 간 결합도를 낮출 수 있다
    	이 방식은 특히 라이브러리 설계에서 중요한 역할을 하며, 
    	API의 일부만 특정 사용자(모듈)에 노출할 수 있다
    	
    	exports: 패키지를 모든 모듈에 공개
    	exports to: 패키지를 특정 모듈에만 공개
    */
    ```
    
5. open, opens
    - 리플렉션(reflection)을 사용하는 경우, 모듈의 캡슐화된 패키지에 대한 접근을 제어하는 데 사용된다 모듈 내의 패키지를 외부 모듈이 리플렉션을 통해 접근할 수 있도록 허용할지 여부를 결정한다
    - 리플렉션은 객체 지향 프로그래밍 언어에서 런타임에 프로그램의 구조와 상태를 검사하고 수정할 수 있는 기능을 말한다 쉽게 말해, 프로그램이 실행되는 동안 클래스, 메서드, 필드 등과 같은 객체의 정보를 동적으로 탐색하고 조작할 수 있는 방법을 제공한다
    - opens:
        - 특정 패키지를 리플렉션을 통해 열 수 있다
        - `opens to`를 사용해 특정 모듈에만 리플렉션 접근을 허용할 수 있다
        - 기본적으로 해당 패키지를 열어도, 소스 코드의 접근 권한을 변경하지는 않는다 
        즉, 컴파일 시점에서는 여전히 접근 제한이 적용된다
    - open:
        - 모듈 전체를 리플렉션에 대해 연다
        - 모듈 내 모든 패키지가 리플렉션을 통해 접근 가능하게 된다
