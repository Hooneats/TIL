# 5. Date 와 Time

## 5-1 Date 와 Time 소개
기존의 Date 클래스는 mutable 해서 Thread safe 하지 않았다.
또한 클래스 이름이 명확하지 않다.(Date 인데 시간까지 다룬다.)
또한 Date 의 시간은 EPOCK (기계용 시간) 을 다룬다.
( EPOCK 은 1970년 1월 1일 0시 0분 0초 부터 현재까지의 타임 스탬프를 표현한다. ) 
또한 버그 발생 여지가 많다.(월이 0부터 시작한다거나... 해서 타입 안정성이 없다.)

### 자바 8 이후 부터는
특정 날짜(LocalDate), 시간(LocalTime), 일시(LocalDateTime) 을 사용할 수 있다.
기간을 표현하는 Duration(시간 기반) 과 Period(날짜 기반) 을 사용할 수 있다.
DateTimeFormatter 를 사용해서 일시를 특정한 문자욜로 포매팅할 수 있다.

---

## 5-2 Date 와 Time API
### java8 의 시간 관련 Instant , LocalDate 등등 은 Immutable 하다는것을 기억하자!
```java
public class App4 {
    public static void main(String[] args) {
        /**
         * 기계용 시간
         * */
        // 기계적인 시간 Date 와 비슷 (기계용) 기준시는 UTC , GMT
        Instant instantNow = Instant.now();
        // 특정 EPOCK 을 기준으로 생성
        Instant instantOf = Instant.ofEpochMilli(new Date().getTime());
        Instant instantOf2 = Instant.ofEpochMilli(Instant.now().toEpochMilli());
        System.out.println("instantNow = " + instantNow);
        System.out.println("instantOf = " + instantOf);
        System.out.println("instantOf2 = " + instantOf2);
        // 기준시를 현재 로컬로 변경
        ZoneId zone = ZoneId.systemDefault();
        ZonedDateTime zonedDateTime = instantNow.atZone(zone);
        System.out.println("zone = " + zone);
        System.out.println("zonedDateTime = " + zonedDateTime);
        ZonedDateTime asia = instantNow.atZone(ZoneId.of("Asia/Seoul"));
        System.out.println("asia = " + asia);


        /**
         * 사람용 시간
         * */
        // 기준은 서버가 존재하는곳의 Local
        LocalDateTime now = LocalDateTime.now();
        System.out.println("now = " + now);
        LocalDateTime birthday = LocalDateTime.of(1994, Month.OCTOBER, 21, 0, 0, 0);
        System.out.println("birthday = " + birthday);
        // 특정 도시 시간 보기
        ZonedDateTime utcTime = ZonedDateTime.now(ZoneId.of("UTC"));
        System.out.println("utcTime = " + utcTime);
        ZonedDateTime instantUtcTime = Instant.now().atZone(ZoneId.of("UTC"));
        System.out.println("instantUtcTime = " + instantUtcTime);
        // 기간을 표현하는 방법 Period(주로 사람용)
        LocalDate today = LocalDate.now();
        LocalDate myBirthday = LocalDate.of(1994, Month.OCTOBER, 21);
        Period period1 = Period.between(today, myBirthday);
        System.out.println("period1.getDays() = " + period1.getDays());
        // 오늘부터 ~ 까지
        Period period2 = today.until(myBirthday);
        System.out.println("period2.getDays() = " + period2.getDays());
        System.out.println("period2.getDays() = " + period2.get(ChronoUnit.DAYS));
        // 날짜 연산
        LocalDateTime now3 = LocalDateTime.now();
        LocalDateTime plus1 = now3.plus(10, ChronoUnit.DAYS);
        System.out.println("plus1 = " + plus1);

        // 기간을 표현하는 방법 Duration(주로 기계용)
        Instant now1 = Instant.now();
        Instant plus = now1.plus(10, ChronoUnit.SECONDS);
        Duration between = Duration.between(now1, plus);
        System.out.println("between.getSeconds() = " + between.getSeconds());

        /**
         * 포매팅
         * */
        LocalDateTime now2 = LocalDateTime.now();
        System.out.println("now2 = " + now2);
        DateTimeFormatter MMddyyyy = DateTimeFormatter.ofPattern("MM/dd/yyyy"); // 이렇게 커스텀해도되고 자바에서 static 으로 제공하는 것도 있다.
        System.out.println(now2.format(MMddyyyy));
        // 포맷 역으로 parsing
        LocalDate parse = LocalDate.parse("10/21/1994", MMddyyyy);
        System.out.println("parse = " + parse);

        /**
         * 레거시 API 와의 호환
         * */
        // 레거시 Date , 자바8 Instant
        // 레거시 -> 뉴
        Date date = new Date();
        Instant instant = date.toInstant();
        System.out.println("instant = " + instant);
        // 뉴 -> 레거시
        Date from = Date.from(instant);
        System.out.println("from = " + from);
        
        // 레거시 -> 뉴
        GregorianCalendar gregorianCalendar = new GregorianCalendar();
        LocalDateTime localDateTime = gregorianCalendar.toInstant().atZone(ZoneId.systemDefault()).toLocalDateTime();
        System.out.println("localDateTime = " + localDateTime);
        // 뉴 -> 레거시
        GregorianCalendar from1 = GregorianCalendar.from(ZonedDateTime.now(ZoneId.systemDefault()));
        System.out.println("from1 = " + from1.getTime());
        
        // 레거시 -> 뉴
        ZoneId zoneId = TimeZone.getTimeZone("PST").toZoneId();
        // 뉴 -> 레거시
        TimeZone timeZone = TimeZone.getTimeZone(zoneId);
        System.out.println("zoneId = " + zoneId);
        System.out.println("timeZone = " + timeZone.getID());
    }
}

```



