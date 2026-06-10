# Security Attacks

Short interview-focused notes for common security attacks relevant to Spring applications.

---

## 1. Authentication vs Authorization

| Term | Meaning |
|---|---|
| Authentication | Verifies identity |
| Authorization | Verifies permission |

**Security impact**
- Broken authentication -> attacker becomes another user
- Broken authorization -> attacker accesses data/actions they should not

---

## 2. XSS (Cross-Site Scripting)

### Definition
XSS happens when untrusted input is rendered as executable JavaScript in the browser.

### Types
- Stored XSS
- Reflected XSS
- DOM-based XSS

### Attack Flow
```text
Attacker injects script
   -> App returns unsafe content
   -> Browser executes script
   -> Cookie/token/data may be stolen
```

### Prevention
- Escape output properly
- Avoid unsafe DOM APIs like `innerHTML`
- Use CSP where possible
- Use `HttpOnly` cookies to reduce token theft risk

### Interview Point
XSS is mainly a **browser-side script execution** problem.

---

## 3. CSRF (Cross-Site Request Forgery)

### Definition
CSRF forces a logged-in user's browser to send an unwanted authenticated request.

### Why it happens
- Browser auto-sends cookies
- Server trusts the session cookie
- No extra request verification exists

### Attack Flow
```text
User logs into app
   -> Browser stores session cookie
   -> User opens evil site
   -> Evil site triggers POST request
   -> Browser sends cookie automatically
   -> Server accepts forged request
```

### Prevention
- CSRF token
- `SameSite` cookies
- Avoid cookie-based auth for stateless APIs
- Re-auth/OTP for sensitive actions

### Interview Point
CSRF is mostly a **browser + cookie auto-send** problem.

---

## 4. How Spring Security Prevents CSRF

### Key Components
- **CSRF Token**: random value expected on unsafe requests
- **`CsrfFilter`**: validates token before request reaches controller
- **Unsafe methods**: `POST`, `PUT`, `PATCH`, `DELETE`

### Validation Flow
```text
GET form
   -> server sends CSRF token
POST form
   -> browser sends session + token
   -> CsrfFilter validates token
   -> valid: continue
   -> invalid: 403 Forbidden
```

### When CSRF can be disabled
Disable mainly when:
- API is stateless
- Auth is sent in headers, not auto-sent cookies
- Example: JWT in `Authorization` header

Example: `http.csrf(csrf -> csrf.disable());`

### Interview Point
Session-based MVC apps usually keep CSRF enabled; stateless JWT APIs often disable it.

---

## 5. SQL Injection

### Definition
SQL Injection happens when user input changes SQL query structure.

### Attack Flow
```text
Attacker sends crafted input
   -> app concatenates SQL string
   -> query logic changes
   -> DB returns unauthorized data / bypasses login
```

### Bad Example
```java
String sql = "SELECT * FROM users WHERE username='" + username + "'";
```

### Prevention
- Prepared statements / parameter binding
- Never build SQL using string concatenation
- Use least-privilege DB users
- Validate input, but do not depend only on validation

### JPA/Hibernate Note
- JPQL/repository methods are usually safer because of parameter binding
- Native queries or dynamic string building can still be unsafe

### Interview Point
Parameter binding is the main defense against SQL injection.

---

## 6. Session Fixation

### Definition
Session fixation means the attacker knows or sets a session id before login, and that same session stays valid after login.

### Attack Flow
```text
Attacker fixes session id
   -> victim logs in with same session
   -> session id remains valid
   -> attacker reuses that session
```

### Prevention
- Regenerate session id after login
- Invalidate old session
- Use secure cookie settings

### Spring Security Note
Spring Security protects against session fixation by changing session id after authentication.

---

## 7. Clickjacking

### Definition
Clickjacking tricks the user into clicking hidden or overlaid UI elements.

### Attack Flow
```text
Evil site loads target app in hidden frame
   -> user clicks visible fake button
   -> click lands on hidden real button
   -> sensitive action executes
```

### Prevention
- `X-Frame-Options: DENY` or `SAMEORIGIN`
- CSP `frame-ancestors`
- Do not allow sensitive pages in frames

### Spring Security Note
Spring Security can send frame protection headers.

---

## 8. Quick Comparison

| Attack | Main Risk | Primary Defense |
|---|---|---|
| XSS | Script execution in browser | Output encoding, CSP |
| CSRF | Forced authenticated request | CSRF token, `SameSite` |
| SQL Injection | Query manipulation | Prepared statements |
| Session Fixation | Reused attacker-known session | Regenerate session id |
| Clickjacking | Hidden UI click | Frame protection headers |

---

## 9. High-Value Interview Notes

- XSS runs attacker script in browser.
- CSRF sends unwanted authenticated requests.
- JWT in header reduces classic CSRF risk, but not XSS risk.
- Hibernate helps with safe queries, but unsafe dynamic SQL is still vulnerable.
- Spring Security provides built-in defenses for CSRF and session fixation.

---

## 10. Quick Revision

| Topic | One-line Revision |
|---|---|
| XSS | Browser executes attacker-controlled script. |
| CSRF | Browser sends unwanted authenticated request. |
| `CsrfFilter` | Validates CSRF token on unsafe requests. |
| SQL Injection | Input changes SQL query logic. |
| Session Fixation | Same session survives before and after login. |
| Clickjacking | User clicks hidden framed UI unknowingly. |
