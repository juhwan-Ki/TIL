### 스트림이란?

스트림은 자바 8 API에 새로 추가된 기능으로 스트림을 이용하면 선언형(데이터를 처리하는 임시 구현 코드 대신 질의로 표현할 수 있다)으로 컬렉션 데이터를 처리할 수 있다 또한 스트림을 이용하면 멀티 스레드 코드를 구현하지 않아도 데이터를 투명하게 병렬로 처리할 수 있다

```java
// 칼로리를 기준으로 요리를 정렬하는 코드
 List<Dish> menu = Arrays.asList(
                new Dish("베이글", true, 300, Dish.Type.MEAT),
                new Dish("짬뽕", false,500, Dish.Type.OTHER),
                new Dish("라면", false,450, Dish.Type.OTHER),
                new Dish("돈가스", false,650, Dish.Type.MEAT),
                new Dish("포케",true,350, Dish.Type.OTHER),
                new Dish("샐러드", true,270, Dish.Type.OTHER),
                new Dish("연어", false, 450, Dish.Type.FISH),
                new Dish("왕새우", false, 300, Dish.Type.FISH));

// 스트림 사용 전 코드
				List<Dish> lowCalorieDishs = new ArrayList<>();
        for (Dish dish : menu) {
            if(dish.getCalorie() > 400) {
                lowCalorieDishs.add(dish);
            }
        }
        Collections.sort(lowCalorieDishs, new Comparator<Dish>() {
            @Override
            public int compare(Dish o1, Dish o2) {
                return Integer.compare(o1.getCalorie(), o2.getCalorie());
            }
        });

        List<String> lowCalorieDishesName = new ArrayList<>();
        for (Dish dish : lowCalorieDishs) {
            lowCalorieDishesName.add(dish.getName());
        }

        for (String name : lowCalorieDishesName) {
            System.out.println(name);
        }

// 스트림 사용 후 코드
				List<String> lowCalorieDishName = menu.stream()
                .filter(dish -> dish.getCalorie() < 400) // 400 칼로리 이하 요리 선택
                .sorted(Comparator.comparing(Dish::getCalorie)) // 칼로리로 요리 정렬
                .map(Dish::getName) // 요리명 추출
                .toList(); // 리스트 형식으로 반환

        for (String name : lowCalorieDishName) {
            System.out.println(name);
        }
//  stream을 parallelStream()으로 변경하면 멀티코어 아키텍처에서 벙렬로 실행가능
```

스트림은 선언형으로 코드를 구현할 수 있으며 즉 루프와 if 조건문 등의 제어 블록을 사용해서 어떻게 동작을 구현하는지 지정할 필요 없이 동작의 수행을 지정할 수 있다

여러 연산(filter, map, sorted 등) 여러 빌딩 블록 연산을 연결해 복잡한 데이터 처리 파이프라인을 만들 수 있으며 데이터 처리 과정을 병렬화 하면서 스레드와 락을 걱정할 필요가 없다

스트림 요약

- 선언형 : 더 간결하고 가독성이 좋아짐
- 조립할 수 있음 : 유연성이 좋아짐
- 병렬화 : 성능이 좋아짐

### 스트림 시작하기

자바8 컬렉션에서는 스트림을 반환하는 stream 메소드가 추가되었다
스트림이란 데이터 처리 연산을 지원하도록 소스에서 추출된 연속된 요소라고 정의 할 수 있다

- 연속된 요소
    - 스트림은 특정 요소 형식으로 이루어진 연속된 값 집합의 인터페이스를 제공
    - 컬렉션은 자료구조로써 시간, 공간의 복잡도와 관련된 요소 저장 및 접근 연산이 발생
    - 스트림은 filter, sorted 등 표현 계산식이 주를 이룸
    - 컬렉션의 주체는 데이터고 스트림의 주제는 계산
- 소스
    - 스트림은 컬렉션, 배열, I/O 자원 등의 데이터를 제공 소스로부터 데이터를 소비
- 데이터 처리 연산
    - 스트림은 함수형 프로그래밍 언어에서 일반적으로 지원하는 연산과 DB와 비슷한 연산을 지원하며 스트림 연산은 순차적으로 또는 병렬로 실행할 수 있음

스트림의 특징

- 파이프라이닝
    - 스트림 연산끼리 파이프라인을 구성 할 수 있도록 스트림 자신을 반환
    
    ```java
    List<String> lowCalorieDishes = menu.stream()
            .filter(m ->  m.getCalorie() < 350)
            .sorted(Comparator.comparing(Dish::getCalorie))
            .map(Dish::getName)
            .toList();
    ```
    
    - 이런 코드가 있다고 하면 filter()가 모두 실행되고 난 다음에야 sorted()가 실행되는 것이 아니라, filter, sorted, map로 이루어진 스트림 파이프라인이 구성되고 난 다음에 스트림이 소스를 소비하는 방식
    - 덕분에 lazy evaluation, short circuit같은 최적화도 쉽게 얻을 수 있음
- 내부 반복
    - 반복자(ex int i = 0)를 이용해서 명식적으로 반복하는 컬렉션과 달리 스트림은 내부 반복을 지원
    
    ```java
    public static void main(String[] args) {
       List<Dish> menu = Arrays.asList(
              new Dish("베이글", true, 300, Dish.Type.OTHER),
              new Dish("소고기", false, 350, Dish.Type.MEAT),
              new Dish("연어", false, 450, Dish.Type.FISH),
              new Dish("짬뽕", false,500, Dish.Type.OTHER),
              new Dish("돈가스", false,650, Dish.Type.MEAT),
              new Dish("라면", false,450, Dish.Type.OTHER),
              new Dish("포케",true,350, Dish.Type.OTHER),
              new Dish("샐러드", true,270, Dish.Type.OTHER),
              new Dish("치킨", false, 600, Dish.Type.MEAT),
              new Dish("피자", false, 700, Dish.Type.OTHER),
              new Dish("고등어", false, 230, Dish.Type.FISH));
    
        List<String> names = menu.stream()
                .filter(dish -> {
                    System.out.println("filtering " + dish.getName());
                    return dish.getType() == Dish.Type.OTHER;
                })
                .map(dish -> {
                    System.out.println("mapping " + dish.getName());
                    return dish.getName();
                })
                .limit(3)
                .collect(toList());
        System.out.println(names);
    }
    
    // 결과
    filtering 베이글
    mapping 베이글
    filtering 소고기
    filtering 연어
    filtering 짬뽕
    mapping 짬뽕
    filtering 돈가스
    filtering 라면
    mapping 라면
    [베이글, 짬뽕, 라면]
    ```
    
    - 내부 반복을 통해 1개의 값만 스트림을 소비 하고 그 후 다른 값이 스트림을 소비
    - limit3의 제한에 걸려서 다른 값들이 더 사용되지 않고 종료됨(short circuit)

스트림의 파이프라인은 소스에 적용하는 질의 같은 존재로 filter, map, limit 와 같은 중간 연산을 사용해서 생성한다 생성된 파이프라인은 collect를 통해 파이프라인을 처리한 후 결과를 반환한다

- filter : 람다를 인수로 받아 스트림에서 특정 요소 제외
- map : 람다를 이용하여 한 요소를 다른 요소로 변환하거나 정보를 추출
- limit : 정해진 개수 이상의 요소가 스트림에 저장되지 못하게 스트림 크기를 축소
- collect : 스트림을 다른 형식으로 변환하여 반환

### 스트림 컬렉션

> 컬렉션과 스트림의 차이
> 
> - 컬렉션
>     - 컬렉션은 현재 자료구조가 포함하는 모든 값을 메모리에 저장하는 자료구조
>     - 모든 요소들은 컬렉션에 추가하기 전에 계산되어야 한다
>         - 컬렉션에 요소를 추가 하거나 삭제 하기 위해서는 모든 요소가 메모리에 저장되어 있어야함
>     - 외부 반복을 통해 사용자가 직접 반복 작업을 거쳐 요소를 가져올 수 있다
> - 스트림
>     - 요청할 때만 요소를 계산하는 고정된 자료구조
>     - 스트림은 요소를 추가하거나 삭제 할 수 없다
>     - 내부 반복으로 추출 요소만 선언해주면 알아서 반복 처리를 진행한다
>     - 스트림은 데이터를 메모리에 저장하지 않고 요청할 때만 사용하고 종료하기 때문에 한번만 탐색이 가능하다

### 외부 반복 VS 내부 반복

컬렉션은 외부 반복을 통해 사용자가 직접 요소를 반복 해야 한다
외부 반복을 사용하면 병렬성을 스스로 관리해야 한다

```java
List<String> names = new ArrayList<>();
for (Dish dish : menu) {
    names.add(dish.getName());
}
```

스트림은 내부 반복을 통해 반복 과정을 신경 쓰지 않고도 요소를 반복 할 수 있다
스트림의 내부 반복은 병렬 처리를 지원하기 때문에 병렬성을 따로 관리하지 않아도 되며 반복을 더 최적화된 다양한 순서로 처리할 수 있다 하지만 내부 반복을 사용하려면 filter와 map같이 반복을 숨겨주는 연산 리스트가 미리 정의되어 있어야 한다 

```java
List<String> names = menu.stream()
        .map(Dish::getName) // 요리명 추출
        .collect(Collectors.toList());  // 파이프라인 실행(반복자 필요 없음)
```

### 스트림 연산

스트림의 연산은 중간 연산과 최종 연산으로 구분된다

- 중간 연산
    
    filter나 sorted 같은 중간 연산은 다른 스트림을 반환한다 그렇기 때문에 여러 중간 연산을 연결해서 질의를 만들 수 있다 중간 연산은 최종 연산이 실행되기 전까지는 아무 연산도 수행하지 않는다 
    
    - 종류 : filter, map, limit, sorted, distinct 등
    
    > 지연 연산(Lazy Evaluation)
    > 
    > - 결과값이 필요할 때까지 연산을 늦추는 기법으로 불필요한 연산을 줄여 효과적으로 연산을 수행하도록 하는 기법이다
    > - 스트림 파이프라인이 실행하게 되면 JVM은 곧바로 스트림 연산을 실행시키는 것이 아니라 해당 스트림 파이프라인에 어떠한 중간 연산과 최종 연산으로 구성되어 있는지 확인하고 확인된 결과를 바탕으로 최적화를 진행할지 계획한다
    > 
    > 최적화 전략
    > 
    > - 루프 퓨전(Loop Fusion)
    >     - 파이프라인에서 연속적으로 체이닝된 복수의 스트림 연산을 하나의 연산 과정으로 병합 시키는 것을 말한다
    >     - 개별 스트림 요소에 접근하는 횟수가 초소화 되어 연산의 횟수를 줄일 수 있다
    >         - primitive 타입의 접근보다 wrapper 타입에 대한 접근은 상대적으로 오버헤드가 크기 때문에 스트림에 대한 접근을 최소화할수록 성능적인 측면에서 더 효과적
    >         (primitive 타입은 stack에서 값을 바로 가져와서 사용하지만 wrapper 타입은 참조타입이기 때문에 heap 영역에서 참조값을 가져다 사용하여 추가적인 오버헤드가 발생함)
    >     - 하지만 모든 스트림 연산에 대해 루프 병합 작업이 적용되는 것은 아니다
    > - 쇼트 서킷(Short Circuit)
    >     - 불필요한 연산을 의도적으로 수행하지 않음으로써 실행 속도를 높이는 기법을 의미한다
    >     - limit 같은 연산을 활용하여 스트림의 일부 요소들에 대한 연산을 완전히 생략한다
- 최종 연산
    
    스트림의 요소를 소모하여 결과를 만들어내는 연산으로 최종 연산 후에는 스트림이 닫히게되며 값은 단일 값, 배열, 컬렉션이 될 수 있다
    
    - 종류 : collection, allMatch, forEach, count 등
