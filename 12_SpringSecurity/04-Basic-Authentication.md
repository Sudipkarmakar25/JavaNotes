# Basic Authentication

Short interview-focused notes on Basic Authentication in Spring Security.

---

## 1. What is Basic Authentication?

Basic Authentication is an HTTP authentication mechanism where the client sends credentials in the `Authorization` header on every request.
The server decodes the header and authenticates the user.

```http
Authorization: Basic <base64(username:password)>
```

---

## 2. Why it exists

Basic Auth exists because HTTP needed a simple standard authentication mechanism.
It is still used because it is:
- easy to configure
- widely supported
- useful for internal APIs and quick testing

---

## 3. Request Flow

```text
Client sends Authorization header
   -> BasicAuthenticationFilter reads it
   -> decodes username:password
   -> AuthenticationManager authenticates
   -> SecurityContextHolder stores Authentication
   -> request continues
```

### Success Request Example
```http
GET /api/users/me HTTP/1.1
Host: example.com
Authorization: Basic YWRtaW46c2VjcmV0MTIz
```

### Success Response Example
```http
HTTP/1.1 200 OK
Content-Type: application/json

{"username":"admin","role":"ADMIN"}
```

### Failure Response Example
```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Basic realm="Realm"
```

---

## 4. Authorization Header and Base64

### Header Format
`Authorization: Basic <base64(username:password)>`

### Important Point
Base64 is only **encoding**, not encryption.
If someone captures the header, they can decode the credentials easily.

```text
admin:secret123
   -> Base64
YWRtaW46c2VjcmV0MTIz
```

---

## 5. `BasicAuthenticationFilter`

`BasicAuthenticationFilter` is the Spring Security filter that processes Basic Auth headers.
It:
- reads the `Authorization` header
- checks `Basic ` prefix
- decodes credentials
- calls `AuthenticationManager`
- stores result in `SecurityContextHolder`

---

## 6. SecurityContext and Stateless Nature

After successful authentication, Spring stores the user in `SecurityContextHolder` for the current request.
In pure Basic Auth usage, authentication is usually done again on every request.

```text
Request 1 -> authenticate
Request 2 -> authenticate again
Request 3 -> authenticate again
```

### Why Basic Auth is called stateless
Because the client keeps sending credentials; the server does not need a login session to remember the user.

---

## 7. Why HTTPS is required

Basic Auth without HTTPS is unsafe because Base64 does not protect credentials.
HTTPS is required so the `Authorization` header is encrypted in transit.

```text
Without HTTPS:
Authorization header can be captured and decoded
```

---

## 8. Advantages and Disadvantages

| Advantages | Disadvantages |
|---|---|
| Very simple | Credentials sent on every request |
| Easy for Postman/curl | Base64 is not secure |
| No login page needed | Bad fit for public browser apps |
| Useful for internal systems | Must use HTTPS |

---

## 9. Basic Auth vs Form Auth

| Feature | Basic Auth | Form Auth |
|---|---|---|
| Credentials | Header on every request | Form body at login |
| State | Usually stateless | Usually stateful |
| Session | Often no session | Commonly session-based |
| Best Fit | APIs, internal tools | Browser business apps |

---

## 10. High-Value Interview Notes

- Basic Auth sends credentials in the `Authorization` header.
- Base64 is encoding, not encryption.
- `BasicAuthenticationFilter` handles Basic Auth in Spring Security.
- Basic Auth is usually stateless because credentials are sent each time.
- HTTPS is mandatory in real systems using Basic Auth.
- Form auth is better for user-facing browser apps.

---

## 11. Quick Revision

| Topic | One-line Revision |
|---|---|
| Basic Authentication | Sends credentials in `Authorization` header. |
| Authorization Header | `Basic <base64(username:password)>`. |
| Base64 | Encoding, not encryption. |
| `BasicAuthenticationFilter` | Reads header and starts authentication. |
| `SecurityContextHolder` | Stores current request authentication. |
| Stateless Nature | Credentials are typically sent on every request. |
| HTTPS | Protects credentials in transit. |
| Form vs Basic | Form is session-based; Basic is request-header based. |
