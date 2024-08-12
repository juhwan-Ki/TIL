Java8이 나오기 전의 Java API에서 날짜와 시간 관련 기능은 그리 만족스럽지 못하였다 하지만 Java8이 나오면서 지금까지의 날짜와 시간 문제를 개선하는 새로운 날짜와 시간 API가 제공되었다

Java 1.0에서 java.util.Date 클래스 하나로 날짜와 시간 관련 기능을 제공했다 날짜를 의미하는 Date 클래스의 이름과 달리 Date 클래스는 특정 시점을 날짜가 아닌 밀리초 단위로 표현한다 게다가 1900년을 기준으로 하는 오프셋, 0에서 시작하는 달 인덱스 등 모호한 설계로 유용성이 떨어졌다

```java
// 2024-08-06을 가르키는 인스턴스 생성
Date date = new Date(124, 8, 6);

// 출력 결과
Fri Sep 06 00:00:00 KST 2024

/*
	Date 클래스의 문제점 
	1. 결과가 직관적이지 않다 
	2. 시작 달 인덱스가 0부터 시작 (1월이 0임)
	3. Date 클래스의 toString으로 반환되는 문자열을 추가로 활용하기 어렵다
*/
```

Date 클래스의 문제점을 해결하고자 java.util.Calender 클래스가 대안으로 나왔지만 Calender 클래스 역시 쉽게 에러를 일으키는 문제를 가지고 있었고 Date와 Calender 두 가지 클래스가 등장하면서 개발자들에게 혼란이 가중되게 되었다

DateFormat과 같은 일부 기능은 Date 클래스에만 작동하였다 DateFormat에도 문제가 있었는데 그 문제는 바로 thread safe하지 않다는 것이다 즉 두 thread가 동시에 하나의 formater로 날짜를 파싱할 떄 예기치 못한 결과가 일어 날 수 있다 

Date와 Calender 모두 가변 객체로 값이 변경된다는 단점 또한 가지고 있다 이러한 단점을 보안 하기 위해 Java8 부터는 java.time 패키지를 추가하여 새로운 날짜와 시간 API를 제공하고 있다

### LocalDate, LocalTime 클래스

새로운 날짜와 시간 API를 사용할 때 처음 접하게 되는 것이 LocalDate이다 LocalDate 인스턴스는 시간을 제외한 날짜를 표현하는 **불변 객체**다 특히 LocalDate는 어떤 시간대 정보도 포함하지 않으며 정적 팩토리 메소드인 of 메소드를 통해 인스턴스를 만들 수 있다

```java
// LocalDate
LocalDate date = LocalDate.of(2024,8,8); // 2024-08-08
int year = date.getYear(); // 2024 
Month month = date.getMonth(); // AUGUST 
int day = date.getDayOfMonth(); // 8
DayOfWeek dow = date.getDayOfWeek(); // THURSDAY -> 무슨 요일인지
int len = date.lengthOfMonth(); // 31 -> 달의 마지막 날
boolean leap = date.isLeapYear(); // true -> 윤년인지 

// 현재 날짜도 얻어 올 수 있음
LocalDate today = LocalDate.now();

/*
	get() 메소드에 TemporalField를 전달해서 정보를 얻는 방법도 있으며 TemporalField는 
	시간 관련 객체에서 어떤 필드의 값에 접근할지 정의하는 인터페이스 이다
	ChronoFiled는 TemporalFiled 인터페이스를 정의하므로 ChronoFiled의 요소를 이용해서
	원하는 정보를 쉽게 얻을 수 있다 (ChronoFiled는 enum형임)
*/
int year = date.get(ChronoFiled.YEAR);
int month = date.get(ChronoFiled.MONTH_OF_YEAR);
...

// LocalTime
LocalTime time = LocalTime.of(13,30,39); // 13:30:39
int hour = time.getHour(); // 13시
int minute = time.getMinute(); // 301분
int second = time.getSecond(); // 39초

...
// LocalDate와 LocalTime을 문자열로 받아서도 사용할 수 있다
LocalDate date = LocalDate.parse("2024-08-08");
```

### LocalDateTime

LocalDateTime은 LocalDate와 LocalTime을 쌍으로 가진 복합 클래스로 날짜와 시간을 모두 표현할 수 있다

```java
// 2024-08-12T14:22:30
LocalDateTime dt = LocalDateTime.of(2024, Month.AUGUST, 12, 14, 22 ,30); 
LocalDateTime dtToDate = date.atTime(14, 22, 30);
LocalDateTime dtToTime = time.atDate(date);

/*
	atTime, atDate를 통해서 LocalDatTime을 만드는 방법도 존재하며 
	toLocalDate나 toLocalDate 메소드로 LocalTime과 LocalDate 인스턴스를 추출할 수 있다
*/
```

### Instant

사람은 보통 주 날짜 시간 분으로 날짜와 시간을 계산하지만 기계에서는 이와 같은 단위로 시간을 표시하기 어렵다 기계의 관점에서는 연속된 시간에서 특정 지점을하나의 큰 수로 표현하는 것이 가장 자연스러운 시간 표현 방법이다 Instant클래스는 유닉스 에포크 시간(1970년 1월 1일 0시 0분 0초 UTC)을 기준으로 특정 지점까지의 시간을 초로 표현한다

```java
Instant.ofEpochSecond(3);
Instant.ofEpochSecond(3, 1_000_000_000);
/*
	Instant 클래스는 나노초의 정밀도를제공하며 오버로드 된 ofEpochSecond의 메소드를 통해
	두 번째 인수를 이용해 나노초 단위로 시간을 보정할 수 있다
	
	Instant는 사람이 읽을 수 있는 시간 정보를 제공하지 않기 때문에 ChronoField와 
	같이 사용하게 되면 예외가 발생한다 
	Duration과 Period를 사용해서 인스턴스를 만들어야 한다
*/
```

### Duration & Period

지금까지 살펴본 클래스는 Temporal 인터페이스를 구현하는데 Temporal 인터페이스는 특정 시간을 모델링하는 객체의 값을 어떻게 읽고 조작할지 정의한다 Time 패키지에는 **Duration(지속)**와 **Period(기간)** 라는 상당히 비슷해 보이는 2개의 클래스가 있다. 이 두개의 클래스는 둘 다 시간의 길이를 나타내기 위해서 사용된다

- **Duration** : 두 시간의 간격 - A시간 - B시간
- **Period** : 두 날짜의 간격 - A날짜 - B날짜

```java
// Duration
Duration dr = Duration.between(Instant.ofEpochSecond(3, 1_000_000_000), Instant.now());
/*
	Duration은 두 시간 사이의 간격을 초나 나노 초 단위로 나타낸다 
	시작과 종료 시간 없이 Duration 클래스의 ofXXX() 정적 메서드를 사용하면 
	Duration을 생성할 수 있다
	
	LocalDateTime과 같이 사람이 사용하도록 만들어진 클래스를 사용하게 되면 예외가 발생한다
	LocalDateTime과 같은 날짜를 표현할 때는 Period 클래스를 사용해야 한다
*/

Period pr = Period.between(dt, LocalDate.now());

/*
	Period 클래스는 두 날짜 사이의 간격을 년/월/일 단위로 나타내고 
	두 날짜 사이의 간격을 알 수 있는 between()을 제공한다 
	시작 날짜와 종료 날짜를 나타내는 두 개의 LocalDate 객체를 인자로 받는다
	시작 날짜와 종료 날짜 없이 of메서드를 통해서 Period 객체를 생성할 수 있다
*/
```

### Duration & Period의 공통 메소드

| 메소드 | 정적 여부 | 설명 |
| --- | --- | --- |
| between | Y | 두 시간, 날짜 사이의 간격을 생성함 |
| from | Y | 시간 단위로 간격을 생성함 |
| of | Y | 주어진 구성 요소에서 간격 인스턴스를 생성함 |
| parse | Y | 문자열을 파싱해서 간격 인스턴스를 생성함 |
| addTo | N | 현재값의 복사본을 생성한 다음에 지정된 Temporal 객체에 추가함 |
| get | N | 현재 간격 정보값을 읽음 |
| isNegative | N | 간격이 음수인지 확인함 |
| isZero | N | 간격이 0인지 확인함 |
| minus | N | 현재 값에서 주어진 시간을 뺀 복사본을 생성함 |
| multipliedBy | N | 현재 값에서 주어진 값을 곱한 복사본을 생성함 |
| negated | N | 주어진 값의 부호를 반전한 복사본을 생성함 |
| plus | N | 현재 값에 주어진 시간을 더한 복사본을 생성함 |
| subtractFrom | N | 지정된 Temporal 객체에서 간격을 뺌 |

<aside>
💡 지금까지 모든 클래스는 불변이다 불변 클래스는 함수형 프로그래밍 그리고 스레드 안전성과 도메인 모델의 일관성을 유지하는 데 좋은 특징이다 하지만 새로운 날짜와 시간 API에서는 변경된 객체 버전을 만들 수 있는 메서드를 제공한다 예를 들어 기존 LocalDate 인스턴스에 3일을 더해야 하는 상황이 발생할 수 있다 이런 경우에는 어떻게 해야할까?

</aside>

### 날짜 조정

withXXX메소드를 통해서 기존의 LocalDate를 바꾼 버전을 직접 간단하게 만들 수 있다

```java
// 절대적인 방식으로 LocalDate의 속성 바꾸기
LocalDate date = LocalDate.of(2024,8,12); // 2024-08-12
LocalDate date2 = LocalDate.withYear(2025); // 2025-08-12
LocalDate date3 = LocalDate.withDayofMonth(25); // 2025-08-25;
LocalDate date4 = LocalDate.with(ChronoField.MONTH_OF_YEAR, 2); // 2025-02-25;

/*
	새롭게 할당해서 바꾼다 기존 date1의 값은 변하지 않는다 복사본을 만든다
	Instant에 ChronoField.MONTH_OF_YEAR 를 사용하거나 
	LocalDate에 ChronoField.NANO_OF_SECOND을 사용하면 예외가 발생한다
*/

// 상대적인 방식으로 LocalDate 속성 바꾸기
LocalDate date5 = date.plusWeeks(1); // 2024-08-19
LocalDate date6 = date5.minusYears(6); // 2025-08-19
LocalDate date7 = date6.plus(6, ChronoUnit.MONTHS); // 2026-02-19
```

### LocalDate, LocalTime, LocalDateTime, Instant 공통 메소드

| 메소드 | 정적 | 설명 |
| --- | --- | --- |
| from | Y | 주어진 Temporal 객체를 이용해서 클래스의 인스턴스를 생성함 |
| now | Y | 시스템 시계로 Temporal 객체를 생성함 |
| of | Y | 주어진 구성 요소에서 Temporal 객체의 인스턴스를 생성함 |
| parse | Y | 문자열을 파싱해서 Temporal 객체를 생성함 |
| atOffset | N | 시간대 오프셋과 Temporal 객체를 합침 |
| atZone | N | 시간대 오프셋과 Temporal 객체를 합침 |
| format | N | 지정된 포매터를 이용해서 Temporal 객체를 문자열로 변환함(Instant는 지원하지 않음) |
| get | N | Temporal 객체의 상태를 읽음 |
| minus | N | 특정 시간을 뺀 Temporal 객체의 복사본을 생성함 |
| plus | N | 특정 시간을 더한 Temporal 객체의 복사본을 생성함 |
| with | N | 일부 상태를 바꾼 Temporal 객체의 복사본을 생성함 |

### **TemporalAdjusters 사용하기**

지금까지 살펴본 날짜 조정 기능은 비교적 간단한 편에 속한다 하지만 다음 주 일요일, 돌아오는 평일, 어떤 달의 마지막 날 등 복잡한 조정기능이 필요하다면 조금은 위의 메서드로 구현하기 힘들 것이다 그래서 java에서는 TemporalAdjuster을 with에 전달하는 방식으로 문제를 해결하는데 도움을 준다

| 메소드 | 설명 |
| --- | --- |
| dayOfWeekInMonth | 이번 달의 n번째 요일 |
| firstDayOfMonth | 이번 달의 첫 날 |
| firstDayOfNextMonth | 다음 달의 첫 날 |
| firstDayOfYear | 올 해의 첫 날 |
| firstInMonth | 이번 달의 첫 번째 요일 |
| lastDayOfMonth | 이번 달의 마지막 날 |
| lastDayOfYear | 올 해의 마지막 날 |
| lastInMonth | 이번 달의 마지막 요일 |
| next | 다음 요일(당일 미포함) |
| previous | 다음 요일(당일 포함) |
| nextOrSame | 다음 요일(당일 포함) |
| previousOrSame | 지난 요일(당일 포함) |

### 날짜 파싱, 포메팅

포메팅과 파팅 전용 패키지인 java.time.format이 새로 추가 되었다 이 패키지에서 가장 중요한 클래스는 DateTimeFormatter다 정적 팩토리 메소드와 상수를 이용해서 손쉽게 포매터를 만들 수 있으며 BASIC_ISO_DATE와 ISO_LOCAL_DATE 등의 상수를 미리 정의하여 사용하도록 하고 있다

```java
LocalDate date = LocalDate.of(2024,8,12);

String format1 = date.format(DateTimeFormatter.BASIC_ISO_DATE); // 20240812
String format2 = date.format(DateTimeFormatter.ISO_LOCAL_DATE); // 2024-08-12

DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd/MM/yyyy");
String format3 = date.format(formatter); // 12/08/2024 내가 정의한 포매팅

String strDate = "20240812";
LocalDate date1 = LocalDate.parse(strDate,DateTimeFormatter.BASIC_ISO_DATE); // 2024-08-12

DateTimeFormatter koreaFormater = DateTimeFormatter.ofPattern("yy. MMMM. dd", Locale.KOREA);
String koreaFormat = date.format(koreaFormater); // 24. 8월. 12 지역정보 포함
LocalDate parse = LocalDate.parse(koreaFormat, koreaFormater); // 2024-08-12 원래대로 변환

/*
	DateTimeFormaterBuilder
	복합적인 포매터를 정의해서 좀 더 세부적으로 포매터를 제어할 수 있다 
	즉, DateTimeFormaterBuilder 클래스로 대소문자를 구분하는 파싱, 
	관대한 규칙을 적용하는 파싱(정해진 형식과 정확하게 일치하지 않는 입력을 해석할 수 있도록 체험적 방식의 파서 사용), 
	패딩, 포매터의 선택사항 등을 활용할 수 있다
*/

DateTimeFormatter builder = new DateTimeFormatterBuilder()
                .appendText(ChronoField.YEAR)
                .appendLiteral(". ")
                .appendText(ChronoField.MONTH_OF_YEAR)
                .appendLiteral(". ")
                .appendText(ChronoField.DAY_OF_MONTH)
                .parseCaseInsensitive()
                .toFormatter(Locale.KOREA);
```

## 시간대와 캘린더 다루기

지금까지 살펴본 모든 클래스에는 시간대와 관련한 정보가 없다 (예를 들자면 우리나라의 시간대는 도쿄를 기준으로 하고 있다) 새로운 날짜와 시간 API의 큰 편리함 중 하나는 시간대를 간단하게 처리할 수 있다는 점이다 기존 java.time.TimeZone을 대체할 수 있는 java.time.ZoneId  클래스가 새롭게 등장했다 새로운 클래스를 이용하면 서머타임Daylight saving Time(DST)과 같은 복잡한 사항이 자동으로 처리된다 날짜와 시간 API애서 제공하는 다른 클래스와 마찬가지로 ZoneId는 불변 클래스다

```java
/*
	표준 시간이 같은 지역을 묶어 시간대 규칙 집합을 정의한다
	ZoneRules 클래스에 시간대 집합이 존재하면 getRules를 이용하여 
	해당 시간대의 규정을 획득할 수 있다
	
	*시간대 정보 특징
		- {지역}/{도시} 형식으로 이루어진다
		- 시간대의 상대적인 시점을 표현한다
*/ 

ZoneId laZone = ZoneId.of("America/Los_Angeles");

LocalDate date = LocalDate.now(); // 2024-08-12
ZonedDateTime zonedDateTime = date.atStartOfDay(laZone); // 2024-08-12T00:00-07:00[America/Los_Angeles]
 
LocalDateTime localDateTime = LocalDateTime.now(); // 2024-08-12T15:42:51.585951200
ZonedDateTime zonedDateTime1 = localDateTime.atZone(laZone); // UTC 기준 시간대 -7시
//2024-08-12T15:51:19.969741300-07:00[America/Los_Angeles]

Instant instant = Instant.now(); // 2024-08-12T06:42:51.585951200Z
ZonedDateTime zonedDateTime2 = instant.atZone(laZone); // 시간이 표현 되며 여기서 -7시
// 2024-08-12T23:51:19.969741300-07:00[America/Los_Angeles]

// ZoneId를 이용해서 LocalDateTime을 Instant로 변환
Instant instant = Instant.now(); // 2024-08-12T07:01:32.813566300Z
LocalDateTime localDateTime = LocalDateTime.ofInstant(instant, ZoneId.of("Asia/Tokyo"));
System.out.println(localDateTime); // 2024-08-12T16:01:32.813566300
Instant toInstant = localDateTime.toInstant(ZoneOffset.UTC); // 2024-08-12T16:01:32.813566300Z
```

**결론**

- 자바 8이전의 java.util.Date는 가변객체, 오프셋 문제, 달의 인덱스문제, 잘못된 결정 등의 설계 결함이 존재한다
- 새로운 날짜 API의 경우 모두 불변 객체이다
- 새로운 API의 경우 기계와 사람이 이해할 수 있는 두가지의 상황을 모두 제공한다 (Instant, LocalDateTime)
- 날짜와 시간 객체는 상대적으로도 처리할 수 있으며 절대적인 방식으로 처리하여 변경할 수 있다 하지만 이 변경의 결과는 기존의 인스턴스를 바꾸는 것이 아닌 결과로 새로운 인스턴스를 생성한다
- TemporalAdjuster은 복잡한 동작도 수행할 수 있다
- 날짜와 시간 객체를 특정 포맷으로 출력하고 파싱하는 포매터를 정의할 수 있다 스레드 안전성을 보장한다 (자바 8 이전의 format은 thread safe하지 못했다)
- 특정지역/장소에 상대적인 시간대 또는 UTC/GMT 기준의 오프셋을 이용해서 시간대를 정의할 수 있다 (ZoneId)
