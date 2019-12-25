---
title: Java8的时间类
date: 2019-03-17 11:28:59
categories:
- 技术
- JDK
- API
tags:
- JDK8
- API
---

### 首先看官方文档怎么说

 关于时间的API在java.base模块下（modules）的java.time包里。
- - -  
Package java.time
The main API for dates, times, instants, and durations.

主要针对dates(日期),times(时间),instants(瞬时),durations(间隔)的API。

<!--more-->

The classes defined here represent the principle date-time concepts, including instants, durations, dates, times, time-zones and periods. They are based on the ISO calendar system, which is the de facto world calendar following the proleptic Gregorian rules. All the classes are immutable and thread-safe.

这里的类表现了date-time概念的原则，其中包括dates(日期),times(时间),instants(瞬时),durations(间隔),time—zone(时区),periods(周期)。并且都基于iso国际时间标准，所有的类都是不可变的、线程安全的。


Each date time instance is composed of fields that are conveniently made available by the APIs. For lower level access to the fields refer to the java.time.temporal package. Each class includes support for printing and parsing all manner of dates and times. Refer to the java.time.format package for customization options.

每一个date time的实列都由一些能够通过APIs非常方便访问的域(field)构成，对于这些域更第一级别的入口定义在包java.time.temporal里面。每一个类都之称各种格式的dates和times，在
 java.time.format包中定义了供使用者自由组织的选项。
 
The java.time.chrono package contains the calendar neutral API ChronoLocalDate, ChronoLocalDateTime, ChronoZonedDateTime and Era. This is intended for use by applications that need to use localized calendars. It is recommended that applications use the ISO-8601 date and time classes from this package across system boundaries, such as to the database or across the network. The calendar neutral API should be reserved for interactions with users.

java.time.chrono 定义了一些中性的API，这些API都是应用将要通过本地的日历使用的，它在应用跨系统范围外例如连接网络，访问数据库时，推荐使用本包中的ISO-8601的date和time类，中性化的api应该留给用户

Dates and Times
Instant is essentially a numeric timestamp. The current Instant can be retrieved from a Clock. This is useful for logging and persistence of a point in time and has in the past been associated with storing the result from System.currentTimeMillis().

Dates和Times的Instant本质上都是数字化的时间戳，当前的实列可以从Clock对象中获取。这对记录和持久化一个时间点非常有用并且与存储 System.currentTimeMillis() 的结果相关联。

LocalDate stores a date without a time. This stores a date like '2010-12-03' and could be used to store a birthday.

LocalDate 存储了日期不包括时间，他存储了一个类似于'2010-12-03'的日期，可用与记录生日

LocalTime stores a time without a date. This stores a time like '11:30' and could be used to store an opening or closing time.

LocalTime 存储了一个时间不包括日期。他存储了一个类似于'11:30'的时间，可用于记录开放或者关闭时间。

LocalDateTime stores a date and time. This stores a date-time like '2010-12-03T11:30'.

LocalDateTime 存储了 时间和日期，例如：'2010-12-03T11:30'.

ZonedDateTime stores a date and time with a time-zone. This is useful if you want to perform accurate calculations of dates and times taking into account the ZoneId, such as 'Europe/Paris'. Where possible, it is recommended to use a simpler class without a time-zone. The widespread use of time-zones tends to add considerable complexity to an application.

ZonedDateTime 存储了包含时区的日期和时间，这非常有用，如果你想要通过ZoneId对日期和时间的计算有精确的操作。例如“Europe/Paris”，如果有可能，推荐使用不包含时区的更简单的类。大范围的使用时区，将增大应用的复杂程度。

Duration and Period
Beyond dates and times, the API also allows the storage of periods and durations of time. A Duration is a simple measure of time along the time-line in nanoseconds. A Period expresses an amount of time in units meaningful to humans, such as years or days.

Duration和Period 比dates和times更高阶，这个api允许存储一个周期或者一段时间。Duration 是在时间线上以纳秒衡量时间的简单方式。Period是一种对人类有意义的时间量，例如，年，天。

Additional value types
额外的数据值的类型
Month stores a month on its own. This stores a single month-of-year in isolation, such as 'DECEMBER'.

Month在他内部存储了一个月，他单独存储了一年中的某一个月，例如“DECEMBER”

DayOfWeek stores a day-of-week on its own. This stores a single day-of-week in isolation, such as 'TUESDAY'.

DayOfWeek 存储了 每周的天数，他单独隔离的存储了一个周的某个天，例如“TUESDAY”

Year stores a year on its own. This stores a single year in isolation, such as '2010'.

Year 存储了 一年，他单独隔离的的存储了一年，例如“2010”.

YearMonth stores a year and month without a day or time. This stores a year and month, such as '2010-12' and could be used for a credit card expiry.

YearMonth 存储了年和月 不包括天和时间，他存储的年和月是如下形式：“2010-12” 可以用来做信用卡过期。

MonthDay stores a month and day without a year or time. This stores a month and day-of-month, such as '--12-03' and could be used to store an annual event like a birthday without storing the year.

MonthDay 存储了 月份和天，不包含年和时间，例如："--13-03" ,可以用来存储周年的时间，例如存储 生日，不用存储年份。

OffsetTime stores a time and offset from UTC without a date. This stores a date like '11:30+01:00'. The ZoneOffset is of the form '+01:00'.

偏移时间存储了一个时间和一个根据UTC的偏移量，不包含date，例如“11:30+01:00”，其中ZoneOffset是“+01:00”的形式。

OffsetDateTime stores a date and time and offset from UTC. This stores a date-time like '2010-12-03T11:30+01:00'. This is sometimes found in XML messages and other forms of persistence, but contains less information than a full time-zone.

便宜日期时间存储了一个日期和时间 和更具UTC的偏移量，例如：“2010-12-03T11:30+01:00”，他有时会出现在XML信息中或者其他持久化的格式中，但相比完整的时区（time-zone）包含了更少的信息

Package specification
包的规范/说明
Unless otherwise noted, passing a null argument to a constructor or method in any class or interface in this package will cause a NullPointerException to be thrown. The Javadoc "@param" definition is used to summarise the null-behavior. The "@throws NullPointerException" is not explicitly documented in each method.
除非另外说明：向本包的类和接口的构造函数和方法传入null值会抛出“NullPointException”,JavaDoc中的“@param”的定义给null的表现形式（null-beHavior）做了总结；而“@throws NullPointerException”并不会明显的在每个方法中指明。

All calculations should check for numeric overflow and throw either an ArithmeticException or a DateTimeException.

所有的计算都应该检出是否 数字溢出？（numeric overflow）并且抛出ArithmeticException或者DateTimeException

Design notes (non normative)

设计提示（非规范的）

The API has been designed to reject null early and to be clear about this behavior. A key exception is any method that takes an object and returns a boolean, for the purpose of checking or validating, will generally return false for null.

API被设计为拒绝null，并且这种行为是非常明确的，但是一个关键的例外是所有的方法--接受一个对象返回一个布尔类型，处于验证和检查的目的，通常会返回一个null或者false

The API is designed to be type-safe where reasonable in the main high-level API. Thus, there are separate classes for the distinct concepts of date, time and date-time, plus variants for offset and time-zone. This can seem like a lot of classes, but most applications can begin with just five date/time types.

API被设计为类型安全的--在主要高阶API中是合理的。因而，对于date, time and date-time, plus variants for offset, time-zone分别有不同的类对应他们清晰的概念。

对API的描述：

Instant - a timestamp

LocalDate - a date without a time, or any reference to an offset or time-zone

LocalTime - a time without a date, or any reference to an offset or time-zone

LocalDateTime - combines date and time, but still without any offset or time-zone

ZonedDateTime - a "full" date-time with time-zone and resolved offset from UTC/Greenwich

Instant is the closest equivalent class to java.util.Date.

Instant是与java.util.Date类最接近相等的类。

ZonedDateTime is the closest equivalent class to java.util.GregorianCalendar.

ZoneDateTime 是与java.util.GregorianCalendar最接近的类。

Where possible, applications should use LocalDate, LocalTime and LocalDateTime to better model the domain. For example, a birthday should be stored in a code LocalDate. Bear in mind that any use of a time-zone, such as 'Europe/Paris', adds considerable complexity to a calculation. Many applications can be written only using LocalDate, LocalTime and Instant, with the time-zone added at the user interface (UI) layer.

尽可能的使用LocalDate, LocalTime and LocalDateTime进行模型化，例如，生日应该被存储在LocalDate中，牢记：任何对时区的使用，例如“Europe/Paris”都会增加计算的复杂度。许多应用可以只使用LocalDate, LocalTime and Instant--他们使用用户层面的时区。

The offset-based date-time types OffsetTime and OffsetDateTime, are intended primarily for use with network protocols and database access. For example, most databases cannot automatically store a time-zone like 'Europe/Paris', but they can store an offset like '+02:00'.

与偏移量相关的类主要用于数据库和网络协议，例如大部分数据库不能存储时区用“Europr/Paris”格式，但是可以存储偏移量，例如“+02:00”

Classes are also provided for the most important sub-parts of a date, including Month, DayOfWeek, Year, YearMonth and MonthDay. These can be used to model more complex date-time concepts. For example, YearMonth is useful for representing a credit card expiry

对于日期的最重要的分支部分也有一些类做了支持，包括：Month, DayOfWeek, Year, YearMonth , MonthDay。这些类可以很好的对复杂的时间概念进行描述。例如：YearMonth可以用来对信用卡的过期时间进行描述。


Note that while there are a large number of classes representing different aspects of dates, there are relatively few dealing with different aspects of time. Following type-safety to its logical conclusion would have resulted in classes for hour-minute, hour-minute-second and hour-minute-second-nanosecond. While logically pure, this was not a practical option as it would have almost tripled the number of classes due to the combinations of date and time. Thus, LocalTime is used for all precisions of time, with zeroes used to imply lower precision.

注意：虽然有大量的类来描述日期的各个方面，但是对于时间的各个方面的描述却比较少，按照类型安全的划分，将会导致大量的类描述hour-minute, hour-minute-second, hour-minute-second-nanosecond,虽然逻辑上是纯粹的，但是却不是一个实际的选择，因为日期和时间的组合会导致类成倍的增加，因此，使用LocalTime表示所有精度的时间，对于更低精度基本不用。

Following full type-safety to its ultimate conclusion might also argue for a separate class for each field in date-time, such as a class for HourOfDay and another for DayOfMonth. This approach was tried, but was excessively complicated in the Java language, lacking usability. A similar problem occurs with periods. There is a case for a separate class for each period unit, such as a type for Years and a type for Minutes. However, this yields a lot of classes and a problem of type conversion. Thus, the set of date-time types provided is a compromise between purity and practicality.

根据类型安全的概念，在对date-time不同域分离不同类上产生了争论。例如一个类用于HourOfDay 另一个用于DayOfMonth,这种方式是被尝试过的，但是对java这种语言过于复杂，缺少实用性。同样的问题也出现在period，针对每一个时间周期都设置一个分离的类。例如Years,Minutes,然而这将产生大量的类，和类型转换的问题。因此 date-time是一种实用性和纯粹性相互妥协的产物。

The API has a relatively large surface area in terms of number of methods. This is made manageable through the use of consistent method prefixes.

API的方法之间存在着联系。通过一致的方法名的前缀进行组织。

of - static factory method

of - 静态的工厂方法

parse - static factory method focussed on parsing

parse - 静态的工厂方法，用于解析

get - gets the value of something

get - 获取某些值

is - checks if something is true

is - 判断真假

with - the immutable equivalent of a setter

with - 于setter的语义一致，不可变

plus - adds an amount to an object

plus - 加法

minus - subtracts an amount from an object

minus - 减法

to - converts this object to another type

to - 类型转换

at - combines this object with another, such as date.atTime(time)

at - 把当前的对象与其他合并。

Multiple calendar systems is an awkward addition to the design challenges. The first principle is that most users want the standard ISO calendar system. As such, the main classes are ISO-only. The second principle is that most of those that want a non-ISO calendar system want it for user interaction, thus it is a UI localization issue. As such, date and time objects should be held as ISO objects in the data model and persistent storage, only being converted to and from a local calendar for display. The calendar system would be stored separately in the user preferences.

多种日历系统是尴尬的额外挑战，第一原则是 大部分用户想要使用ISO，所以主要的api都是只支持iso的。第二个原则是 针对那些想使用非iso的用于进行用户的交互，所以真是一个用户本地化的事情，因而，所有的队形数据模型和持久化都是iso的，只有展示的时候才转化为其他格式。用户根据喜好选择日历系统。

There are, however, some limited use cases where users believe they need to store and use dates in arbitrary calendar systems throughout the application. This is supported by ChronoLocalDate, however it is vital to read all the associated warnings in the Javadoc of that interface before using it. In summary, applications that require general interoperation between multiple calendar systems typically need to be written in a very different way to those only using the ISO calendar, thus most applications should just use ISO and avoid ChronoLocalDate.

然而有少数的用户认为他们需要存储和使用任意的日历系统，这在ChronoLocalDate接口中是支持的，但是 在使用之前详细阅读文档是非常必须的（vital）。总结来说，使用多种日历系统的项目写法和ISO是非常不同的，因此大部分应用应该使用ISO而避免使用ChronoLocalDate。

The API is also designed for user extensibility, as there are many ways of calculating time. The field and unit API, accessed via TemporalAccessor and Temporal provide considerable flexibility to applications. In addition, the TemporalQuery and TemporalAdjuster interfaces provide day-to-day power, allowing code to read close to business requirements:

API也为用户的扩展进行了设计，例如有许多方式进行时间的计算，考虑到应用的弹性TemporalAccessor and Temporal 提供了获取域和unit api的入口，TemporalQuery and TemporalAdjuster提供了天为单位的计算方式。

   LocalDate customerBirthday = customer.loadBirthdayFromDatabase();
   LocalDate today = LocalDate.now();
   if (customerBirthday.equals(today)) {
     LocalDate specialOfferExpiryDate = today.plusWeeks(2).with(next(FRIDAY));
     customer.sendBirthdaySpecialOffer(specialOfferExpiryDate);
   }
   
###    测试用例试一下

```
@Test
    public void test() throws InterruptedException {
        //A clock providing access to the current instant, date and time using a time-zone.
        //提供了对某一时区，当前的瞬时，日期，时间的入口
        Clock clock = Clock.systemDefaultZone();
        int i =0;
        Instant instant = clock.instant();
        /*while (i++<60){
            System.out.println( clock.instant());
            Thread.sleep(10);
        }*/
        //temporal 是时间间隔类们的父类。
        //Duration 获取一段时间的间隔
        System.out.println(Duration.between(instant,clock.instant()));

        DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");

        /**
         * it is a description of the date, as used for birthdays, combined with
         *  * the local time as seen on a wall clock.
         *
         *  不带时区，跟随当地时间
         *
         * */
        LocalDateTime localDateTime = LocalDateTime.of(2019, 12, 22, 12, 52);
        //String format = localDateTime.format(dateTimeFormatter);
        //System.out.println(format);
        ZonedDateTime zonedDateTime = localDateTime.atZone(ZoneId.of("Asia/Shanghai"));
        //ZonedDateTime zonedDateTime1 = localDateTime.atZone(ZoneId.of("America/Phoenix"));
        //System.out.println(zonedDateTime);
        System.out.println(zonedDateTime);
        ZonedDateTime zonedDateTime2 = zonedDateTime.withZoneSameInstant(ZoneId.of("America/Phoenix"));
        System.out.println(zonedDateTime2);
        System.out.println(zonedDateTime2.format(dateTimeFormatter));
        System.out.println(zonedDateTime.format(dateTimeFormatter));

    }

 ```
### note
1.  官方文档对设计的基本概念进行了阐述 
      - 遵循iso
      - 面向对象
2. 对于其中几个问题 
      - Instant 比其他类的抽象程度高
      - Instant/LocalDate*/ZoneTime的关系。ps:进行时区转换需要用到ZoneTime；Local系列不保存时区。
