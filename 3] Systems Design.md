# Systems Design Synopsis

Systems design engineering is the process of defining the **architecture, components, modules, interfaces, and data** for a system to meet specified requirements. It involves planning how different components interact, selecting a technology stack, and implementing strategies for **scalability, reliability, and maintainability**.

---

# APIs

### Why Do We Need APIs?
APIs help developers build decoupled software more easily. Instead of writing complex code from scratch, they can call APIs that provide the functionality they need. For example, a developer can fetch weather data via an API instead of building a full weather system. APIs are also crucial in modern websites where **heavy data transfers occur between client and server**.
### How Do APIs Work?
- **Request:** A client (user) sends a request to the API’s URI.
- **Processing:** The API forwards the request to the server.
- **Response:** The server processes the request and generates a response.
- **Delivery:** The API returns the response to the client.

Think of this as the API being a router: it directs incoming requests to the appropriate internal function or external system and delivers the response back to the client.

### Types of API Architectures
1. **REST (Representational State Transfer):**
    - Uses HTTP methods (GET, POST, PUT, DELETE) for communication.
    - Flexible and widely used in modern web apps.
2. **SOAP (Simple Object Access Protocol):**
    - XML-based, more rigid and standardized.
3. **GraphQL:**
    - Query language for APIs allowing clients to request exactly the data they need.
### Security
APIs often require authentication and authorization to control access:
- **API Key:** Unique key included in request headers or URL.
- **OAuth:** Token-based authentication.
- **Basic Auth:** Encodes username/password in the request header (less secure).

## API Methods / Interacting with APIs (REST)
REST is an architectural style for designing loosely coupled applications over the network. It provides **high-level design guidelines** without enforcing specific lower-level implementation rules.

- **Data Format:** Most REST APIs use **JSON (JavaScript Object Notation)** as the standard, though XML, Protobuf, or MessagePack are alternatives.


---

# Authentication and Authorization

## Key Concepts
- **Authentication:** Verifying a user or client’s identity.
- **Authorization:** Determining what actions an authenticated user can perform (permissions), potentially object-specific.

## Authentication/Authorization Providers

- Auth0
- Okta
- Firebase Auth
- AWS Cognito

## -- Authorization Mechanisms --

### (1) JWTs (JSON Web Tokens)
JWTs are compact, URL-safe tokens that can be used for both authentication and authorization, depending on how they’re issued and used:
**1. Authentication**
- JWT can **prove the identity of a user or client**.
- After a successful login, the server issues a signed JWT containing claims like user ID (`sub`) and issued-at time (`iat`).
- Subsequent API requests include the JWT, allowing the server to verify **who is making the request** without re-checking credentials each time.

 **2. Authorization**
- JWT can also carry **permissions or roles** in its payload.
- The server reads claims like `role`, `scope`, or `permissions` to decide **what actions the client is allowed to perform**.
- Example: a JWT might allow an “admin” to access `/api/users` but restrict a “viewer” to `/api/profile`.

**Structure:**
```
<header>.<payload>.<signature>
```
- **Header:** Metadata (signing algorithm `alg`, token type `typ`).
- **Payload:** Claims (user ID, roles, expiration `exp`).
- **Signature:** Cryptographic signature over the header + payload, ensuring **integrity** and **authenticity**.

**Example Header and Payload (before signing):**
```json
// Header
{
  "alg": "RS256",
  "typ": "JWT"
}

// Payload
{
  "sub": "1234567890",
  "name": "Alice",
  "role": "admin",
  "iat": 1693660800,
  "exp": 1693664400
}
```

**Usage in API Requests:**
```
GET /api/orders HTTP/1.1
Host: example.com
Authorization: Bearer <JWT>
```

**Server Verification Steps:**
1. Decode header and payload.
2. Use `kid` from JWT header to fetch public key from the issuer’s JWKS endpoint.
3. Verify the signature.
4. Check claims (`exp`, `aud`, roles) to enforce authorization.
5. If valid → allow access; if invalid → reject.

> **Note:** JWTs authenticate the client and provide claims; they **do not prevent malicious actions** if the client misbehaves.



### (2) JWKS (JSON Web Key Set)
- JWKS is a set of **public keys published by the issuer** to verify JWT signatures.
- Supports **automatic key rotation** without breaking verification.

**Example JWKS snippet:**
```json
{
  "keys": [
    {
      "kty": "RSA",
      "use": "sig",
      "kid": "1234abcd",
      "n": "0vx7agoebGcQSuuPiLJXZptN...",
      "e": "AQAB"
    }
  ]
}
```
- `kty` = key type
- `use` = intended use (`sig` = signing)
- `kid` = key ID, matches JWT header
- `n` and `e` = RSA modulus and exponent

These keys are then made available for example: Auth0 exposes a JWKS endpoint for each tenant, which is found at `https://{yourDomain}/.well-known/jwks.json`. This endpoint will contain the JWK used to verify all Auth0-issued JWTs for this tenant.



## -- Authentication Terms/concepts --
- **Signing:** Ensures **authenticity and integrity** of data. Example uses: JWTs, SSH keys, TLS certificates.
- **Encryption:** Ensures **confidentiality**.
- **Hashing:** Produces a fixed-length digest to ensure **data integrity**. Used in signing workflows for JWTs and SSH authentication.
  
 > 	For more on the above see the super general information notes page, it has detailed info on signing, encryption and hashing

---
...
# Technologies

## Web Servers

- Apache
    
- Nginx
    
- Gunicorn
    

## In-Memory Data Stores

- Memcached
    
- Redis
    

---
