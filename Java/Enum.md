### Enum(Enumeration)
- Enum은 Enumeration의 약자로 열거, 목록, 알림표 라는 뜻을 가지고 있으며 한글로는 열거형이라고 부른다
- 열거형은 요소, 멤버라고 불리는 값의 집합을 이루는 자료형이다(상수 데이터들의 집합)
- 상수로 표시가 가능한 데이터 묶음을 열거형 타입인 Enum으로 묶어주면 보다 구조적인 프로그래밍을 할 수 있다

### Enum이 없었을 때는 어떻게 상수를 정의했나?
1. final 키워드 사용
    - final 제어자를 할당함으로써을 사용하여 한번 지정하면 바뀌지 않게 설정되며, 동시에 static을 사용하여 메모리에 한 번만 할당 되게 설정된다 
    - 하지만 이 방법은 접근 제어자들 때문에 가독성이 그렇게 좋지 못하다라는 단점이 존재한다

    ``` java
    class Grade {
        // private와 같이 접근 제어자를 사용하기 때문에 가독성이 떨어짐
        private final static int BASIC = 1; 
        private final static int GOLD = 2;
        private final static int VIP = 3;
        
        public static void main(String[] args) {

            int grade = Grade.BASIC;

            switch (grade) {
                case Grade.BASIC:
                    System.out.println("당신의 회원 등급은 BASIC 입니다.");
                    break;
                case Grade.GOLD:
                    System.out.println("당신의 회원 등급은 GOLD 입니다.");
                    break;
                case Grade.VIP:
                    System.out.println("당신의 회원 등급은 VIP 입니다.");
                    break;
            }
        }
    }
    ```

2. 인터페이스 상수 사용
    - interface는 반드시 추상 메소드만 선언할 수 있는 것이 아니다
    - 인터페이스 내에서도 상수를 선언할 수 있는데, 인터페이스의 멤버는 public static final 속성을 생략할 수 있는 특징을 이용하여 코드를 조금 더 간결하게 작성할 수 있게 된다
    - 하지만 결국 정수나 문자열 같은 값을 저장하고 있기 때문에 어떠한 값이 들어와도 컴파일 시점에서는 에러를 찾기 어렵다는 단점이 있다
    - 프로그램의 크기가 커지면서 개발자의 실수가 잦아지며 이러한 제약적이지 않는 요소들 때문에 프로그램에 버그가 발생할 수 있다

    ``` java
    interface Grade {
        int BASIC = 1; 
        int GOLD = 2;
        int VIP = 3;
    }

    public static void main(String[] args) {

        int grade = 1;

        // 조건문에서 비교하는 값이 결국 정수 값 이기 때문에 에러가 안나고 그대로 수행됨 
        // -> 프로그램 버그 발생 가능성
        switch (grade) {
            case Grade.BASIC:
                System.out.println("당신의 회원 등급은 BASIC 입니다.");
                break;
            case Grade.GOLD:
                System.out.println("당신의 회원 등급은 GOLD 입니다.");
                break;
            case Grade.VIP:
                System.out.println("당신의 회원 등급은 VIP 입니다.");
                break;
        }
    }
    ```

3. 자체 클래스 상수 사용
    - 상수를 정수나 문자열로 구성하는 것이 아니라 독립된 고유의 객체로 선언하여 자체 클래스 인스턴스화를 사용해 상수처럼 사용하는 기법이 사용 되었다
    - 자기 자신 객체를 인스턴스화 하고 final static화 함으로써 고유의 객체를 상수처럼 사용하는 방식이다
    - 하지만 가독성이 떨어지며, if문에는 문제가 없지만 switch문에서는 사용할 수 없다는 단점이 있다
        - swith문에 들어가는 데이터 타입은 제한적이기 때문(객체 비교는 불가능)
    
    ``` java
    class Grade {
        public final static int BASIC = new Grade(); 
        public final static int GOLD = new Grade();
        public final static int VIP = new Grade();
    }

    public static void main(String[] args) {

        Grade grade = Grade.BASIC;

        // if문은 가능
        if(grade == Grade.BASIC) {
            System.out.println("당신의 회원 등급은 BASIC 입니다.");
        }

        // switch문은 에러 발생 -> 객체가 들어올 수 없음
        switch (grade) {
            case Grade.BASIC:
                System.out.println("당신의 회원 등급은 BASIC 입니다.");
                break;
            case Grade.GOLD:
                System.out.println("당신의 회원 등급은 GOLD 입니다.");
                break;
            case Grade.VIP:
                System.out.println("당신의 회원 등급은 VIP 입니다.");
                break;
        }
    }
    ```

### Enum 사용
- 이러한 문제점을 해결하고자 자바에서 상수만을 다루는 enum 타입 클래스를 만들게 되었다
- enum의 핵심은 이러한 상수를 단순히 정수로 치부하지말고 객체 지향적으로 객체화해서 관리하자는 취지이다
- JAVA의 enum은 인터페이스와 같이 독립된 특수한 클래스로 구분한다 즉, 일종의 객체이기 때문에 힙(heap) 메모리에 저장되며 각 enum 상수들은 별개의 메모리 주소값을 가짐으로써 완벽히 독립된 상수를 구성할 수 있는 것이다
- 또한 IDE에서 클래스 파일을 생성할때 독립된 열거형 파일도 생성할 수 있다 (독립된 클래스이기 때문에)

### Enum 장점
1. 코드가 단순해지며 가독성이 좋아진다
2. 허용 가능한 값들을 제한하여 유형 안전(type safe)을 제공한다
3. 키워드 enum을 사용하기 때문에 구현의 의도가 열거임을 분명하게 나타낼 수 있다
4. 자체 클래스 상수와 달리 switch문에서도 사용할 수 있다
5. 단순 상수와 비교해 IDE의 적극적인 지원을 받을 수 있다 (자동완성, 오타검증, 텍스트 리팩토링 등등)
6. 리팩토링시 변경 범위가 최소화 된다 (enum에서 한번에 관리하기 때문에 내용의 추가가 필요하더라도, Enum 코드외에 수정할 필요가 없다)
7. enum은 본질적으로 Thread safe인 싱글톤 객체 이므로 싱글톤 클래스를 생성하는데에도 사용된다

### Enum 문법
1. 선언
    - enum 명은 클래스와 같이 첫 문자를 대문자로하고 나머지는 소문자로 구성한다
    - 관례적으로, 열거 상수는 모두 대문자로 작성한다
    - 열거 상수가 여러 단어로 구성될 경우, 단어 사이를 밑줄 (_)로 연결한다

    ``` java
    enum Grade {
        BASIC, GOLD, VIP
    }

    enum LoginResult {
	    LOGIN_SUCCESS, LOGIN_FAILED
    }
    ```

2. 참조 
    - enum도 객체 타입이기 때문에 변수를 선언하고 사용할 수 있다
    - enum은 primitive 타입이 아닌 reference 타입으로 분류되며 상수의 값은 heap 영역에 저장된다
    - String 처럼 스택 영역에 있는 변수들이 힙 영역에 있는 데이터의 주소값을 저장함으로써 참조 형태를 띄게 된다 그래서 다음과 같이 같은 enum 타입 변수 끼리 같은 상수 데이터를 바라봄으로써 둘이 주소를 비교하는 == 연산 결과는 true가 되게 된다

    ``` java
    Grade grade = null; // 참조 타입이기 때문에 null도 저장 가능
    grade = Grade.VIP;

    // 주소값 비교
    System.out.println(grade == Grade.VIP); // true
    ```

3. 메소드 
    - name() : 열거 객체가 가지고 있는 문자열을 리턴
    - ordinal() : 열거 타입을 정의 할 때 주어진 순번을 리턴
    - compareTo() : 매개값으로 주어진 열거 객체를 비교해서 순번 차이를 리턴
    - valueOf() : 매개값으로 주어지는 문자열과 동일한 문자열을 가지는 열거 객체를 리턴(없는 경우 에러 발생)
    - values() : 열거 타입의 모든 열거 객체들을 배열로 만들어 리턴

### Enum 고급 문법
1. 매핑
    - enum에서 정의한 상수와 고유의 값을 매핑하고 싶을 때는 필드를 생성하여 enum 생성자를 통해 값을 매핑할 수 있다
    - 또한 getter 메소드를 통해 매핑된 값을 가져올 수 있다

    ``` java
    // enum 매핑 클래스
    enum Season {
        SPRING("봄"),
        SUMMER("여름"),
        FALL("가을"),
        WINTER("겨울");
        
        // 문자열을 저장할 필드
        private String season;

        // 생성자 (싱글톤)
        private Season(String season) {
            this.season = season;
        }

        // Getter
        public String getSeason() {
            return season;
        }
    }

    public static void main(String[] args) throws Exception {
        Season s = Season.SUMMER;
        System.out.println(s.name()); // 열거 객체명 출력 : SUMMER
        System.out.println(s.getSeason()); // 매핑된 열거 데이터 출력 : 봄
    }
    ```

2. 내부 구성 이해
    - enum은 상수 하나당 자신의 인스턴스를 만들어서 public static final 필드로 공개한다
    - 이처럼 각 enum 객체마다 자신의 클래스를 인스턴스화 하여 저장하니 enum을 매핑한다거나 추상메소드 확장한다거나 등 응용이 가능한 것이다
    - 위의 코드를 보면 생성자 자체가 private 접근제어자 이기 때문에, 밖에서 접근할 수 있는 생성자를 제공하지 않으므로 사실상 final이 되게 된다
    - 그래서 클라이언트가 인스턴스를 직접 생성하거나 확장할 수 없으니 열거 타입 선언으로 만들어진 인스턴스들은 딱 하나씩만 존재함이 보장된다

    ``` java
    /* 타입 안전 열거 패턴(typesafe enum pattern) */
    // 위 Enum을 클래스로 표현하면 이런식(실제로 enum이 나오기 전에는 이렇게 사용했음)
    final class Season {
        public static final Season SPRING = new Season("봄"); 
        public static final Season SUMMER = new Season("여름");
        public static final Season AUTUMN = new Season("가을");
        public static final Season WINTER = new Season("겨울");
        
        private String season;
        
        private Season(String season) {
            this.season = season;
        }
        
        public String getSeason() {
            return season;
        }
    }
    ```

### Enum 확장
- Enum은 단순 상수의 집합을 넘어서 상수 메소드로서도 이용이 가능하다
-  추상 메소드를 정의해서 각 상수마다 익명 클래스 처럼 메소드 재정의를 하게 해서 각 상수마다 다른 역할을 하는 메소드를 갖게 되는 원리이다

``` java
enum Operation {
    PLUS("+") {
        public double apply(double x, double y) {
            return x + y;
        }
    },
    MINUS("-") {
        public double apply(double x, double y) {
            return x - y;
        }
    },
    MULTI("*") {
        public double apply(double x, double y) {
            return x * y;
        }
    },
    DIVIDE("/") {
        public double apply(double x, double y) {
            return x / y;
        }
    };

    // 클래스 생성자와 멤버
    private final String symbol;
    Operation(String symbol) {
        this.symbol = symbol;
    }

    // toString을 재정의하여 열거 객체의 매핑된 문자열을 반환하도록
    @Override
    public String toString() {
        return symbol;
    }

    // 열거 객체의 메소드에 사용될 추상 메소드 정의
    public abstract double apply(double x, double y);
}
```

### enum은 상태와 행위를 한곳에서 관리가 가능
- 이처럼 enum을 단순히 상수 표현식을 넘어서 동작, 행위를 수행하는 상수로서도 응용 확장이 가능하다 즉 상수 상태와 행위를 한 곳에서 관리가 가능한 것이다
- 예를들어 각 상수를 특정 데이터와 연결짓거나, 상수마다 다르게 동작하게 하는 로직이 필요할때, 보통이라면 단순히 if문, switch문으로 상수를 분기하여 하여 각 메소드들을 실행하는 것이 일반적인 로직이기 마련이다 그러나 이러한 코드 구성은 값(상태)과 메소드(행위)가 어떤 관계가 있는지에 대해 코드 정의문을 찾아 기웃거리며 파악해야 하는 시간이 걸린다
- 하지만 enum을 이용하면 상태와 행위를 한눈에 파악할 수 있어 유지보수가 용이하도록 만들 수 있다
- 또한 람다식을 사용하여 깔끔하게 처리할 수도 있다
