개발을 하면서 NullPointerException을 자주 겪는다 NullPointerException을 처리하는 방법은 여러가지가 있다

### 보수적인 자세로 NullPointerException 줄이기

null을 피하기 위해서 필요한 곳에 다양한 null 확인 코드를 추가하여 예외를 처리할 수 있다
하지만 이렇게 하게되면 중복된 if 코드가 추가되며 코드 들어쓰기 수준이 늘어나게 되어 코드의 구조가 엉망이 되고 가독성이 떨어진다

```java
public String getCarName(Person person) {
	if(person != null) { 
		Car car = person.getCar(); 
		if(car != null) {
			return car.getName();
		}
	}
	return "Unknown";
}

// if코드가 중복적으로 발생하여 코드의 구조가 엉망이 됨
```

### null 때문에 발생하는 문제

1. 에러의 근원이다 
    - NullPointerException은 자바에서 가장 흔히 발생하는 에러이다
2. 코드를 어지럽게한다 
    - 중첩된 null 확인 코드를 추가해야 하므로 코드의 가독성이 떨어진다
3. 아무 의미가 없다 
    - null은 아무 의미도 표현하지 않는다
4. 자바 철학에 위배된다 
    - 자바는 개발자로부터 모든 포인터를 숨겼는데 null 포인터는 예외이다
5. 형식 시스템에 구멍을 만든다
    - null은 무형식이며 정보를 포함하고 있지 않으므로 모든 참조 형식에 null을 할당 할 수 있다 이런 식으로 null을 할당하기 시작하면서 시스템의 다른 부분으로 null이 퍼졌을 때 애초에 null이 어떤 의미로 사용되었는지 알 수 없다

### Optional 클래스

자바 8은 하스켈과 스칼라의 영향을 받아서 java.util.Optional<T>라는 새로운 클래스를 제공한다 Optional은 선택형값을 캡슐화하는 클래스다 만약 값이 없다면 Optional은 null을 할당하는 것이 아닌 Optional.empty 메소드로 Optional을 반환할 수 있다 Optional.empty는 Optional의 특별한 싱글턴 인스턴스를 반환하는 정적 팩토리 메소드다 empty 메소드는 Optional 객체이므로 다양한 방식으로 활용할 수 있다

- **값이 없는 상황에서 Optional의 활용**
    
    ```java
    @Getter
    @Setter
    public class Person{
        private Optional<Car> car;
    }
    @Getter
    @Setter
    public class Car{
        private Optional<Insurance> insurance;
    }
    
    @Getter
    @Setter
    public class Insurance{
        private String name;
    }
    
    /*
    	Optional을 사용함에 따라 객체 모델의 의미가 더 명확해 졌다
    	Opional<Car> Optional<Insurcance>와 같이 자동차와 보험을 소유할 수도 있고
    	아닐 수도 있음을 명확히 설명하고 있다
    	
    	하지만 보험회사의 name은 Optional<String>이 아니라 String이기 때문에 
    	반드시 name을 가져야 한다는 것을 명확하게 보여주고 있다
    	
    	보험회사의 이름이 없어서 NullPointerException이 발생하는 것은 
    	해당 문제를 예외처리 하는것이 아닌 보험회사의 이름은 반드시 가져야 하며
    	이름이 없다면 없는 이유가 없는지 밝혀서 문제를 해결해야 한다
    	
    	Optional을 이용하면 값이 없는 상황에서 데이터에 문제가 있는 것인지 
    	알고리즘의 문제가 있는 것인지 명확하게 파악 할 수 있다
    	
    	즉 Optional의 역할은 더 이해하기 쉬운 API를 설계하도록 돕는 것이다
    */
    ```

### Optional 적용 패턴

실제로 Optional을 어떻게 활용할 수 있는지 Optional을 감싼 값을 실제로 어떻게 사용할 수 있는지 알아본다

- **Optional 객체 만들기**
    
    **빈 Optional 만들기**
    
    ```java
    Optional<Car> car = Optional.empty();
    ```
    
    **null이 아닌 값으로 Optional 만들기**
    
    ```java
    Car car = new Car();
    Optional<Car> optCar = Optional.of(car);
    /*
    	car가 null 이라면 즉시 NullPointerException 이 발생한다
    	Optional을 사용하지 않았다면 car의 프로퍼티에 접근할때 에러가 발생한다
    */
    Car car = null;
    Optional<Car> optCar = Optional.of(car); // npe 발생
    ... 결국 아래의 형식과 비슷함
    Car car = null;
    Objects.requireNonNull(car); // 이건 많이 쓴다
    car.getInsurance();
    ```
    
    **null 값으로 Optional 만들기**
    
    ```java
    Car car = null;
    Optional<Car> optCar = Optional.ofNullable(car);
    System.out.println(optCar); // Optional.empty 을 반환한다
    ```
    
- **map()으로 Optional의 값을 추출하고 변환하기**
    
    **보통 정보를 추출할 때 Optional을 많이 사용한다** 예를 들어 보험회사의 이름을 추출한다고 가정하자 다음 코드처럼 이름 정보에 접근하기 전 null인지 확인 해야한다
    
    ```java
    Insurance insurance = null;
    if (insurance != null) {
        String name = insurance.getName();
        System.out.println(name);
    }
    /*
    	이러한 유형의 패턴에 사용할 수 있도록 Optional은 map 메소드를 지원한다
    */
    
    Insurance nullInsurance = null;
    Optional<Insurance> insurance = Optional.ofNullable(nullInsurance);
    Optional<String> s = insurance.map(Insurance :: getName);
    System.out.println(s);
    
    /*
    	get()을 사용할때의 차이점은 기본값으로 넘어온 상태에서도 Optional<T> 형식이 유지된다
    	스트림에서 제공하는 map 과 유사하고 거의 비슷한 일을 한다 
    */
    ```
    
- **flatMap으로  Optional 객체 연결**
    
    map을 사용하는 방법을 배웠으므로 다음처럼 우리는 map을 이용하여 코드를 재구현 할 수 있다 Optional로 감싸고 있는 객체가 또 다른 Optional로 감싸져 있다면 map을 통하여는 원하는 값을 얻지 못하게 된다 예제에서 return 되는 형식은 Optional<Optional<Car>> 이다 이럴 때 사용하는 것이 flatMap이다
    
    ```java
    Person nullPerson = null;
    Optional<Person> optional = Optional.ofNullable(nullPerson);
    optional.flatMap(Person :: getCar)
            .flatMap(Car :: getInsurance)
            .map(Insurance :: getName)
            .orElse("Unknown"); // 만약 값이 없는 빈 Optional 객체인 경우 해당 값을 리턴
    
    @Getter
    @Setter
    public class Person{
        private Optional<Car> car;
    }
    @Getter
    @Setter
    public class Car{
        private Optional<Insurance> insurance;
    }
    
    @Getter
    @Setter
    @AllArgsConstructor
    public class Insurance{
        private String name;
    }
    
    /*
    	이렇게 또 다른 Optional을 사용할 때는 flatMap을 사용하여 처리해야 한다
    	Optional의 값이 없으면 빈 Optional 객체를 리턴하게 된다 
    	이때 orElse()를 사용하여 값이 없을 때 기본값을 리턴하게 할 수 있다
    */
    ```
    

### Optional 스트림 조작

- **Optional 스트림 조작 (자바 버전  9이상)**
    
    자바 9에서는 Optional을 포함하는 스트림을 쉽게 처리할 수 있도록 Optional에 Stream() 메소드를 추가했다
    
    ```java
    Set<String> collect = persons.stream()
                    .map(Person :: getCar)
                    .map(car -> car.flatMap(Car :: getInsurance)) 1
                    .map(insurance -> insurance.map(Insurance :: getName)) 2
                    .flatMap(Optional :: stream)
                    .collect(Collectors.toSet());
    System.out.println(collect);
    
    /*
    	size가 10인 List<Person>이 있다고 가정했을 때 보통 Optional이 아니더라도 스트림을 조작하면 
    	긴 체이닝 과정이 필요하다 하지만 Optional의 추가로 더욱 길어졌다 또한 변환과정이 그렇게
    	매끄럽지 못하다 일단 1에서는 Stream<Optional<Insurance>> 가 반환된다 이를 flatMap을 통하여 평면화 시키는 과정이
    	필요하다 다음 2에서는 Stream<Optional<String>>의 형태로 반환된다 언랩하는 과정이 상당히 길어질 수 있다
    */
    
    ...
    Stream<Optional<String>> optionalStream = persons.stream()
                    .map(Person :: getCar)
                    .map(car -> car.flatMap(Car :: getInsurance))
    		            .map(insurance -> insurance.map(Insurance :: getName));
    
    Set<String> collect = optionalStream.filter(Optional :: isPresent) // 값의 유/무 판단
    				        .map(Optional :: get) // 있는 값만 get을 통하여 추출됨
    				        .collect(Collectors.toSet());
    
    System.out.println(collect);
    /*
    	위의 코드에서 볼 수 있듯이 더 안전하게 값을 추출하는 방법이다 
    	위의 Stream<Optional<String>>을 얻은뒤 
    	Optional의 isPresent를 통하여 null이 아닌 값을 추출한다 
    	그리고 get을 통하여 존재하는 값만 추출할 수 있다
    */
    ```
    
- **디폴트 액션과 Optional 언랩**
    
    빈 Optional인 상황에서는 Optional은 empty를 반환한다 빈 Optional 객체에 사용하는 다양한 디폴트 액션과 언랩(Optional에서 값을 사용할 수 있게 박스를 제거하는 과정)을 살펴보자
    
    ```java
    @Getter
    @Setter
    @AllArgsConstructor
    class Person {
        private Optional<Car> car;
    }
    
    **person.getCar().get(); // get()**
    /*
    	가장 기본적인 메서드면서 가장 안전하지 않는 메소드이다 
    	get은 래핑된 값이 있으면 해당 값을 반환하고 없으면 NosuchElementException을 던진다
    	결국 이 상황은 중첩된 null확인코드와 다른게 하나도 없다
    */
    
    **person.getCar().orElse(new Car()); // orElse()**
    /* Optional이 값을 포함하고 있지 않을때 기본값을 제공할 수 있다 */
    
    **person.getCar().orElseGet(); // orElseGet()
    /*** 
    	orElse()에 대응하는 게으른 버전의 메소드다
    	Optional에 값이 없을때만 Supplier 가 실행되기 때문이다
    	디폴트 메소드를 만드는데 시간이 걸리거나 Optional이 비어있을 때만 
    	기본값을 생성하고(기본 값이 반드시 필요할때) 싶다면이 메소드를 사용해야한다 
    */
    
    **person.getCar().orElseThrow(); // orElseThrow()
    /*** 
    	optional이 비어있을때 예외를 발생시킬 수 있다 
    	get 메소드와 비슷하다 하지만 발생시킬 예외를 선택할 수 있다
    */
    **person.getCar().ifPresent(); // ifPresent()
    /***
    	값이 존재할 때 인수로 넘겨준 동작을 실행할 수 있다
    	값이 없으면 아무 일도 일어나지 않는다
    */
    **person.getCar().ifPresentOrElse(); // ifPresentOrElse()
    /*** 
    	Optional이 비었을 때 실행할 수 있는 Runnable을 인수로 받는다는 점만 
    	ifPresent() 와 다르다
    */
    ```
    
- **두 Optional 합치기**
    
    이제 Person과 Car 정보를 이용해서 가장 저렴한 보험료를 제공하는 보험회사를 찾는 몇몇 복잡한 비즈니스 로직을 구현한 외부 서비스가 있다고 가정하자
    
    ```java
    public Insurance findCheapestInsurance(Person person, Car car){
            Insurance insurance = new Insurance();
            return insurance;
    }
    
    public Optional<Insurance> nullSafeCheapestInsurance(Optional<Person> person, Optional<Car> car){
        if (person.isPresent() && car.isPresent())
            return Optional.of(findCheapestInsurance(person.get(), car.get()));
        return Optional.empty();
    }
    ```
    
    이 메소드의 장점은 Person과 Car의 시그니처만으로 둘다 아무값도 반환하지 않을 수 있다 하지만 안타깝게도 구현코드는 null확인 코드와 다를 것이 거의 없다
    
    **Optional 언랩하지 않고 두 Optional 합치기**
    
    ```java
    public Optional<Insurance> nullSafeCheapestInsurance(Optional<Person> person, Optional<Car> car){
    	      return person.flatMap(p -> car.map(c -> findCheapestInsurance(p,c)));
    }
    /*
    	1. Optional<Person>에 flatMap을 호출
    		1.1 Optional<Person> 이 비어 있다면 -> Optional.empty 반환
    		1.2 Optional<Person> 이 존재한다면  -> person 반환
    	2. Optional<Car>에 map을 호출
    		2.1 Optional<car> 이 비어 있다면 -> Optional.empty 반환
    		2.2 Optional<car> 이 존재한다면  -> car 반환
    */
    ```
    
    **이 로직을 통해서 알 수 있는 점**
    
    ```java
    // map 함수
    public <U> Optional<U> map(Function<? super T, ? extends U> mapper) {
            Objects.requireNonNull(mapper);
            if (!isPresent()) {
                return empty();
            } else {
                return Optional.ofNullable(mapper.apply(value)); // 일반적 객체를 Optional<U>로 반환
            }
    }
    // flatMap 함수
    public <U> Optional<U> flatMap(Function<? super T, ? extends Optional<? extends U>> mapper) {
            Objects.requireNonNull(mapper);
            if (!isPresent()) {
                return empty();
            } else {
                @SuppressWarnings("unchecked")
                Optional<U> r = (Optional<U>) mapper.apply(value); // Optional<?> 객체를 Optional<U>로 반환
                return Objects.requireNonNull(r);
            }
        }
    ```
    
    <aside>
    ⁉️ **flatMap과 map의 구분?**
    Optional 객체의 반환 의무를 지닌 mapper함수를 사용한다면 flatMap 사용
    Optional 객체의 반환 의무를 지니지 않은 mapper 함수를 사용한다면 map 사용
    
    </aside>
    
- **필터로 특정 값 거르기**
    
    종종 객체의 메소드를 호출해서 어떤 프로퍼티를 확인해야할 때가 있다 예를 들어 보험회사 이름이 "CambridgeInsurance" 인지 확인해야 한다면 Insurance가 먼저 null인지 확인한 다음에 getName을 호출해야한다
    
    ```java
    // 일반적인 필터링하는 방법
    Insurance insurance = null;
    if (insurance != null && "CambridgeInsurance".equals(insurance.getName())) {
        System.out.println("ok");
    }
    
    // filter 메서드를 이용하는 방법
    Optional<Insurance> optInsurance = Optional.empty();
    optInsurance
            .filter(insurance -> "CambridgeInsurance".equals(insurance.getName()))
            .ifPresent(s -> System.out.println("ok"));
    
    ```
    
    filter는 프레디케이트를 인수로 받는다 Optional 객체가 값을 가지며 프레디케이트와 일치하면 filter 메소드는 그 값을 반환하고 그렇지 않으면 빈 Optional 객체를 반환한다
    만약 Optional이 비어 있다면 filter연산은 아무동작하지 않는다 반대로 Optional이 값이 있으면 filter는 동작한다
    
    ```java
    public Optional<T> filter(Predicate<? super T> predicate) {
            Objects.requireNonNull(predicate);
            if (!isPresent()) {
                return this; // 나 자신을 반환 하고 멈춘다.
            } else {
                return predicate.test(value) ? this : empty();
    	      }
    }
    ```
    
    **예제**
    
    Person / Car / Insurance 가 있다. 그런데 Person에는 int age 필드가 존재한다. 이럴때 26세 이상의 나이를 가지고 있을때 보험 이름을 반환하도록 하라
    
    ```java
    public static void main(String[] args) {
    /*
    	Persons = 
    [Optional[Person(car=Optional[Car(insurance=Optional[Insurance(name=보험 이름 : 1)])], age=3)], 
    Optional[Person(car=Optional[Car(insurance=Optional[Insurance(name=보험 이름 : 2)])], age=6)], 
    Optional[Person(car=Optional[Car(insurance=Optional[Insurance(name=보험 이름 : 3)])], age=9)], 
    Optional[Person(car=Optional[Car(insurance=Optional[Insurance(name=보험 이름 : 4)])], age=12)], 
    Optional[Person(car=Optional[Car(insurance=Optional[Insurance(name=보험 이름 : 5)])], age=15)], 
    Optional[Person(car=Optional[Car(insurance=Optional[Insurance(name=보험 이름 : 6)])], age=18)], 
    Optional[Person(car=Optional[Car(insurance=Optional[Insurance(name=보험 이름 : 7)])], age=21)], 
    Optional[Person(car=Optional[Car(insurance=Optional[Insurance(name=보험 이름 : 8)])], age=24)], 
    Optional[Person(car=Optional[Car(insurance=Optional[Insurance(name=보험 이름 : 9)])], age=27)], 
    Optional[Person(car=Optional[Car(insurance=Optional[Insurance(name=보험 이름 : 10)])], age=30)]]
    
    */
    
            List<String> names = InsuranceNameFilter(persons);
    	      System.out.println(names);
    }
    
    public static List<String> InsuranceNameFilter(List<Optional<Person>> persons){
        return persons
                .stream()
                .map(Main::InsuranceNameFilter)
                .collect(toList());
    }
    
    public static String InsuranceNameFilter(Optional<Person> optPerson){
        return optPerson
                .filter(person -> person.getAge() >= 26)
                .flatMap(Person::getCar)
                .flatMap(Car::getInsurance)
                .map(Insurance::getName)
                .orElse("UnKnown");
    }
    /*
    result
    [UnKnown, UnKnown, UnKnown, UnKnown, UnKnown, UnKnown, UnKnown, UnKnown, 보험 이름 : 9, 보험 이름 : 10]
    */
    ```
    

### Optional 클래스의 메소드

| 메소드 | 값 O | 값 X | 관계없음 |
| --- | --- | --- | --- |
| empty |  |  | 빈 Optional 값 리턴 |
| filter | Predicate 와 일치할 경우 값을 포함하는 Optional 반환 | predicate 와 일치하지 않을 경우 빈 Optional 반환 |  |
| flatMap | 인수로 제공된 함수를 적용한 결과 Optional 반환 | 빈 Optional 반환 |  |
| get | Optional이 감싸는 값 반환 | NoSuchElementException 발생 |  |
| isPresent | true 반환 | false 반환 |  |
| ifPresentOrElse | 지정된 Consumer를 실행 |  |  |
| map | 제공된 함수를 적용 후 Optional로 감싼 결과 반환 | 빈 Optional 반환 |  |
| of | 값을 감싸는 Optional 반환  | NullPointerException 발생 |  |
| ofNullable | 값을 감싸는 Optional 반환 | 빈 Optional 반환 |  |
| or | 같은 Optional 반환 | Supplier에서 만든 Optional 반환 |  |
| orElse | 받은 값을 반환 | 설정된 기본 값 반환 |  |
| orElseGet | 받은 값을 반환 | Supplier에서 제공하는 값을 반환 |  |
| orElseThrow | 받은 값을 반환 | Supplier에서 생성한 예외 발생 |  |
| stream | 존재하는 값만 포함하는 스트림 반환 | 빈 스트림 반환 |  |

### 값이 없을 수도 있는 상황에서 프로그래밍 방법

- **잠재적으로 null이 될 수 있는 대상 Optional로 감싸기**
    
    기존 자바 API에서는 null을 반환하면서 요청한 값이 없거나 어떤 문제로 계산에 실패했음을 알린다. 예를 들어 Map의 get()는 요청한 키에 대응하는 값을 찾지 못했을 때 null을 반환한다 지금까지 살펴본 것처럼 null을 반환하는 것보다는 Optional을 반환하는 것이 더 바람직하다
    
    ```java
    // map에 key가 존재하면 값 반환, map에 key가 존재하지 않는다면 null반환
    Object value = map.get("key");
    
    // map에 key가 존재하면 Optional<값> 반환, map에 key가 존재하지 않는다면 Optional.empty 반환
    Optional<Object> optValue = Optional.ofNullable(map.get(key));
    ```
    
- **기본형 Optional을 사용하지 말아야 하는 이유**
    
    스트림처럼 Optional도 기본특화형이 존재한다
     하지만 이를 사용하는 것은 오히려 Optional의 이점을 반감시킨다
    
    - Optional의 최대 요소 수는 한 개이므로 Optional에서는 기본형 특화 클래스로 성능을 개선할 수 없다
    - Optional 클래스의 유용한 메소드 map, flatMap, filter 등을 지원하지 않으므로 기본형 특화 Optional을 사용할 것을 권장하지 않는다
    - 스트림과 마찬가지로 기본형 특화 Optional로 생성한 결과는 다른 일반 Optional과 혼용할 수 없다 (예를 들어 OptionalInt 형을 반환한 이후 Optional의 flatMap을 사용할 수 없다)
      
- **응용**
    
    Optional 클래스의 메소드를 실제 업무에서 어떻게 활용할 수 있는지 살펴본다 예를들어 프로그램의 설정 인수로 properties를 전달한다고 가정하자 그리고 다음과 같은 Properties로 우리가 만든 코드를 테스트 할 것이다
    
    - properties에 key와 value로 값이 존재한다고 가정한다
    - properties에 value는 지속시간(int형) 으로 변형한다
    - 값이 없다면 0 초를 반환한다
    - 값을 초 단위로 변환 할 수 없다면 0을 반환한다
    
    ```java
    static Properties properties = new Properties();
        {
            properties.setProperty("a","5");
            properties.setProperty("b","true");
            properties.setProperty("c","-3");
      }
    
    @Test
    public void durationTest(){
        assertEquals(5, readDuration(properties,"a"));
        assertEquals(0, readDuration(properties,"b"));
        assertEquals(0, readDuration(properties,"c"));
        assertEquals(0, readDuration(properties,"d"));
    }
    ... // 기존의 명령형 코드
    public static int readDuration(Properties properties, String name){
        String property = properties.getProperty(name);
        if (property != null) {
            try {
                int i = Integer.parseInt(property);
                if (i > 0) return i;
            }catch (NumberFormatException e) {}
        }
        return 0;
    }
    ... // Optional을 활용한 코드
    public static int readDuration(Properties properties, String name){
    	    return Optional.ofNullable(properties.getProperty(name)) // 이미 stringToIntOptional에서 empty를 반환함 비어 있어도 상관없음
                    .flatMap(Main::stringToIntOptional)
                    .filter(i -> i>0)
                    .orElse(0);
    
    }
    public static Optional<Integer> stringToIntOptional(String num){
        try {
            return Optional.of(Integer.parseInt(num));
        }catch (NumberFormatException e){
            System.out.println("NumberFormatException!");
            return Optional.empty();
        }
    }
    
    ```
