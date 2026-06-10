# JWT Authentication

Short interview-focused notes on JWT authentication in Spring Security.

---

## 1. What is JWT?

JWT (JSON Web Token) is a compact signed token used to carry identity and claims between client and server.
It is commonly used for **stateless authentication** in APIs.

### Why JWT?
- no server-side session lookup on each request
- useful for REST APIs and mobile apps
- good fit for distributed systems and microservices

### JWT vs Session Auth

| Feature | JWT | Session |
|---|---|---|
| State | Usually stateless | Stateful |
| Client Sends | Bearer token | Session id / cookie |
| Server Storage | Usually no auth session | Session stored on server |
| Best Fit | APIs, mobile, microservices | Traditional browser apps |
| Logout/Revocation | Harder | Easier |

---

## 2. JWT Structure

```text
header.payload.signature
```

```text
Base64Url(Header).Base64Url(Payload).Signature
```

### Parts
| Part | Meaning |
|---|---|
| Header | Metadata like algorithm and type |
| Payload | Claims/data |
| Signature | Protects integrity |

### Common Claims
| Claim | Meaning |
|---|---|
| `sub` | User identity / subject |
| `exp` | Expiry time |
| `iat` | Issued-at time |
| `iss` | Issuer |
| `roles` | Custom roles/authorities |

### Important Point
JWT payload is usually **encoded, not encrypted**. Do not store sensitive data in it.

---

## 3. JWT Authentication Flow

```text
1. Client sends login request
2. AuthenticationManager authenticates user
3. Server generates JWT
4. Server returns token to client
5. Client stores token
```

### Login Request Example
```http
POST /api/auth/login HTTP/1.1
Content-Type: application/json

{"username":"admin","password":"secret123"}
```

### Login Response Example
```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "accessToken": "eyJhbGciOiJIUzI1NiIs...",
  "refreshToken": "eyJhbGciOiJIUzI1NiIs..."
}
```

### Token Storage Options
- memory
- `sessionStorage`
- `localStorage`
- HttpOnly secure cookie

**Interview note:** token storage matters as much as token generation.

---

## 4. JWT Authorization Flow

```text
1. Request arrives with Bearer token
2. JWT filter extracts token
3. Token signature and expiry are validated
4. Claims are read
5. Authentication is created
6. SecurityContextHolder is populated
7. Authorization rules are checked
```

### Protected Request Example
```http
GET /api/orders HTTP/1.1
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
```

### Failure Response Example
```http
HTTP/1.1 401 Unauthorized
```

---

## 5. Spring Security Components

| Component | Purpose |
|---|---|
| `OncePerRequestFilter` | Common base class for custom JWT filter |
| JWT Filter | Reads token, validates it, sets authentication |
| `SecurityContextHolder` | Stores current request authentication |

### Request Flow Diagram
```text
Authorization: Bearer <jwt>
   -> JWT Filter
   -> validate token
   -> create Authentication
   -> SecurityContextHolder
   -> authorization check
   -> controller
```

### Important Point
In JWT-based stateless auth, the security context is rebuilt from the token on every request.

---

## 6. Security Topics

### Access Token
Short-lived token used to access protected APIs.

### Refresh Token
Longer-lived token used to obtain a new access token.

### Token Expiry
Controlled by `exp`; limits damage if token is stolen.

### Token Revocation
Harder than session invalidation. Common approaches:
- short-lived access tokens
- refresh token rotation
- blacklist / DB / cache tracking

### XSS Impact
If token is accessible to JavaScript, XSS can steal it.
`localStorage` is convenient but more exposed.

### CSRF Impact
- JWT in `Authorization` header -> lower classic CSRF risk
- JWT in cookie -> CSRF risk becomes relevant again

---

## 7. High-Value Interview Notes

- JWT is signed, not encrypted by default.
- Signature protects integrity, not secrecy.
- Custom JWT filters usually extend `OncePerRequestFilter`.
- `SecurityContextHolder` is still used in stateless JWT auth.
- Access token is short-lived; refresh token is longer-lived.
- Revocation is one of the biggest tradeoffs in JWT systems.
- JWT does not solve XSS automatically.
- JWT is not always better than session auth.

---

## 8. Top Interview Questions

| Question | Short Answer |
|---|---|
| What is JWT? | Signed token used for stateless authentication. |
| What are the 3 parts of JWT? | Header, payload, signature. |
| Is JWT encrypted by default? | No, usually only encoded and signed. |
| What does signature do? | Prevents tampering. |
| What is `sub`? | Subject / user identity. |
| What is `exp`? | Token expiry time. |
| Why use JWT? | Stateless API authentication. |
| JWT vs session? | JWT is stateless; session is server-side stateful. |
| Which filter is used for JWT? | Usually a custom filter extending `OncePerRequestFilter`. |
| What does JWT filter do? | Extracts, validates token, and sets authentication. |
| What is access token? | Short-lived API token. |
| What is refresh token? | Used to obtain a new access token. |
| Why is revocation hard? | Server does not naturally track each access token. |
| How does XSS affect JWT? | Token may be stolen if stored unsafely. |
| How does CSRF affect JWT? | Depends on whether browser auto-sends the token. |

---

## 9. Quick Revision

| Topic | One-line Revision |
|---|---|
| JWT | Signed token for stateless authentication. |
| Header | Metadata like algorithm and type. |
| Payload | Claims such as `sub`, `exp`, `roles`. |
| Signature | Prevents tampering. |
| Authentication Flow | Login -> authenticate -> generate token -> return token. |
| Authorization Flow | Read token -> validate -> build context -> authorize. |
| `OncePerRequestFilter` | Common base for custom JWT filter. |
| JWT Filter | Processes Bearer token on each request. |
| `SecurityContextHolder` | Stores current request authentication. |
| Access Token | Short-lived API token. |
| Refresh Token | Used to get new access token. |
| XSS Impact | Token theft risk if stored unsafely. |
| CSRF Impact | Depends on how token is sent. |
