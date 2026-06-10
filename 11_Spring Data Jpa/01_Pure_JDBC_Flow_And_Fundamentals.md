# Era 1 – Pure JDBC (The Beginning of Java Database Access)

---

## 1️⃣ Introduction

Before Hibernate, JPA, or Spring Data existed, Java applications interacted with databases using **JDBC (Java Database Connectivity)**.

JDBC is a low-level API that allows Java programs to:

- Connect to a database
- Execute SQL queries
- Process results
- Manage transactions manually

It was powerful but very verbose and error-prone.

---

## 2️⃣ How JDBC Works (Flow)

Typical JDBC workflow:

1. Load Driver
2. Create Connection
3. Create Statement / PreparedStatement
4. Execute Query
5. Process ResultSet
6. Close Resources

Flow diagram:

Connection → Statement → Execute SQL → ResultSet → Map to Object → Close

---

## 3️⃣ Basic JDBC Example

```java
import java.sql.*;

public class JdbcExample {

    public static void main(String[] args) throws Exception {

        String url = "jdbc:mysql://localhost:3306/testdb";
        String username = "root";
        String password = "root";

        Connection connection = DriverManager.getConnection(url, username, password);

        String sql = "SELECT * FROM users WHERE id = ?";
        PreparedStatement statement = connection.prepareStatement(sql);

        statement.setInt(1, 1);

        ResultSet resultSet = statement.executeQuery();

        while (resultSet.next()) {
            int id = resultSet.getInt("id");
            String name = resultSet.getString("name");
            System.out.println(id + " - " + name);
        }

        resultSet.close();
        statement.close();
        connection.close();
    }
}
```

---

## 4️⃣ Key Components in JDBC

### DriverManager
Establishes database connection.

### Connection
Represents active connection with DB.

### Statement / PreparedStatement
Executes SQL queries.

### ResultSet
Stores query results.

---

## 5️⃣ Problems with Pure JDBC

### 1. Boilerplate Code
Large amount of repetitive setup code.

### 2. Manual Resource Management
Developers must close:
- Connection
- Statement
- ResultSet

Forgetting this causes memory leaks.

### 3. Exception Handling Complexity
SQLExceptions must be handled everywhere.

### 4. Manual Object Mapping
ResultSet must be converted manually into Java objects.

Example:

```java
User user = new User();
user.setId(rs.getInt("id"));
user.setName(rs.getString("name"));
```

This becomes repetitive in large projects.

### 5. Hard to Maintain
Business logic mixed with SQL logic.

---

## 6️⃣ Transaction Handling in JDBC

Transactions must be manually controlled.

```java
connection.setAutoCommit(false);

try {
    // execute multiple queries
    connection.commit();
} catch (Exception e) {
    connection.rollback();
}
```

Developers must manage:
- Commit
- Rollback
- Auto-commit mode

Risk of inconsistent data if mishandled.

---

## 7️⃣ Why JDBC Was Still Important

Even though verbose, JDBC provided:

- Direct SQL control
- Maximum flexibility
- High performance
- No abstraction overhead

It was the foundation of all future persistence technologies.

---

## 8️⃣ Limitations That Led to Next Evolution

Because of:

- Too much boilerplate
- Manual mapping
- Complex transaction handling
- Error-prone resource management

Developers needed abstraction.

This led to:

→ Spring JDBC  
→ Hibernate  
→ JPA  
→ Spring Data JPA

---

## 9️⃣ Interview-Level Summary (30 Words)

JDBC is a low-level Java API for database interaction requiring manual SQL execution, transaction management, and object mapping, leading to verbose code and maintenance issues that inspired higher-level ORM frameworks.

---