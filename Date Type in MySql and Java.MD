# 在Java和MySql中如何正确使用Date类型
## MySql 5 支持的日期时间类型:

| Data Type | Length | Format | Min Value | Max Value | Zero Value |
|--------|--------|--------|--------|--------|--------|
| DATE | 4 bytes | 'YYYY-MM-DD' | '1000-01-01' | '9999-12-31' | '0000-00-00' |
| TIME | 3 bytes | 'HH:MM:SS' | '-838:59:59' | '838:59:59' | '00:00:00' |
| YEAR | 1 bytes | 'YYYY' | '1901' | '2155' | '0000' |
| DATETIME | 8 bytes | 'YYYY-MM-DD HH:MM:SS[.fraction]' | '1000-01-01 00:00:00.000000' | '9999-12-31 23:59:59.999999' | '0000-00-00 00:00:00' |
| TIMESTAMP | 4 bytes | 'YYYY-MM-DD HH:MM:SS[.fraction]' | '1970-01-01 00:00:01.000000' | '2038-01-19 03:14:07.999999' | '0000-00-00 00:00:00' |

- **DATETIME**
DATETIME 用于表示 年月日 时分秒，是 DATE 和 TIME 的组合，并且记录的年份（见上表）比较长久。如果实际应用中有这样的需求，就可以使用 DATETIME 类型
- **TIMESTAMP**
TIMESTAMP 用于表示 年月日 时分秒，但是记录的年份（见上表）比较短暂。
TIMESTAMP 和时区相关，更能反映当前时间。当插入日期时，会先转换为本地时区后再存放；当查询日期时，会将日期转换为本地时区后再显示。所以不同时区的人看到的同一时间是  不一样的。
表中的第一个 TIMESTAMP 列自动设置为系统时间（CURRENT_TIMESTAMP）。当插入或更新一行，但没有明确给 TIMESTAMP 列赋值，也会自动设置为当前系统时间。如果表中有第二个 TIMESTAMP 列，则默认值设置为0000-00-00 00:00:00。
TIMESTAMP 的属性受 Mysql 版本和服务器 SQLMode 的影响较大。
如果记录的日期需要让不同时区的人使用，最好使用 TIMESTAMP。
- **DATE**
DATE 用于表示 年月日，如果实际应用值需要保存 年月日 就可以使用 DATE。
- **TIME**
TIME 用于表示 时分秒，如果实际应用值需要保存 时分秒 就可以使用 TIME。
- **YEAR**
YEAR 用于表示 年份，YEAR 有 2 位（最好使用4位）和 4 位格式的年。 默认是4位。如果实际应用只保存年份，那么用 1 bytes 保存 YEAR 类型完全可以。不但能够节约存储空间，还能提高表的操作效率。

## Java中日期的使用
### java.util.Date
从 JDK 1.1 开始，应该使用 Calendar 类实现日期和时间字段之间转换，使用 DateFormat 类来格式化和分析日期字符串。Date 中的把日期解释为年、月、日、小时、分钟和秒值的方法已废弃。
构造器方法之一: `Date(long date)` date - the milliseconds since January 1, 1970, 00:00:00 GMT.
### java.text.DateFormat
DateFormat 是日期/时间格式化子类的抽象类，它以与语言无关的方式格式化并分析日期或时间。日期/时间格式化子类（如 SimpleDateFormat）允许进行格式化（也就是日期 -> 文本）、分析（文本-> 日期）和标准化。将日期表示为 Date 对象，或者表示为从 GMT（格林尼治标准时间）1970 年，1 月 1 日 00:00:00 这一刻开始的毫秒数。
要格式化不同语言环境的日期，可在 getDateInstance() 的调用中指定它。
`DateFormat df = DateFormat.getDateInstance(DateFormat.LONG, Locale.CHINA);`
常见格式化风格包括FULL、LONG、MEDIUM 和 SHORT.
- SHORT 完全为数字，如 12.13.52 或 3:30pm
- MEDIUM 较长，如 Jan 12, 1952
- LONG 更长，如 January 12, 1952 或 3:30:32pm
- FULL 是完全指定，如 Tuesday, April 12, 1952 AD 或 3:30:42pm PST
注意, DataFormat不是线程安全的.

### java.text.SimpleDateFormat
SimpleDateFormat 是一个以与语言环境相关的方式来格式化和分析日期的具体类。它允许进行格式化（日期 -> 文本）、分析（文本 -> 日期）和规范化。
SimpleDateFormat 使得可以选择任何用户定义的日期-时间格式的模式。但是，仍然建议通过 DateFormat 中的 getTimeInstance、getDateInstance 或 getDateTimeInstance 来新的创建日期-时间格式化程序。
### java.util.Calendar
Calendar 类是一个抽象类，它为特定瞬间与一组诸如 YEAR、MONTH、DAY_OF_MONTH、HOUR 等 日历字段之间的转换提供了一些方法，并为操作日历字段（例如获得下星期的日期）提供了一些方法。瞬间可用毫秒值来表示，它是距历元（即格林威治标准时间 1970 年 1 月 1 日的 00:00:00.000，格里高利历）的偏移量。
与其他语言环境敏感类一样，Calendar 提供了一个类方法 getInstance，以获得此类型的一个通用的对象。Calendar 的 getInstance 方法返回一个 Calendar 对象，其日历字段已由当前日期和时间初始化。
Calendar的使用一般也是通过`Calendar.getInstance()`来获取实例.
Calendar中的几个注意事项：
- Calendar的星期是从周日开始的，常量值为0。
- Calendar的月份是从一月开始的，常量值为0。
- Calendar的每个月的第一天值为1。

### java.util.GregorianCalendar
GregorianCalendar 是 Calendar 的一个具体子类，提供了世界上大多数国家使用的标准日历系统。
GregorianCalendar 是一种混合日历，在单一间断性的支持下同时支持儒略历和格里高利历系统，在默认情况下，它对应格里高利日历创立时的格里高利历日期（某些国家是在 1582 年 10 月 15 日创立，在其他国家要晚一些）。可由调用方通过调用 setGregorianChange() 来更改起始日期。
