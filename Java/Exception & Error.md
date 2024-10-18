### 에러의 종류

- 논리 에러(Logic Error)
    - 이른바 버그라고 생각하면 되며 프로그램이 실행하고 동작하는데 아무런 문제가 없는 오류지만, 결과가 예상과 달라 의도한 작업을 수행하지 못하게 되어 서비스 이용에 문제가 생기는 에러
    - 개발자가 프로그램의 전반적인 코드와 알고리즘을 체크해야 한다
- 컴파일 에러(Compillation Error)
    - 컴파일 단계에서 발생하는 오류로 컴파일러가 에러 메세지를 출력해주는 에러
    - 대표적인 원인으로 문법 구문 오류(syntax error)이다
    - 컴파일 오류는 컴파일이 안된다는 의미이며 즉 프로그램이 만들어지지 않아 프로그램 자체가 실행되지 않기 때문에 심각하게 볼 오류는 아니다
        
        <aside>
        💡
        
        컴파일 에러는 소스 코드를 javac.exe로 컴파일 하는 과정에서 컴파일러가 전반적인 코드를 체크해서 에러 메세지를 보여주는 형태이지만, IDE에서는 일정 주기로 계속 자동으로 컴파일을 해주기 때문에 바로바로 문제를 알 수 있다
        
        </aside>
        
- 런타임 에러(Runtime Error)
    - 프로그램 실행 중에 발생하는 에러
    - 대체로 개발 시 논리적인 설계 미숙으로 발생하는 경우가 대부분이며 런타임 에러 발생 시 프로그래머가 역추적해서 원인 확인을 해야한다
    - 잠재적인 런타임 에러를 방지하기 위해 프로그램의 실행 도중 발생할 수 있는 경우의 수를 최대한 고려해야 한다

### 에러와 예외

자바 프로그램에서 실행 시 발생할 수 있는 오류를 에러(Error)와 예외(Exception)으로 구분하였다

- 에러(Error) : 프로그램 코드에 의해서 수습될 수 없는 심각한 오류(JVM 실행에 문제가 생김)
    - ex) OutOfMemoryError, StackOverflowError 등
- 예외(Exception) : 프로그램 코드에 의해서 수습될 수 있는 다소 미약한 오류
    - ex) IOException, ClassCastException 등

Error는 발생하면 복구 할 수 없는 심각한 오류여서 예측이 불가능하며 개발자가 대처할 방법이 없다 Exception은 발생하더라도 수습될 수 있는 비교적 덜 심각한 오류로 Exception이 계속 발생한다 해도 Error처럼 프로그램이 죽거나 그럴 경우는 적다

그렇다고 Exception을 제대로 처리하지 않으면 예상치 못한 오류 발생으로 프로그램에 문제가 생길 수 있기 때문에 예외 처리를 통해 언제나 예외 상황을 처리하여 프로그램이 종료되는 일이 없도록 해야한다

### 자바의 예외(Exception) 클래스

자바에서는 오류를 Error와 Exception으로 나누었고 클래스로 구현하여 처리하도록 하였다
JVM은 프로그램을 실행하는 도중 예외가 발생하면 해당 예외 클래스로 객체를 생성하고 예외 처리 코드에서 예외 객체를 이용할 수 있도록 해준다

예외 클래스의 계층 구조는 다음과 같다

![image](https://github.com/user-attachments/assets/8e899ff8-7c3f-4974-94f2-ad78f0047cd0)

<aside>
	
Throwable 클래스란? 
오류와 예외 모두 자바의 최상위 클래스인 Object를 상속받는다
Throwable 클래스의 역할은 오류나 예외에 대한 메시지를 담는 것이다 
대표적으로getMessage() 와printStackTrace() 메소드가 바로 이 클래스에 속해 있다
Throwable을 상속받은 Error와 Exception 클래스에서도 두 메소드를 사용할수 있게 된다

</aside>

<aside>
printStackTrace() : 예외발생 당시 호출스택에 있던 메소드의 정보와 예외 메시지 출력

getMessage() : 발생한 예외클래스의 인스턴스에 저장된 메시지를 가져옴

</aside>

### CheckedException/ UncheckedException

자바의 예외(Exception)은 컴파일 에러와 런타임 에러로 구분된다 
예외의 종류도 CheckedException/ UncheckedException으로 나뉜다

Checked Exception은 컴파일 예외클래스들을 가리키는 것이고, Unchecked Exception은 런타임 예외클래스들을 가리키는 것으로 보면 된다
Checked / Unchecked Exception으로 재분류 한 이유는 코드적 관점에서 예외 처리 동작을 필수 지정 유무에 따라 나뉘기 때문이다

|  | **Checked Exception** | **Unchecked Exception** |
| --- | --- | --- |
| 발생 원인 | 사용자의 실수와 같은 외적인 요인에 의해 발생 | 프로그래머의 실수로 발생 |
| 처리 여부 | 반드시 예외를 처리해야 함 | 명시적인 처리를 하지 않아도 됨 |
| 확인 시점 | 컴파일 단계 | 런타임 단계 |
| 예외 종류 | RuntimeException을 제외한 
Exception 클래스와 그를 상속받는 하위 예외

- IOException
- SQLException | RuntimeException 과 그 하위 예외

- NullPointerException
- IllegalArgumentException
- IndexOutOfBoundException |

### 커스텀 예외 만들기

자바 예외 클래스도 결국 클래스 이기 때문에 사용자가 직접 커스텀 예외 클래스를 만들어서 사용할 수 있다 예외로 사용할 클래스를 만들고 extends를 통해 사용할 예외를 상속받고 사용하면 된다

```java
// 사용자 커스텀 예외 클래스를 만들려면 부모 클래스인 Exception 클래스를 상속 하면 된다
// Exception 이외에도 특정 예외를 상속받아서 사용할 수도 있다
class MyErrException extends Exception {
    private String msg;
	
    // 사용자 커스텀 예외클래스 생성자
    public MyErrException(String msg) {
        super(msg); // 부모 Exception 클래스 생성자도 호출
        this.msg = msg;
    }
	
    // 사용자 커스텀 예외클래스 메세지 출력
    public void printMyMessage() {
        System.out.println(this.msg);
    }
}

public class Main {
    public static void main(String[] args) {
        try {
            throw new MyErrException("나의 커스텀 예외 클래스 메세지"); // 커스텀 예외 클래스 발생
        } catch (MyErrException e) {
            e.printMyMessage(); // 커스텀 예외 클래스의 메서드 실행
            e.printStackTrace(); // 상속한 부모클래스의 메서드 실행
        }
    }
}
```

출처:

[https://inpa.tistory.com/entry/JAVA-☕-에러Error-와-예외-클래스Exception-💯-총정리](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%97%90%EB%9F%ACError-%EC%99%80-%EC%98%88%EC%99%B8-%ED%81%B4%EB%9E%98%EC%8A%A4Exception-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC)
