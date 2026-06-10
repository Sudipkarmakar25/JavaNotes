# Java 8 Date and Time API

## 1. Why New Date API Was Needed

### Problems with Old Date API
- `java.util.Date` was mutable.
- `Calendar` API was verbose and hard to read.
- Month indexing and date manipulation were confusing.
- Poor timezone handling.
- Formatting/parsing APIs were not very clean.
- Older APIs were harder to use safely in multi-threaded code.

### Interview Note
Java 8 introduced `java.time` to provide **immutable**, **clear**, and **thread-safe** date-time classes.

---

## 2. Core Classes Overview

| Class | Purpose | Example Use |
|---|---|---|
| `LocalDate` | Date only | birth date, holiday, invoice date |
| `LocalTime` | Time only | store opening time |
| `LocalDateTime` | Date + time without timezone | created timestamp in local system |
| `ZonedDateTime` | Date + time + timezone | global applications |
| `Period` | Difference in years/months/days | age, subscription gap |
| `Duration` | Difference in hours/minutes/seconds | job runtime, API timeout |

---

## 3. `LocalDate`

### Purpose
Represents a date without time and timezone.

### Common Methods
- `now()`
- `of(year, month, day)`
- `plusDays()` / `minusDays()`
- `getYear()` / `getMonth()` / `getDayOfMonth()`
- `isBefore()` / `isAfter()`

### Small Example
```java
class Demo {
    void test() {
        LocalDate today = LocalDate.now();
        LocalDate custom = LocalDate.of(2026, 5, 29);
        LocalDate nextWeek = today.plusDays(7);
    }
}
```

### Interview Note
- Use `LocalDate` when time is not needed.
- Common in forms, billing dates, and DOB fields.

---

## 4. `LocalTime`

### Purpose
Represents time without date and timezone.

### Common Methods
- `now()`
- `of(hour, minute, second)`
- `plusHours()` / `minusMinutes()`
- `getHour()` / `getMinute()`

### Small Example
```java
class Demo {
    void test() {
        LocalTime start = LocalTime.of(9, 30);
        LocalTime end = start.plusHours(2);
    }
}
```

### Interview Note
- Good for business timings like office hours or market open/close time.

---

## 5. `LocalDateTime`

### Purpose
Represents date and time without timezone.

### Common Methods
- `now()`
- `of(...)`
- `plusDays()` / `minusHours()`
- `toLocalDate()` / `toLocalTime()`

### Small Example
```java
class Demo {
    void test() {
        LocalDateTime createdAt = LocalDateTime.now();
        LocalDateTime meeting = LocalDateTime.of(2026, 5, 29, 10, 30);
    }
}
```

### Interview Note
- Suitable when timezone is not part of the business requirement.
- Do not use it for globally distributed time calculations.

---

## 6. `ZonedDateTime`

### Purpose
Represents date, time, and timezone together.

### Common Methods
- `now()`
- `now(ZoneId.of(...))`
- `withZoneSameInstant()`
- `getZone()`

### Small Example
```java
class Demo {
    void test() {
        ZonedDateTime indiaTime = ZonedDateTime.now(ZoneId.of("Asia/Kolkata"));
        ZonedDateTime usTime = indiaTime.withZoneSameInstant(ZoneId.of("America/New_York"));
    }
}
```

### Interview Note
- Use `ZonedDateTime` when timezone matters.
- Important for international apps, scheduling, and distributed systems.

---

## 7. `Period`

### Purpose
Represents a date-based amount of time in years, months, and days.

### Common Methods
- `between(start, end)`
- `ofDays()` / `ofMonths()` / `ofYears()`
- `getYears()` / `getMonths()` / `getDays()`

### Small Example
```java
class Demo {
    void test() {
        LocalDate start = LocalDate.of(2020, 1, 1);
        LocalDate end = LocalDate.of(2026, 5, 29);
        Period period = Period.between(start, end);
    }
}
```

### Interview Note
- `Period` is for **date-based** difference.
- Common question: use `Period` for age, not `Duration`.

---

## 8. `Duration`

### Purpose
Represents a time-based amount in seconds, minutes, hours, and nanos.

### Common Methods
- `between(start, end)`
- `ofHours()` / `ofMinutes()`
- `toHours()` / `toMinutes()` / `getSeconds()`

### Small Example
```java
class Demo {
    void test() {
        LocalTime start = LocalTime.of(10, 0);
        LocalTime end = LocalTime.of(12, 30);
        Duration duration = Duration.between(start, end);
    }
}
```

### Interview Note
- `Duration` is for **time-based** difference.
- Good for execution time, timeout, and session expiry logic.

---

## 9. Date Formatting and Parsing

### Purpose
Used to convert date-time objects to strings and parse strings into date-time objects.

### Common Methods
- `DateTimeFormatter.ofPattern(...)`
- `format(formatter)`
- `LocalDate.parse(text, formatter)`
- `LocalDateTime.parse(text, formatter)`

### Small Example
```java
class Demo {
    void test() {
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd-MM-yyyy");

        LocalDate date = LocalDate.of(2026, 5, 29);
        String formatted = date.format(formatter);

        LocalDate parsed = LocalDate.parse("29-05-2026", formatter);
    }
}
```

### Interview Note
- Prefer `DateTimeFormatter` over old formatting APIs.
- `DateTimeFormatter` is thread-safe.

---

## 10. Common Java 8 Date API Interview Questions

### 1. Why was the old Date API replaced?
Because it was mutable, confusing, verbose, and harder to use correctly.

### 2. Difference between `LocalDate` and `LocalDateTime`?
`LocalDate` stores only date; `LocalDateTime` stores date and time.

### 3. Difference between `LocalDateTime` and `ZonedDateTime`?
`LocalDateTime` has no timezone; `ZonedDateTime` includes timezone.

### 4. When do we use `Period`?
For date-based differences like age or months between two dates.

### 5. When do we use `Duration`?
For time-based differences like minutes, hours, or seconds.

### 6. Is Java 8 Date-Time API mutable?
No, main `java.time` classes are immutable.

### 7. Why is immutability useful here?
It makes date-time handling safer and easier in multi-threaded applications.

### 8. Which class is best for timezone handling?
`ZonedDateTime`.

### 9. Which formatter is used in Java 8?
`DateTimeFormatter`.

### 10. Is `DateTimeFormatter` thread-safe?
Yes.

---

## 11. Quick Revision Table

| Topic | Remember |
|---|---|
| Old API issues | mutable, verbose, confusing, weak timezone support |
| `LocalDate` | date only |
| `LocalTime` | time only |
| `LocalDateTime` | date + time, no timezone |
| `ZonedDateTime` | date + time + timezone |
| `Period` | years / months / days |
| `Duration` | hours / minutes / seconds |
| Formatting | `DateTimeFormatter` |
| Parsing | `LocalDate.parse(...)`, `LocalDateTime.parse(...)` |

---

## 12. Final Takeaways

- Prefer `java.time` over old `Date` and `Calendar` APIs.
- Choose the class based on business need: date only, time only, or timezone-aware date-time.
- Use `Period` for date differences and `Duration` for time differences.
- Use `DateTimeFormatter` for formatting and parsing.
- Immutability and clarity are the biggest strengths of Java 8 Date-Time API.

