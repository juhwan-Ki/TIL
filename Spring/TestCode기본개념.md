## TDD란?

- 테스트 주도 개발이라는 의미로 테스트를 먼저 설계 및 구축 후 테스트를 통과할 수 있는 코드를 짜는 것
- 애자일 개발 방식 중 하나로 코드 설계시 원하는 단계적 목표에 대해 설정하여 진행하고자 하는 것에 대한 결정 방향의 갭을 줄이고자 함
- 최초 목표에 맞춘 테스트를 구축하여 그에 맞게 코드를 설계하기 때문에 적은 의견 충돌을 기대할 수 있음

## 테스트 코드 작성 목적

1. 코드의 안전성을 높일 수 있음
2. 기능을 추가하거나 변경하는 과정에서 발생할 수 있는 Side-Effect를 줄일 수 있음
3. 해당 코드가 작성된 목적을 명확하게 표현할 수 있음
→ 코드에 불필요한 내용이 들어가는 것을 비교적 줄일 수 있음

## JUnit이란?

- JAVA 진영의 대표적인 Test Framework로 단위 테스트(Unit Test)를 위한 도구를 제공

<aside>
💡 단위 테스트란?
- 코드의 특정 모듈이 의도된 대로 동작하는지 테스트 하는 절차를 의미
- 모든 함수와 메소드에 대한 각각의 테스트 케이스를 작성하는 것

</aside>

- 어노테이션(Annotation)을 기반으로 테스트를 지원
- 단정문(Assert)으로 테스트 케이스의 기대값에 대해 수행 결과를 확인할 수 있음
- Junit5는 크게 Jupiter, Platform, Vintage 모듈로 구성됨

## JUnit 모듈

### 1. JUnit Jupiter

- TestEngine API 구현체로 JUnit5를 구현하고 있음
- 테스트의 실제 구현체는 별도 모듈 역할을 수행하는데, 그 모듈 중 하나가 Jupiter-Engine임
- Jupiter API를 사용하여 작성한 테스트 코드를 발견하고 실행하는 역할을 수행
개발자가 테스트 코드를 작성할 때 사용됨

### 2. JUnit Platform

- Test를 실행하기 위한 뼈대로 Test를 발견하고 테스트 계획을 생성하는 TestEngine 인터페이스를 가지고 있음 → 즉 Platform은 TestEngine 인터페이스이고 그 구현체가 Jupiter
- TestEngine을 통해 Test를 발견하고 수행 및 결과를 보고함
- 각종 IDE 연동을 보조하는 역할을 수행(콘솔 출력등)
Platform = TestEngine API + Console Launcher + JUnit 4 Based Runner 등)

### 3. JUnit Vintage

- TestEngine API 구현체로 JUnit 3, 4를 구현하고 있음
- 기존 JUnit 3, 4 버전으로 작성된 테스트 코드를 실행할 때 사용
- Vintage Engine 모듈을 포함
- Platform 위에 Vintage와 Jupiter가 동작
→ Platform은 인터페이스이기 때문에 Platform을 구현한 구현체인 Vintage와 Jupiter가 동작
    
    ![Untitled](https://github.com/juhwan-Ki/TIL/assets/87765888/35b78e94-3bb7-46a7-9469-aecd89f9a758)
    

## JUnit LifeCycle Annotation

1. @Test : 테스트용 메소드를 표현하는 어노테이션
2. @BeforEach : 각 테스트 메소드가 시작되기 전에 실행되어야 하는 메소드를 표현
3. @AfterEach : 각 테스트 메소드가 시작된 후 실행되어야 하는 메소드를 표현
4. @BeforAll : 테스트 시작 전에 실행되어야 하는 메소드를 표현(static 처리 필요)
5. @AfterAll : 테스트 종료 후에 실행되어야 하는 메소드를 표현(static 처리 필요)

## JUnit Main Annotation

### @SpringBootTest

- **통합 테스트 용도로 사용**되어 @SpringBootApplication을 찾아가 하위의 모든 Bean을 스캔하여 로드함
- 그 후 Test용 Application Context를 만들어 스캔하여 로드된 Bean을 추가하고, MockBean으로 생성되어 있는 테스트코드가 있으면 Bean을 MockBean으로 교체

### @ExtendWith

- JUnit4에서 @RunWith로 사용되던 어노테이션이 ExtendWith로 변경
- @ExtendWith는 메인으로 실행될 Class를 지정할 수 있음
- @SpringBootTest는 기본적으로 @ExtendWith가 추가되어 있음

### @WebMvcTest(Class명.class)

- ()에 작성된 클래스만 실제로 로드하여 테스트를 진행
- 매개변수를 지정해주지 않으면 @Controller, @RestController, @RestControllerAdvice등 컨트롤러와 연관된 Bean이 모두 로드됨
- 스프링의 모든 Bean을 로드하는 @SpringBootTest 대신 컨트롤러 관련 코드만 테스트 할 경우 사용

### @Autowired about Mockbean

- Controller의 API를 테스트하는 용도인 MockMvc 객체를 주입 받음
    - 객체를 주입 받을 때 @Autowired를 사용하여 주입 받음
- perform() 메소드를 활용하여 컨트롤러의 동작을 확인할 수 있음
    - perform() : HTTP 통신을 도와주는 역할
    - andExpect(), andDo(), andReturn() 등의 메소드를 같이 활용

### @MockBean

- 테스트할 클래스에서 주입 받고 있는 객체에 대해 가짜 객체를 생성해주는 어노테이션
    - 의존성이 물려있는 객체들에 대해 가짜 객체를 만들어준다고 생각하면됨
- given() 메소드를 활용하여 가짜 객체의 동작에 대해 정의하여 사용할 수 있음
    
    ```java
    @MockBean
    ProductServiceImpl productService;
    
    // http://localhost:8080/api/v1/product-api/product/{productId}
    @Test
    @DisplayName("Product 데이터 가져오기 테스트")
    void getProductTest() throws Exception {
    
      // given : Mock 객체가 특정 상황에서 해야하는 행위를 정의하는 메소드
      given(productService.getProduct("12315")).willReturn(
          new ProductDto("15871", "pen", 5000, 2000));
    
      String productId = "12315";
    
      // andExpect : 기대하는 값이 나왔는지 체크해볼 수 있는 메소드
      mockMvc.perform(
              get("/api/v1/product-api/product/" + productId))
          .andExpect(status().isOk())
          .andExpect(jsonPath("$.productId").exists()) // json path의 depth가 깊어지면 .을 추가하여 탐색할 수 있음 (ex : $.productId.productIdName)
          .andExpect(jsonPath("$.productName").exists())
          .andExpect(jsonPath("$.productPrice").exists())
          .andExpect(jsonPath("$.productStock").exists())
          .andDo(print());
    
      // verify : 해당 객체의 메소드가 실행되었는지 체크해줌
      verify(productService).getProduct("12315");
    }
    ```
    

## @AutoConfigureMockMvc

- spring.test.mockmvc의 설정을 로드하면서 MockMvc의 의존성을 자동으로 주입
    - 일반적으로 단위테스트 시 MockMvc를 사용할 때 같이 사용
- MockMvc 클래스는 REST API 테스트를 할 수 있는 클래스

### @Import

- 필요한 클래스들을 Configuration으로 만들어 사용할 수 있음
- Configuration Component 클래스도 의존성 설정할 수 있음
- Import된 클래스는 의존성 주입으로 사용할 수 있음

## 단위 테스트 원칙 F.I.R.S.T

- Fast : 테스트 코드의 실행은 빠르게 진행되어야 함 
→ 테스트 코드는 간결하게 작성하여 실행하는데 무리가 가서는 안됨
- Independent : 독립적인 테스트가 가능해야함 
→ 모든 테스트를 돌리는 것이 아닌 하나 테스트를 돌려도 테스트가 가능하도록 작성해야함
- Repeatable : 테스트는 매번 같은 결과를 만들어야함
- Self-Validating : 테스트는 그 자체로 실행하여 결과를 확인할 수 있어야 함
→ 테스트 메소드 하나만 가지고 테스트를 완료되어야 함(Independent와 비슷함)
- Timely : 단위 테스트는 비즈니스 코드가 완성되기 전에 구성하고 테스트가 가능해야함
→ 코드가 완성되기 전부터 테스트가 따라와야 한다는 TDD의 원칙을 담고 있음

- 출처: 어라운드 허브 스튜디오(테스트 코드 적용하기 (JUnit, TDD) [ 스프링 부트 (Spring Boot)])