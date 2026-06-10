# Era 2 – Spring JDBC (Reducing Boilerplate)

---

## 1️⃣ Introduction

After developers struggled with verbose and error-prone JDBC code, Spring introduced **Spring JDBC** to simplify database interaction.

Spring JDBC does NOT replace JDBC.

It wraps JDBC and removes:

- Boilerplate code
- Manual resource closing
- Repetitive exception handling

Core class introduced:

→ `JdbcTemplate`

---

## 2️⃣ What Problem Spring JDBC Solved

Problems in Pure JDBC:

- Too much repetitive code
- Manual closing of Connection, Statement, ResultSet
- Complex transaction handling
- SQLException everywhere
- Hard object mapping

Spring JDBC solved these by:

- Managing resources automatically
- Converting checked exceptions to runtime exceptions
- Providing template-based abstraction

---

## 3️⃣ JdbcTemplate – Core Concept

`JdbcTemplate` handles:

- Opening connection
- Preparing statement
- Executing SQL
- Closing resources
- Handling exceptions

Developers only write:

- SQL query
- Parameter values
- Row mapping logic

---

## 4️⃣ Basic Example Using JdbcTemplate

### Configuration Example

```java
@Configuration
public class AppConfig {

    @Bean
    public DataSource dataSource() {
        DriverManagerDataSource ds = new DriverManagerDataSource();
        ds.setUrl("jdbc:mysql://localhost:3306/testdb");
        ds.setUsername("root");
        ds.setPassword("root");
        return ds;
    }

    @Bean
    public JdbcTemplate jdbcTemplate(DataSource dataSource) {
        return new JdbcTemplate(dataSource);
    }
}
```

---

### Query Example

```java
@Service
public class UserService {

    private final JdbcTemplate jdbcTemplate;

    public UserService(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    public User getUserById(int id) {
        String sql = "SELECT * FROM users WHERE id = ?";

        return jdbcTemplate.queryForObject(
                sql,
                new Object[]{id},
                (rs, rowNum) -> {
                    User user = new User();
                    user.setId(rs.getInt("id"));
                    user.setName(rs.getString("name"));
                    return user;
                }
        );
    }
}
```

---

## 5️⃣ Improvements Over Pure JDBC

### 1. No Manual Resource Closing
Spring automatically closes:
- Connection
- Statement
- ResultSet

### 2. Cleaner Exception Handling
Spring converts `SQLException` into:
- DataAccessException (runtime)

No need for try-catch everywhere.

### 3. Simplified Query Execution
Less code compared to raw JDBC.

### 4. Better Integration with Spring Transactions
Works seamlessly with `@Transactional`.

---

## 6️⃣ Still Existing Problems

Even with Spring JDBC:

- SQL still written manually
- Manual RowMapper required
- Business logic still tightly coupled with SQL
- No automatic entity mapping
- Not fully object-oriented

Example repetitive mapping:

```java
(rs, rowNum) -> new User(
    rs.getInt("id"),
    rs.getString("name")
);
```

In large projects, this becomes repetitive again.

---

## 7️⃣ Why Developers Needed Next Evolution

Because developers still:

- Wrote SQL manually
- Mapped ResultSet manually
- Managed relationships manually

They wanted:

- Table ↔ Object mapping automatically
- No manual SQL for CRUD
- Object-based persistence

This led to:

→ Hibernate (ORM revolution)

---

## 8️⃣ Visual Evolution So Far

```
JDBC (manual everything)
        ↓
Spring JDBC (less boilerplate, still SQL-heavy)
        ↓
Hibernate (ORM)
```

---

## 9️⃣ Interview-Level Summary (30 Words)

Spring JDBC simplified raw JDBC by introducing JdbcTemplate, which removes boilerplate code, handles resource management and exceptions automatically, but still requires manual SQL and object mapping, leading to ORM evolution.

---