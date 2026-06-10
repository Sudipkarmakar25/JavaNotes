# Spring Security Fundamentals

Short interview-focused notes for Java backend and Spring Boot.

---

## 1. What is Spring Security?

Spring Security secures Spring applications by handling **authentication** and **authorization**.
It works mainly through a **filter chain** that runs before the request reaches controllers.

```text
Client Request
   -> Security Filters
   -> Authentication
   -> Authorization
   -> Controller
```

---

## 2. Authentication vs Authorization

| Term | Meaning |
|---|---|
| Authentication | Verifies identity: who are you? |
| Authorization | Verifies permission: what can you access? |

**Example**
- Login with username/password -> authentication
- Access `/admin/**` only for admins -> authorization

---

## 3. Core Architecture

```text
Request
  -> SecurityFilterChain
  -> Authentication Filter
  -> AuthenticationManager
  -> AuthenticationProvider
  -> UserDetailsService / DB
  -> SecurityContextHolder
  -> Authorization Check
  -> Controller
```

### Key Idea
- Filters intercept the request.
- Authentication validates the user.
- Authorization checks roles/authorities.
- Authenticated user is stored in the security context.

---

## 4. Important Components

| Component | Purpose | Internal Role |
|---|---|---|
| `SecurityFilterChain` | Defines security rules | Decides which filters run for a request |
| `AuthenticationManager` | Coordinates authentication | Delegates to provider(s) |
| `AuthenticationProvider` | Validates credentials/token | Performs actual authentication |
| `UserDetailsService` | Loads user data | Returns `UserDetails` |
| `PasswordEncoder` | Hashes and verifies passwords | Prevents plain-text password storage |
| `SecurityContextHolder` | Holds current authenticated user | Stores `Authentication` for request/thread |

---

## 5. `SecurityFilterChain`

`SecurityFilterChain` is the entry point for request security in modern Spring Security.
It replaces the old `WebSecurityConfigurerAdapter` style.

```java
@Bean
SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
    return http
            .authorizeHttpRequests(auth -> auth
                    .requestMatchers("/public/**").permitAll()
                    .requestMatchers("/admin/**").hasRole("ADMIN")
                    .anyRequest().authenticated())
            .formLogin(Customizer.withDefaults())
            .build();
}
```

---

## 6. AuthenticationManager and AuthenticationProvider

### `AuthenticationManager`
Main entry point for authentication.
It receives an `Authentication` object and delegates validation.

### `AuthenticationProvider`
Performs real validation such as:
- username/password check
- JWT validation
- LDAP/OAuth2 validation

```text
Filter -> AuthenticationManager -> AuthenticationProvider
```

---

## 7. `UserDetailsService` and `PasswordEncoder`

### `UserDetailsService`
Loads user data by username.
It does **not** compare passwords.

### `PasswordEncoder`
Encodes passwords during signup and verifies them during login.
Common implementation: `BCryptPasswordEncoder`.

```java
@Bean
PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();
}
```

---

## 8. `SecurityContextHolder`

After successful authentication, Spring stores the current user in `SecurityContextHolder`.
It usually uses thread-local storage for the current request.

```java
Authentication auth = SecurityContextHolder.getContext().getAuthentication();
String username = auth.getName();
```

```text
SecurityContextHolder
      -> SecurityContext
      -> Authentication
```

---

## 9. Principal, Authority, Role

| Term | Meaning |
|---|---|
| Principal | Current logged-in identity |
| `GrantedAuthority` | Permission/privilege |
| Role | Coarse-grained authority like `ADMIN` |

### Role vs Authority
- Role: broad access label like `ADMIN`
- Authority: fine-grained permission like `READ_REPORTS`
- Spring internally treats roles as authorities with prefix `ROLE_`

```text
hasRole("ADMIN") -> checks ROLE_ADMIN
```

---

## 10. End-to-End Flow

```text
1. Request reaches security filter chain
2. Filter extracts credentials/token
3. AuthenticationManager delegates authentication
4. Provider validates user
5. SecurityContextHolder stores Authentication
6. Authorization checks roles/authorities
7. Request reaches controller
```

---

## 11. High-Value Interview Notes

- Spring Security is **filter-based**, not controller-based.
- `AuthenticationProvider` does the actual authentication.
- `UserDetailsService` loads user info; it does not verify passwords.
- `PasswordEncoder` should always be used for password storage.
- `SecurityContextHolder` stores current authenticated user details.
- Roles are usually stored as authorities with `ROLE_` prefix.

---

## 12. Quick Revision

| Topic | One-line Revision |
|---|---|
| Spring Security | Secures Spring apps using filters, authentication, and authorization. |
| Authentication | Verifies identity. |
| Authorization | Verifies permission. |
| SecurityFilterChain | Configures request security rules. |
| AuthenticationManager | Delegates authentication. |
| AuthenticationProvider | Performs actual credential/token validation. |
| UserDetailsService | Loads user data. |
| PasswordEncoder | Hashes and verifies passwords. |
| SecurityContextHolder | Stores current request authentication. |
| Principal | Logged-in user identity. |
| GrantedAuthority | Permission/privilege. |
| Role | Broad authority like `ADMIN`. |
