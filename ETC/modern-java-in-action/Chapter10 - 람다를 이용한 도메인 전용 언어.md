### 도메인 전용 언어

DSL은 범용이아니라 특정 도메인을 대상으로 만들어진 특수 프로그래밍 언어로 특정 비즈니스 도메인의 문제를 해결하기위해 만든 언어다 자바에서는 도메인을 표현할 수 있는 클래스와 메서드 집합이 필요하다 즉 DSL이란 특정 비즈니스 도메인을 인터페이스로 만든 API라고 생각할 수 있다

<aside>
💡 SQL → DB를 조회할 때만 사용 SQL로 웹 프로그래밍은 불가능! 이런게 DSL

</aside>

DSL은 범용 프로그래밍 언어가 아니기 때문에 특정 도메인에 국한되어 진다 DSL을 이용하면 사용자가 특정 도메인의 복잡성을 더 잘 다룰 수 있다 사용자 친화적인 DSL을 개발하기 위해 생각해야할 것들은 다음과 같다

1. 의사 소통 : 코드의 의도가 명확히 전달되어야 하며 프로그래머가 아닌 사람도 이해할 수 있어야 한다 이런 방식으로 코드가 비즈니스 요구사항에 부합하는지 확인 할 수 있다
2. 가독성 : 가독성은 유지보수의 핵심이기에 항상 다른 사람이 읽기 쉬운 코드를 구현해야한다

### DSL의 장단점

- DSL의 장점
    1. 간결함 : API는 비즈니스 로직을 간편하게 캡슐화하므로 반복을 피할 수 있고 코드를 간결하게 할 수 있다
    2. 가독성 : 도메인 영역의 용어를 사용하므로 비 도메인 전문가도 코드를 쉽게 이해할 수 있다
    3. 유지보수 : 잘 설계된 DSL로 구현한 코드는 쉽게 유지보수하고 바꿀 수 있다
    4. 높은 수준의 추상화 : DSL은 도메인과 같은 추상화 수준에서 동작하므로 도메인의 문제와 직접적으로 관련되지 않은 세부 사항을 숨긴다
    5. 집중 : 비즈니스 도메인의 규칙을 표현할 목적으로 설계된 언어이므로 프로그래머가 특정 코드에 집중할 수 있다
    6. 관심사 분리 : 지정된 언어로 비즈니스 로직을 표현함으로 애플리케이션의 인프라구조와 관관련된 문제와 독립적으로 비즈니스 관련된 코드에 집중하기가 용이해 결과적으로 유지보수가 쉬운 코드를 구현한다
- DSL의 단점
    1. DSL 설계의 어려움 : 간결하게 제한적인 언어에 도메인 지식을 담기가 어렵다
    2. 개발 비용 : 코드에 DSL을 추가하는 작업은 초기 프로젝트에 많은 시간과 비용이 소모된다
    3. 추가 우회 계층 :  DSL은 추가적인 계층으로 도메인 모델을 감싸며 이 때 계층을 최대한 작게 만들어 성능 문제를 회피한다
    4. 새로 배워야 하는 언어 : 요즘에는 한 프로젝트에도 여러가지 언어를 사용하는 추세이므로 새로 배워야하는 언어가 추가될 수 있다
    5. **호스팅 언어 한계 :**일부 자바 같은 범용 프로그래밍 언어는 장황하고 엄격한 문법을 가졌다 이런 언어로는 사용자 친화적 DSL을 만들기 힘들다 사실 장황한 프로그래밍 언어를 기반으로 만든 DSL은 성가진 문법의 제약을 받고 읽기가 어려워진다 Java8의 람다 표현식은 이 문제를 해결할 강력한 새 도구다

### JVM에서 이용할 수 있는 다른 DSL 해결책

- **내부 DSL**
    
    우린 자바를 배우고 있다 그래서 내부 DSL은 자바를 이용하여 만든 DSL을 의미한다 그럼 자바로 만든 DSL은 어떤 것을 의미하는 것일까? 
    
    ```java
    List<Integer> list = Arrays.asList(1,2,3,4,5);
    **list.forEach**(new Consumer<Integer>() {
        @Override
        public void **accept**(Integer integer) {
            **System.out.println(integer);**
        }
    });
    
    /*
    	위의 코드에서 중요한 것은 밑줄이다. 밑줄을 제외한 나머지 코드는 의미상 필요하지 않다. 그럼 의미만 남기는
    	코드로 고쳐보자
    	위의 코드를 말로 해석해보자
    	1. list라는 배열을 사용한다.
    	2. list의 요소를 forEach를 이용하여 순차적으로 가져온다.
    	3. forEach에서는 consumer을 사용하기 위해 accept를 재정의한다.
    	4. accept는 Integer 타입의 integer을 받아 system.out.println을 이용하여 integer(변수명)을 출력한다.
    	필요한 요소만 밑줄이 존재한다. 나머지는 코드의 잡음이 되는 것이다.
    */
    
    list.forEach(System.out :: println);
    /*
    	이젠 밑줄 친 부분을 제외한 나머지 코드의 잡음은 존재하지 않는다. 의미적으로도 잘 전달 될 수 있다.
    	위의 코드를 다시 말로 해석해보자
    	1. list를 배열을 사용한다.
    	2. forEach를 이용해 배열 요소를 순차적으로 가져온다.
    	3. System.out.println을 이용하여 요소를 출력한다.
    	잡음이 상당히 줄었다.
    */
    ```
    
    순수 자바를 이용하여 DSL을 구현함으로 얻는 장점은 다음과 같다.
    
    - 내부로 작성되기 때문에 노력이 줄어든다. 즉, 새로운 패턴과 기술을 배워 DSL을 구현하는 노력이 줄어든다. (당연히 내부의 자바 코드로만 구현하기 때문에)
    - 다른 언어의 컴파일러를 이용하거나 외부 DSL을 만드는 도구를 사용할 필요가 없어 비용이 줄어든다.
    - 외부 도구를 따로 배울 필요가 없다.
    - IDE의 많은 도움을 받을 수 있다. (자바라는 언어가 역사와 플랫폼? 이 깊고 크다. 그래서 여러 도구가 지원이 잘된다.)
    - 이식성을 높일 수 있다.(자바로 개발한 도메인에서 다른 DSL이 필요하다면 당연히 자바로 개발하는 것이 더욱 이식성을 높인다.)
- **외부 DSL**
    
    프로젝트에 DSL을 추가하는 세 번째 옵션은 외부 DSL이다 이는 새롭게 처음부터 1부터 100까지 전부 설계해야 하기 때문에 결과적으로 어렵다 하지만 큰 장점은 외부 DSL은 무한한 유연성을 제공한다 이 유연성이 제공되는 원리는  제대로 설계된 외부 DSL은 최고의 가독성, 자바로 개발된 인프라구조 와 비즈니스 문제를 정의한 DSL 코드를 명확히 분리 할 수 있기 때문이다 단점 또한 존재한다 그만큼 인공적인 계층이 발생함으로 양날의 검과 같다
    
- **다중 DSL**
    
    자바는 바이트코드를 사용하여 JVM이 코드를 해석한다 그래서 같은 바이트 코드로 해석되는 언어라면 DSL의 합침문제를 해결할 수 있었다 이것이 다른 언어라도 같은 JVM위에서 동작을 가능하게 하는 원리이고 다중 DSL의 시작이다 
    

**결론**

- DSL을 사용함으로 얻는 가독성, 유연성, 명확성이 큰 장점으로 작용한다
- DSL은 외부, 내부, 다중으로 분류 할 수 있다
- DSL 도메인전문가와 소프트웨어 개발자의 간극을 좁혀 준다
- "프로그래머가 아닌 사람도 이해할 수 있어야하는가?", "유지보수가 지속적으로 필요한가?", "비즈니스 코드에 집중해야하는 상황인가?" 이것이 필요한 상황이라면 DSL을 적용하는 방법도 생각해볼 수 있다
- DSL의 핵심은 가독성과 분리이다

### 최신 자바 API의 작은 DSL

자바의 새로운 기능의 장점을 적용한 첫 API는 네이티브 자바 API 자신이다 자바 8 이전의 네이티브 자바 API는 이미 한개의 추상 메서드를 가진 인터페이스를 갖고 있었다 하지만 무명 내부 클래스를 구현하려면 재정의, 생성자 선언 등 불필요한 잡음이 많이 발생했다 하지만 람다의 추가와 메서드 참조가 등장하며 최신 자바에선 재사용성과 메서드 결합도를 높였다

- **자바와 람다, 메서드 참조의 장점**
    
    ```java
    List<Integer> list =
                    IntStream.rangeClosed(1,10)
    		                .boxed()
    			              .collect(Collectors.toList());
    
    Collections.sort(list, new Comparator<Integer>() { // 내부 구현
        @Override
        public int compare(Integer o1, Integer o2) { // 코드 잡음 발생
            return o2 - o1;
        }
    });
    
    ...
    Collections.sort(list,(o1, o2) -> o2 -o1); // 코드잡음 제거 방법
    ...
    Collections.sort(list,Comparator.comparing(Integer::intValue).reversed()); // 코드 잡음 제거 방법
    /* 
    	최종 잡음을 제거했을때는 읽기 편하고 의미해석이 잘된다. 이것이 자바에 적용된 DSL 관점이다. 이를 통하여 우리는
    	자바에 작은 부분이지만 최소 DSL을 경험 할 수 있었다. 하지만 이예제에서 볼 수 있는 것은 만약 Integer가 아닌
    	실제 도메인 객체? 예를 들어 회원 정보를 정렬해야하는 경우? 라도 쉽게 가독성 좋게 활용 할 수 있다는 것이다.
    */
    
    collect.sort(Comparator.comparing(Customer::getName) // 이름으로 정렬
    												.reversed() // 이름 역순 정렬
    												.thenComparing(Customer::getAge)); // 다음 정렬은 나이 순서
    System.out.println(collect);
    ```
    
- **스트림 API는 컬렉션을 조작하는 DSL (조작)**
    
    스트림 인터페이스는 네이티브 자바 API에 작은 내부 DSL을 적용한 좋은 예시 중 하나이다. 스트림의  컬렉션의 항목을 필터, 정렬, 변환, 그룹화, 조작하는 작지만 강력한 DSL이다.
    
    ```java
    /*
    	로그 파일을 읽어 ERROR 라는 단어로 시작하는 파일의 40 행을 수집하는 작업을 수행하는 방법
    */
    try {
        List<String> collect = Files.lines(Paths.get("예시"))
                .filter(line -> line.startsWith("ERROR"))
                .limit(40)
                .collect(Collectors.toList());
    } catch (IOException e) {
        e.printStackTrace();
    }
    
    ...만약 스트림을 이용하는 것이 아니라면?
    
    List<String> errors = new ArrayList<>();
    try {
        BufferedReader bufferedReader = new BufferedReader(new FileReader("예시"));
        String line = bufferedReader.readLine();
        int errorCount = 0;
        while (errorCount < 40 && line != null){
            if (line.startsWith("ERROR")) errors.add(line);
            errorCount++;
            line = bufferedReader.readLine();
        }
    } catch (FileNotFoundException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    }
    /*
    	차이가 느껴진다. 확실히 DSL을 이용했을때 명시적이고 정확한 정보가 눈에 보인다. 그리고 잡음을 줄일 수 있다.
    */
    ```
    
    **결론**
    
    - 스트림 API는 컬렉션을 조작하는 강력한 DSL이다.
    - 조작하는데 있어서 가독성만이 아니라 명시적이고 흐름을 조금 더 사람의 언어처럼 이해할 수 있는 것 같다.
- **데이터를 수집하는 DSL인 Collectors (수집)**
    
    stream인터페이스를 데이터 리스트를 조작하는 DSL로 간주할 수 있음을 확인했다. 마찬가지로 Collector 인터페이스는 데이터 수집을 수행하는 DSL로 간주 할 수 있다.
    
    ```java
    Map<Integer, List<Integer>> collect = 
    							IntStream.rangeClosed(1, 10)
    							        .boxed()
    							        .collect(groupingBy(
    									                integer -> integer % 2));
    System.out.println(collect);
    ```
    
    - 아직은 결론을 내리기 힘들어 보류
- **자바로 DSL을 만드는 패턴과 기법**
    
    DSL이 좋은 것은 알았다. 따라서 우리도 DSL을 한번 만들어 볼 것이다.
    
    - 📍 **도메인 모델 정의**
        - **Stock**
            
            ```java
            @Getter
            @Setter
            public class Stock {
                private String symbol;
                private String market;
            }
            ```
            
        - **Trade**
            
            ```java
            @Getter
            @Setter
            public class Trade {
                public enum Type{ BUY,SELL}
                private Type type;
            
                private Stock stock;
                private int quantity;
                private double price;
            
                public double getValue(){
                    return quantity * price;
                }
            }
            ```
            
        - **Order**
            
            ```java
            public class Order {
                @Getter @Setter private String customer;
                private List<Trade> trades = new ArrayList<>();
            
                public void addTrade(Trade trade){
                    trades.add(trade);
                }
                
                public double getValue(){
                    return trades.stream().mapToDouble(Trade::getValue).sum();
                }
            
            }
            ```
            
    - 📍 **(번외)  도메인 객체의 API를 이용해 주식거래 주문을 직접 만드는 방법 (DSL** ❌**)**
        
        이걸 통하여 얻는 것은 DSL이 비개발자와 개발자 사이의 간극을 얼마나 좁혀주는지 이다.
        
        ```java
        public static void main(String[] args) {
                Order order = new Order();
                order.setCustomer("BigBank");
        
                Trade trade1 = new Trade();
                trade1.setType(Trade.Type.BUY);
        
                Stock stock1 = new Stock();
                stock1.setSymbol("IBM");
                stock1.setMarket("NYSE");
        
                trade1.setStock(stock1);
                trade1.setPrice(125.00);
                trade1.setQuantity(80);
                order.addTrade(trade1);
        
                Trade trade2 = new Trade();
                trade2.setType(Trade.Type.SELL);
        
                Stock stock2 = new Stock();
                stock2.setSymbol("GOOGLE");
                stock2.setMarket("NASDAQ");
        
                trade2.setStock(stock2);
                trade2.setPrice(375.00);
                trade2.setQuantity(50);
        	      order.addTrade(trade2);
        }
        ```
        
        🤮 **토할 것 같다.** 너무 길다. 그리고 비개발자인 도메인 전문가가 위의 코드를 이해하고 검증하기를 기대할 수 없다. 그렇기에 우리에겐 가독성 좋고 직관적으로 도메인 모델을 적용할 수 있어야하는 것이다. (솔직히 비개발자가 아니라 개발자가 봐도 한번에 딱딱 읽기엔 상당히 **귀찮은 양**이다.)
        
    - 📍 **도메인 모델을 활용한 DSL (메서드 체인 방식)**
        
        <aside>
        ❓ 주식거래를 똑같이 메서드 체인 방식으로 구현해볼 것이다. 사실 필자도 주식에는 무지하다. 그래서 뭔지 잘몰라서 사고 팔고 하는 걸 구현한다고 생각해보면 편할 것이다. (사실 스톡, 트레이드 뭐 이런 용어가 익숙하지 않다.)
        
        </aside>
        
        **최종 목표**
        
        ```java
        Order order = forCustomer("BigBank")
        					.buy(80)
        					.stock("IBM")
        					.on("NYSE")
        					.at(125.00)
        					.sell(50)
        					.stock("GOOGLE")
        					.on("NASDAQ")
        					.at(375.00)
        					.end();
        /*
        	정말정말 보기 편하다. 솔직히 비개발자가 봐도 알아볼 수 있을 정도로 직관적이다.
        */
        ```
        
        - 🔎 **구현**
            - **MethodChainingOrderBuilder**
                
                ```java
                public class MethodChainingOrderBuilder {
                
                    public final Order order = new Order();
                
                    private MethodChainingOrderBuilder(String customer) {
                        order.setCustomer(customer);
                    }
                
                    public static MethodChainingOrderBuilder forCustomer(String customer){
                        return new MethodChainingOrderBuilder(customer);
                    }
                
                    public TradeBuilder buy(int quantity){
                        return new TradeBuilder(this, Trade.Type.BUY,quantity);
                    }
                    public TradeBuilder sell(int quantity){
                        return new TradeBuilder(this, Trade.Type.SELL,quantity);
                    }
                    public MethodChainingOrderBuilder addTrade(Trade trade){
                        order.addTrade(trade);
                        return this;
                    }
                    public Order order(){
                        return order;
                    }
                    
                }
                ```
                
            - **TradeBuilder**
                
                ```java
                public class TradeBuilder {
                    private final MethodChainingOrderBuilder methodChainingOrderBuilder;
                    private final Trade trade = new Trade();
                
                    public TradeBuilder(MethodChainingOrderBuilder methodChainingOrderBuilder, Trade.Type type, int quantity) {
                        this.methodChainingOrderBuilder = methodChainingOrderBuilder;
                        trade.setType(type);
                        trade.setQuantity(quantity);
                    }
                
                    public StockBuilder stock(String symbol){
                        return new StockBuilder(methodChainingOrderBuilder, trade, symbol);
                    }
                }
                ```
                
            - **StockBuilder**
                
                ```java
                public class StockBuilder {
                    private final MethodChainingOrderBuilder methodChainingOrderBuilder;
                    private final Trade trade;
                    private final Stock stock = new Stock();
                
                    public StockBuilder(MethodChainingOrderBuilder methodChainingOrderBuilder, Trade trade, String symbol) {
                        this.methodChainingOrderBuilder = methodChainingOrderBuilder;
                        this.trade = trade;
                        stock.setSymbol(symbol);
                    }
                
                    public TradeBuilderWithStock on(String market){
                        stock.setMarket(market);
                        trade.setStock(stock);
                        return new TradeBuilderWithStock(methodChainingOrderBuilder,trade);
                    }
                }
                ```
                
            - **TradeBuilderWithStock**
                
                ```java
                public class TradeBuilderWithStock {
                    private final MethodChainingOrderBuilder methodChainingOrderBuilder;
                    private final Trade trade;
                
                    public TradeBuilderWithStock(MethodChainingOrderBuilder methodChainingOrderBuilder, Trade trade) {
                        this.methodChainingOrderBuilder = methodChainingOrderBuilder;
                        this.trade = trade;
                    }
                
                    public MethodChainingOrderBuilder at(double price){
                        trade.setPrice(price);
                        return methodChainingOrderBuilder.addTrade(trade);
                    }
                }
                ```
                
        
        <aside>
        ⁉️ 여기서 생기는 의문 빌더와 도메인을 나눠서 생각할게 아니라 도메인을 빌더로 만들면 되는거 아닌가? 아니면 도메인 안에서 빌더를 만들 수 있지 않을까?
        
        </aside>
        
        **결론**
        
        - 예제를 플루언트 방식으로 추가할 수 있다.
        - 메서드 체인 방식은 미리 지정된 절차에 따라 플루언트 API의 메서드를 호출하도록 강제한다.
        - 정적 메서드 사용을 최소화하고 메서드 이름이 인수의 이름을 대신(buy(), sell() 과 같은)하도록 하면서 DSL의 가독성을 개선하는 효과를 더한다.
    - 📍 **도메인 모델을 활용한 DSL (중첩된 함수 이용 방식)**
        
        같은 예제를 가지고 해볼 것이다.
        
        **최종 목표**
        
        ```java
        Order orderMadeByNestedFunction = 
        								order(
                            "BigBank",
                            buy(80, stock("IBM", on("NYSE")), at(125.00)),
                            sell(50, stock("GOOGLE", on("NASDAQ")), at(375.00))
                        );
        ```
        
        - 🔍 **구현**
            
            ```java
            public class NestedFunctionOrderBuilder {
            
                public static Order order(String customer, Trade... trades){
                    Order order = new Order();
                    order.setCustomer(customer);
                    Stream.of(trades).forEach(order::addTrade);
                    return order;
                }
            
                public static Trade buy(int quantity, Stock stock, double price){
                    return buildTrade(quantity,stock,price, Trade.Type.BUY);
                }
            
                public static Trade sell(int quantity, Stock stock, double price){
                    return buildTrade(quantity,stock,price, Trade.Type.SELL);
                }
            
                public static Trade buildTrade(int quantity, Stock stock, double price, Trade.Type type){
                    Trade trade = new Trade();
                    trade.setQuantity(quantity);
                    trade.setStock(stock);
                    trade.setPrice(price);
                    trade.setType(type);
                    return trade;
                }
                public static double at(double price){
                    return price;
                }
            
                public static Stock stock(String Symbol, String market){
                    Stock stock = new Stock();
                    stock.setSymbol(Symbol);
                    stock.setMarket(market);
                    return stock;
                }
            
                public static String on(String market){
                    return market;
                }
            
            }
            ```
            
        
        **결론**
        
        - 메서드 체인에 비해 함수의 중첩 방식이 도메인 객체의 계층 구조를 그대로 반영한다.
        - 괄호를 많이 사용한다는 단점이 존재한다
    
    <aside>
    ⁉️ 여기서 중간 점검 **메서드 체인, 중첩 함수**는 람다를 사용하지 않는 방식이다.
    
    </aside>
    
    - 📍 **도메인 모델을 활용한 DSL (람다 표현식을 이용한 함수 시퀀싱)**
        
        이제 부터는 람다를 이용하여 DSL을 표현하는 방법을 알아본다.
        
        **최종 목표**
        
        ```java
        Order orderMadeByFunctional =
                        LambdaOrderBuilder.order(o -> {
                            o.forCustomer("BigBank");
                            o.buy(t -> {
                                t.quantity(80);
                                t.price(125.00);
                                t.stock(s -> {
                                    s.symbol("IBM");
                                    s.market("NYSE");
                                });
                            });
                            o.sell(t -> {
                                t.quantity(50);
                                t.price(375.00);
                                t.stock(s -> {
                                    s.symbol("GOOGLE");
                                    s.market("NASDAQ");
                                });
                            });
                        });
        ```
        
        - 🔍 **구현**
            
            ```java
            public class LambdaOrderBuilder {
            
                private Order order = new Order();
            
                public static Order order(Consumer<LambdaOrderBuilder> consumer){
                    LambdaOrderBuilder builder = new LambdaOrderBuilder();
                    consumer.accept(builder);
                    return builder.order;
                }
            
                public void forCustomer(String customer){
                    order.setCustomer(customer);
                }
            
                public void buy(Consumer<TradeBuilder> consumer){
                    trade(consumer, Trade.Type.BUY);
                }
            
                public void sell(Consumer<TradeBuilder> consumer){
                    trade(consumer, Trade.Type.SELL);
                }
            
                private void trade(Consumer<TradeBuilder> consumer, Trade.Type type) {
                    TradeBuilder builder = new TradeBuilder();
                    consumer.accept(builder);
                    order.addTrade(builder.trade);
            
                }
            }
            ...
            public class TradeBuilder {
            
                Trade trade = new Trade();
            
                public void quantity(int quantity){
                    trade.setQuantity(quantity);
                }
            
                public void price(double price){
                    trade.setPrice(price);
                }
            
                public void stock(Consumer<StockBuilder> consumer){
                    StockBuilder stockBuilder = new StockBuilder();
                    consumer.accept(stockBuilder);
                    trade.setStock(stockBuilder.stock); // 1
                }
            }
            ...
            public class StockBuilder {
            
                Stock stock = new Stock(); // 2
                
                public void symbol(String symbol){
                    stock.setSymbol(symbol);
                }
                
                public void market(String market){
                    stock.setMarket(market);
                }
            }
            /*
            	책을 보고 하던중 이상한 구문을 발견했다. 위의 클래스들은 모두 분리되어 있다. 근데 1에서 볼 수 있듯
            	직접 stock 을 가져다가 사용한다. 그런데 StockBuilder의 멤버변수 stock은 private로 되어 있었고 get과
            	같은 stock을 반환하는 메서드가 존재하지 않았다. 그래서 참조가 되지 않아 "뭐지?"라는 생각을 했다. 그래서
            	접근 제어자를 default로 바꾸어 한 패키지안에 넣었다. 그럼 패키지 내부에서는 참조가 되고 패키지 밖의 클래스들은
            	참조할 수 없기때문이다. 아니면 get과 같은 메서드를 이용해도 된다. 하지만 어떤 것이 더 맞는 방법인지는 모르지만
            	책이 조금 잘못된 것 같다. 유의하자
            */
            ```
            
        
        **결론**
        
        - 메서드 체인의 플루언트 방식과 같이 정의할 수 있다.
        - 중첩 함수 형식처럼 람다 표현식의 중첩 수준과도 비슷하게 도메인 객체의 계층 구조를 유지할 수 있다.
        - 단점으로는 설정 코드가 많이 필요하며 DSL자체가 람다 표현식 문법에 의한 잡음의 영향을 받는다.
    - 📍 **도메인 모델을 활용한 DSL (모든 방식 조합)**
        
        지금까지 3가지의 패턴을 살펴보았다. 각각의 장점과 단점이 존재했고 뭘써야할지 감이 안온다. 하지만 한 DSL에 한가지만 써라는 법은 없다. 우리는 3가지를 다 섞어 볼 것이다.
        
        **최종 목표**
        
        ```java
        Order order = forCustomer("BigBank",
                        buy(t -> t.quantity(80).stock("IBM").on("NYSE").at(125.00)),
                        sell(t -> t.quantity(50).stock("GOOGLE").on("NASDAQ").at(370.00))
                );
        ```
        
        - 🔍 **구현**
            
            ```java
            public class MixedBuilder {
            
                public static Order forCustomer(String customer, TradeBuilder... tradeBuilders){
                    Order order = new Order();
                    order.setCustomer(customer);
                    Stream.of(tradeBuilders).forEach(d -> order.addTrade(d.trade));
                    return order;
                }
            
                public static TradeBuilder buy(Consumer<TradeBuilder> consumer){
                    return builderTrade(consumer,Trade.Type.BUY);
                }
            
                public static TradeBuilder sell(Consumer<TradeBuilder> consumer){
                    return builderTrade(consumer,Trade.Type.SELL);
                }
            
                private static TradeBuilder builderTrade(Consumer<TradeBuilder> consumer, Trade.Type type) {
                    TradeBuilder builder = new TradeBuilder();
                    builder.trade.setType(type);
                    consumer.accept(builder);
                    return builder;
                }
            }
            
            ...
            public class TradeBuilder {
                Trade trade = new Trade();
            
                public TradeBuilder quantity(int quantity){
                    trade.setQuantity(quantity);
                    return this;
                }
            
                public TradeBuilder at(double price){
                    trade.setPrice(price);
                    return this;
                }
            
                public StockBuilder stock(String symbol){
                    return new StockBuilder(this, trade, symbol);
                }
            }
            
            ...
            public class StockBuilder {
                
                private final TradeBuilder builder;
                private final Trade trade;
                private final Stock stock = new Stock();
            
                public StockBuilder(TradeBuilder builder, Trade trade, String symbol) {
                    this.builder = builder;
                    this.trade = trade;
                    stock.setSymbol(symbol);
                }
            
                public TradeBuilder on(String market){
                    stock.setMarket(market);
                    trade.setStock(stock);
                    return builder;
                }
            
            }
            ```
            
        
        **결론**
        
        - 3가지의 DSL을 혼용해서 가독성 잇는 DSL을 만들 수 있다.
        - 한가지 기법만 적용한 DSL에 비해 학습 곡선이 크다.
        - 이보다 더 가독성을 높일 수 있는 방식은 메서드 참조를 이용하면 된다.
- **DSL에 메서드 참조 사용하기**
    
    <aside>
    🙄 우리는 메서드 참조가 더 높은 가독성을 제공해준다는 것을 이미 알고 있다. 그런데 DSL에서 메서드 참조를 사용한다면 더더더더더더더더 높은 가독성을 제공해주지 않을까? 그래서 우리는 메서드 참조를 사용하는 패턴을 배워본다. 실제로 적용할 수 있을 것 같은 예제로 조금 바꾸어보았다.
    
    </aside>
    
    **예제 객체**
    
    ```java
    @Getter
    @Setter
    @AllArgsConstructor
    public class Product{
        double price;
    }
    ```
    
    - 📍 **(좋지 못한 예**🤦‍♂️**) 불리언 플래그 집합을 이용한 할인 적용**
        
        ```java
        public static void main(String[] args) {
                Product product = new Product(10000);
                double v = Discount.DiscountCalculate(product, true, true, false);
        }
        ... 
        class Discount{
        
            public static double DiscountCalculate(Product product, boolean isMember,
                                                   boolean isCoupon, boolean isSpecialDiscount){
        				double value = product.getPrice();
                if (isMember) value = value - memberDiscount(value);
                if (isCoupon) value = value - couponDiscount(value);
                if (isSpecialDiscount) value = value - specialDiscount(value);
                return value;
        
            }
            public static double memberDiscount(double value) {
                return value * 0.1;
            }
        
            public static double couponDiscount(double value){
                return value * 0.2;
            }
        
            public static double specialDiscount(double value){
                return value * 0.3;
            }
        
        }
        result
        7200.0
        ```
        
        **결론**
        
        - 가독성 문제가 들어난다. 메인에서 사용할때 true, true, false를 보면 저게 무슨의미인지 알기 쉽지 않을 것이다.
        - 적용되었는지 아닌지를 파악하기 힘들다. 내부에 플래그가 존재하기 때문에 어떤 것이 적용된 할인인지 알기 어렵다.
    - **📍 (조금 더 유창한 버전🎤) 플래그를 다른 방식으로 적용한 할인**
        
        ```java
        public static void main(String[] args) {
            Product product = new Product(10000);
            double v = new Discount()
        										.withMemberDiscount()
        				            .withCouponDiscountProduct()
        				            .DiscountCalculate(product);
            System.out.println(v);
        }
        ...
        public class Discount {
            private boolean isMember;
            private boolean isCoupon;
            private boolean isSpecialDiscount;
        
            public Discount withMemberDiscount() {
                this.isMember = true;
                return this;
            }
        
            public Discount withCouponDiscountProduct() {
                this.isCoupon = true;
                return this;
            }
        
            public Discount withSpecialDiscountProduct() {
                this.isSpecialDiscount = true;
                return this;
            }
        
            public double DiscountCalculate(Product product) {
                return DiscountCalculate(product, isMember, isCoupon, isSpecialDiscount);
            }
        
            private double DiscountCalculate(Product product, boolean isMember,
                                             boolean isCoupon, boolean isSpecialDiscount) {
                double value = product.getPrice();
                if (isMember) value = value - memberDiscount(value);
                if (isCoupon) value = value - couponDiscount(value);
                if (isSpecialDiscount) value = value - specialDiscount(value);
                return value;
        
            }
        
            private double memberDiscount(double value) {
                return value * 0.1;
            }
        
            private double couponDiscount(double value) {
                return value * 0.2;
            }
        
            private double specialDiscount(double value) {
                return value * 0.3;
            }
        }
        ```
        
        **결론**
        
        - 좀 더 명확하게 어떤 할인이 적용된지 파악 할 수 있다.
        - 가독성도 좋아진다.
        - 단점으로는 장황한 코드이다. 어쨋든 불리언 플래그는 사라지지 않는다. 결론적으로 장황한 if 문을 줄줄줄 적어야한다. 이러한 단점은 확장성에도 영향을 준다. if 분기는 Discount 클래스 내부에서만 정의해야한다. 이로인하여 확장성의 영향을 받는다.
    - 📍 **(유창하게 해보자** 👏🏻**) 유창한 할인 적용 버전**
        
        ```java
        public static void main(String[] args) {
            Product product = new Product(10000);
            Discount discount = new Discount();
            double v = new Discount()
                    .with(Discount::memberDiscount)
                    .with(Discount::couponDiscount)
                    .DiscountCalculate(product);
            System.out.println(v);
        }
        
        ...
        public class Discount {
            public DoubleUnaryOperator discountFunction = d -> d;
        
            public Discount with(DoubleUnaryOperator doubleUnaryOperator){
                this.discountFunction =discountFunction.andThen(doubleUnaryOperator);
                return this;
            }
        
            public double DiscountCalculate(Product product) {
                return  discountFunction.applyAsDouble(product.getPrice());
            }
        
            public static double memberDiscount(double value) {
                return value - (value * 0.1);
            }
        
            public static double couponDiscount(double value) {
                return value - (value * 0.2);
            }
        
            public static double specialDiscount(double value) {
                return value - (value * 0.3);
            }
        }
        
        ```
        
        **결론**
        
        - 훨씬 확장성이 좋다. 만약 static 메서드만 존재하고 함수의 시그니처가 DoubleUnaryOperator와 동일하다면 with()을 통하여 체이닝이 가능해진다.
        - **확장성 예시** 🔍
            
            ```java
            public static void main(String[] args) {
                Product product = new Product(10000);
                Discount discount = new Discount();
                double v = new Discount()
                        .with(Discount::memberDiscount)
                        .with(Discount::couponDiscount)
                        .with(OtherClass::eventDiscount) // 하지만 같이 사용가능
                        .DiscountCalculate(product);
                System.out.println(v);
            }
            
            public static double eventDiscount(double value){ // Discount class와 다른 클래스에 정의
                return value - (value * 0.5);
            }
            ```
            
        - 하지만 생각해볼 수 있는 단점은 과연 static 메서드가 늘어나는 것이 옳을지에 대한 의문은 가지고 있다.

**결론**

- DSL 은 결국 도메인 전문가와 개발자의 간격을 좁히기 위해 탄생했다 결국 개발자가 아닌 사람도 이해할 수 있는 언어로 이런 비즈니스로직을 구현할 수 있다고 해서 도메인전문가가 프로그래머가 되는 것은 아니다 하지만 로직을 읽고 검증하는 역할은 수행할 수 있다
- DSL은 크게 내부적(애플리케이션을 개발한 언어를 그대로 활용한 것, 우리의 자바 API와 비슷함) DSL과 외부적(직접 언어를 설계함, 우리가 만든 것 같은 사용 코드) DSL로 분류할 수 있다
- 자바와 같은 문법의 엄격함을 가진 언어는 DSL개발 언어로 적합하지 않다 하지만 람다와 메서드 참조 덕분에 개발 환경이 많이 개선되었다
- 최신 자바는 자체 API에 작은 DSL을 제공한다 이들 Stream, Collectors 클래스 등에서 제공하는 작은 DSL은 특히 컬렉션 데이터의 정렬, 필터링, 그룹화, 변환에 유용하다
