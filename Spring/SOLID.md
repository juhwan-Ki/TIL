## SOLID란??

- SPR(Single Responsibility Principle) : 단일 책임 원칙
- OCP(Open/Closed Principle) : 개방/폐쇄 원칙
- LSP(Liskov Substitution Principle) : 리스코프 치환 원칙
- ISP(Interface Segregation Principle) : 인터페이스 분리 원칙
- DIP(Dependency Inversion Principle) : 의존관계 역전 원칙

### SRP - 단일 책임 원칙

- 하나의 클래스는 하나의 책임만 가져야 한다.
- 하나의 책임이라는 것은 모호하다.
- 중요한 기준은 변경이다. 변경이 있을 때 파급 효과가 적으면 단일 책임 원칙을 잘 따른것

### OCP - 개방 / 폐쇄 원칙

- 소프트웨어 요소는 확장에는 열려 있으나 변경에는 닫혀 있어야 한다.
- 다형성을 활용하여 인터페이스를 구현한 새로운 클래스를 새로 생성하여 새로운 기능을 구현
    - 새로운 구현체를 만드는 것은 기존 코드를 수정하는 것이 아닌 새로운 기능을 만드는 것이기 때문에
    변경에 닫혀 있다고 할 수 있음
- 문제점
    
    ```java
    // MemberRepository를 상속받는 MemoryMemberRepository와 
    // JdbcMemberRepository가 있다고 가정
    public class MemberService {
    	//private MemberRepository memberRepository = new MemberRepository(); // 가능
    	private MemberRepository memberRepository = new JdbcMemberRepository(); // 가능
    } 
    ```
    
    - 위 코드처럼 MemberService가 구현 클래스를 직접 선택하고 있음
    - 구현 객체를 변경하려면 클라이언트(현재는 MemberService)코드를 변경해야함!!
    - 다형성을 사용했지만 OCP 원칙을 지킬 수 없다.
    - 해결하려면 객체를 생성하고 연관관계를 맺어주는 별도의 조립, 설정자가 필요
    (DI와 IOC container가 필요)

### LSP - 리스코프 치환 원칙

- 프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 한다
- 다형성에서 하위 클래스는 인터페이스 규약을 다 지켜야 한다는 것
- 즉 인터페이스에서 정의한 기능 대로 구현하도록 하는 것
- 단순히 컴파일에 성공하는 것을 넘어서서 정의된 기능에 대해 다르게 구현하지 않아야 한다.
- 예) 자동차 인터페이스에서 엑셀은 앞으로 가는 기능
→ 뒤로가게 구현하면 LSP를 위반
→ 느리더라도 앞으로 가게 하는 것이 LSP를 지키는 것

### ISP - 인터페이스 분리 원칙

- 인터페이스 하나에 모든 기능을 넣는 것이 아닌 특정 기능에 따라 인터페이스를 분리해야 한다.
- 자동차 인터페이스 → 운전 인터페이스, 정비 인터페이스로 분리
- 사용자 클라이언트 → 운전자 클라이언트, 정비사 클라이언트로 분리
- 분리하게 되면 정비 인터페이스가 변해도 운전자 클라이언트에 영향을 주지 않음
- 인터페이스가 명확해지고 대체 가능성이 높아짐

### DIP - 의존관계 역전 원칙

- 구체화에 의존하는것이 아닌 추상화에 의존하는 원칙으로 의존성 주입은 이 원칙을 따르는 방법중 하나
- 쉽게 이야기해서 구현 클래스에 의존하지 말고, 인터페이스에 의존 하라는 뜻
- 역할(인터페이스)에 의존하게 해야 한다는 것과 같다.
- 예시
    
    ```java
    // MemberRepository를 상속받는 MemoryMemberRepository와 
    // JdbcMemberRepository가 있다고 가정
    public class MemberService {
    	//private MemberRepository memberRepository = new MemberRepository(); // 가능
    	private MemberRepository memberRepository = new JdbcMemberRepository(); // 가능
    } 
    ```
    
- 위 코드에서 MemberService는 인터페이스에 의존하지만 구현 클래스도 동시에 의존하고 있음
- 즉 MemberService 클라이언트가 구현 클래스를 직접 선택 하고 있다는 뜻
    - private MemberRepository memberRepository = new MemberRepository();
- DIP 위반

## 정리

- 객체 지향의 핵심은 다형성
- 다형성 만으로는 구현 객체를 변경할 때 클라이언트 코드도 함께 변경된다.
- 다형성 만으로는 OCP, DIP를 지킬 수 없다.