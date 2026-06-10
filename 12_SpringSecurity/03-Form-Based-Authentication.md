# Form-Based Authentication

Short interview-focused notes on session-based login in Spring Security.

---

## 1. What is Form Authentication?

Form authentication is the classic browser login flow where a user submits username and password through an HTML form.
After successful login, Spring Security usually creates a **session** and tracks it using `JSESSIONID`.

```text
User submits login form
   -> Spring Security authenticates
   -> Session created
   -> Browser gets JSESSIONID cookie
```

---

## 2. Complete Authentication Flow

```text
1. Browser sends POST /login
2. UsernamePasswordAuthenticationFilter reads credentials
3. AuthenticationManager delegates authentication
4. AuthenticationProvider validates user
5. UserDetailsService loads user data
6. SecurityContextHolder stores Authentication
7. Session is created
8. Browser receives JSESSIONID
```

### Request Example
```http
POST /login HTTP/1.1
Content-Type: application/x-www-form-urlencoded

username=admin&password=secret123
```

### Response Example
```http
HTTP/1.1 302 Found
Set-Cookie: JSESSIONID=ABC123XYZ; Path=/; HttpOnly
Location: /
```

---

## 3. Core Components

| Component | Purpose |
|---|---|
| `UsernamePasswordAuthenticationFilter` | Extracts form credentials and starts authentication |
| `AuthenticationManager` | Delegates authentication |
| `AuthenticationProvider` | Performs actual validation |
| `UserDetailsService` | Loads user details |
| `SecurityContextHolder` | Stores current authenticated user |

### Important Point
`UserDetailsService` loads user data, but password verification is done by the provider with a `PasswordEncoder`.

---

## 4. Authorization Flow

```text
Request with JSESSIONID
   -> Spring loads session
   -> Restores SecurityContext
   -> Checks roles/authorities
   -> Allowed: controller runs
   -> Denied: 403 Forbidden
```

### Protected Request Example
```http
GET /admin/dashboard HTTP/1.1
Cookie: JSESSIONID=ABC123XYZ
```

---

## 5. Session Creation and `JSESSIONID`

### Session Creation
After login, Spring stores the authenticated `SecurityContext` in the HTTP session.
That is why form login is usually **stateful**.

### `JSESSIONID`
`JSESSIONID` is the session identifier cookie.
It does not store user data directly; it only points to the server-side session.

```http
Set-Cookie: JSESSIONID=ABC123XYZ; Path=/; HttpOnly
```

---

## 6. `SecurityContextHolder`

`SecurityContextHolder` stores the current request's `Authentication` object.
On later requests, Spring restores it from the session and clears it after request completion.

```java
Authentication auth = SecurityContextHolder.getContext().getAuthentication();
```

---

## 7. Logout Flow

```text
POST /logout
   -> LogoutFilter handles request
   -> SecurityContext cleared
   -> Session invalidated
   -> JSESSIONID becomes useless/expired
```

### Response Example
```http
HTTP/1.1 302 Found
Set-Cookie: JSESSIONID=; Max-Age=0; Path=/
Location: /login?logout
```

---

## 8. Remember-Me Authentication

Remember-me allows the user to stay logged in even after the normal session expires.
It usually works through a long-lived cookie and automatic re-authentication.

```text
Session exists -> use JSESSIONID
Session expired + remember-me cookie exists -> auto-login attempt
```

### Important Point
Remember-me is a convenience feature, not a replacement for strong authentication.
Sensitive actions may still require fresh login.

---

## 9. Form Auth vs Stateless Auth

| Feature | Form Authentication | JWT/Basic |
|---|---|---|
| State | Stateful | Usually stateless |
| Session | Yes | Often no session |
| Browser Fit | Excellent | Less user-friendly |
| Credentials Sent | Usually only at login | Often every request/token per request |

---

## 10. High-Value Interview Notes

- `UsernamePasswordAuthenticationFilter` handles form login.
- Form authentication is usually **session-based**.
- `JSESSIONID` identifies the session, not the user data.
- `SecurityContextHolder` stores current authentication.
- Logout clears security context and invalidates session.
- Remember-me is weaker than a fresh authenticated session.

---

## 11. Quick Revision

| Topic | One-line Revision |
|---|---|
| Form Authentication | Browser login with session-based security. |
| `UsernamePasswordAuthenticationFilter` | Reads form credentials and starts authentication. |
| `AuthenticationManager` | Delegates authentication. |
| `UserDetailsService` | Loads user data. |
| Session | Stores login state on server. |
| `JSESSIONID` | Session identifier cookie. |
| `SecurityContextHolder` | Stores current request authentication. |
| Logout | Clears context and invalidates session. |
| Remember-Me | Cookie-based automatic re-authentication. |
