
# Web concepts

1. **Web Applications & Request-Response Cycle:**
- **Web applications** operate on a **request-response cycle**, where a client (usually a browser) sends a request to a server, and the server responds with data (e.g., HTML, JSON, etc.).
- This communication typically occurs over the **HTTP** protocol, which is based on a **client-server model** where the client (browser or app) makes requests and the server provides responses.

	1. **Network Socket Connection:**
	- To enable this communication, a **Connection** is established between the client and the server. This connection is made through a network socket (linux IPC, hard port like 80 on the server side and ephemeral on the client side) and acts as a "tunnel" through which the data (requests and responses) is transmitted.
	- Typically, the socket connection is based on **TCP** (Transmission Control Protocol) for reliable, ordered, and error-checked communication.

3. **HTTP/2 and Multiplexing:**
- In **HTTP/2**, the protocol introduces **multiplexing**, allowing multiple streams of data (requests and responses) to be sent over a **single connection** (socket). This helps to overcome limitations in HTTP/1, such as the head-of-line blocking issue, and reduces the overhead of having to establish multiple connections for concurrent requests.
- Multiplexing allows the server and client to send multiple requests and responses at once, without having to wait for one to finish before sending the next.

4. **HTTP/1 and Multiple Connections:**
- In **HTTP/1.x**, to avoid limitations like **head-of-line blocking** (where one request blocking all others), browsers often open **multiple TCP connections** to the server. This allows parallel requests and responses but also introduces overhead (e.g., more memory usage, connection management).
- Because of this, HTTP/1.x often requires several parallel connections to perform efficiently when multiple resources need to be fetched (e.g., images, scripts, stylesheets).

5. **Socket Connections Managed in Kernel Space:**
- The actual management of the **network socket connections** (e.g., establishing, tracking, and closing connections) is handled by the **kernel** of the operating system. The kernel keeps track of all active network connections, including details such as IP addresses, ports, connection states, and protocols used.
- While the details of the socket (like the TCP control block, buffers, etc.) are maintained in **kernel space**, you can view certain **metrics** and information about active connections (e.g., the 5-tuple, connection state, etc.) using tools like **ss** or **netstat**.

**In Summary:**
Web applications rely on a **request-response cycle** over a **network socket** (typically using **TCP**). **HTTP/2** improves this by using **multiplexing** to allow multiple requests and responses on a **single connection**, whereas **HTTP/1** often uses **multiple connections** to overcome protocol limitations. The **kernel** manages the underlying socket connections, and **metrics** about these connections (like state, IPs, and ports) can be viewed through system commands like **ss** and **netstat**.

References + additionals_-
[protocol theory - Why is a TCP Socket identified by a 4 tuple? - Network Engineering Stack Exchange](https://networkengineering.stackexchange.com/questions/54344/why-is-a-tcp-socket-identified-by-a-4-tuple)


---

# Signing

Signing is integral to almost every facet of systems and application development. At its core, signing is the process of proving **authenticity and integrity** of data via cryptography. There are three primary types of signing, which we will cover in order of importance and frequency of occurrence. **signing is not encryption although cryptographic algorithms are used. signed resources are just marked to show that the data is unmodified, if the data changes the signature wont match.**

> **Note:** Signing ensures **Integrity**, but does not conceal any information sent, to conceal information and ensure **confidentiality** we must utilize encryption. 

##### **1) Asymmetric Signing (Public/Private Key)**
Asymmetric signing is the most common type and is used in **TLS certificates, DNSSEC, JWTs (RS256/ES256), blockchain transactions, code signing, SSH keys**, and more. It Uses a **private key** (kept secret, never leaves the client or signing device.) and a **public key** (shared with trusted parties).
- **How It Works:**
    1. The data (file, message, certificate, etc.) is hashed to produce a fixed-length **digest**.
    2. The private key signs the digest, producing a **digital signature**.
    3. The signature and original data are sent to the verifier.
    4. The verifier uses the public key to validate the signature, confirming authenticity and integrity.

A key concept in this signing format is **hashing**. Hashing takes an object (data, certificate, message, etc.) and produces a fixed-length value called a **digest**. This digest is then processed by the private key using the signing algorithm, producing the **signature**. The signature is what proves that the data is authentic and has not been altered.

- **Example 1  – SSH Authentication:**
	1. Server (ssh target) sends a random challenge 
	2. Client (node ssh originates from) hashes the challenge (and session-specific context).
	3. Client signs the hash with its private key (RSA, ECDSA, or Ed25519).
	4. Server verifies the signature using the client’s public key stored in `authorized_keys`.
	5. This is a unique usage because it’s not validating the integrity of any data; instead, it uses the asymmetric signing method to verify that the client possesses the private key corresponding to the public key distributed to the server.
- **Example 2 – JWT Signing:**
	1. An Identity Provider (IdP) generates a JWT containing user claims (user ID, roles, expiration).
	2. The IdP hashes the JWT header and payload.
	3. The IdP signs the hash with its **private key** (e.g., RS256).
	4. A service or API receiving the JWT verifies the signature using the IdP’s **public key** to ensure the token is authentic and unmodified.
	5. NOTE: The **JWT is a credential**: it says “this client is trusted and has these claims/permissions.” More on this in the 'systems design' notes page.
**NOTE:**
- Intercepted signatures cannot be reused or reverse-engineered to reveal the private key.

##### **2) Symmetric Signing / HMAC**
Symmetric signing uses a **shared secret** instead of a public/private pair. Both sender and receiver must know the secret key.
- **Use Cases:** API request authentication, internal service-to-service calls, JWTs with HS256.
    
- **How It Works:**
    1. Data is processed through a **hash-based message authentication code (HMAC)** using the shared secret.
    2. The resulting signature is sent along with the data.
    3. The receiver recomputes the HMAC using the same secret and compares it to the signature to verify authenticity and integrity.
**Key Points:**
- Faster than asymmetric signing.
- Less scalable for large networks because all parties must share the secret.
- Provides authenticity and integrity but not non-repudiation (any holder of the secret could have signed the data).

##### **3) Hybrid / Envelope Signing**
Hybrid signing combines asymmetric and symmetric approaches to balance trust and performance
- **Use Cases:** TLS session establishment, secure messaging, large data signing.

- **How It Works:**
    1. Asymmetric keys are used to exchange a **symmetric session key** securely.
    2. The symmetric key is then used to sign or encrypt large data efficiently.
**Key Points:**
- Provides the **trust of asymmetric signing** for establishing keys.
- Achieves **performance efficiency** for large data or high-throughput systems.
- Common in TLS and modern secure communication protocols.


---

## **Encryption**

Encryption is the process of transforming data into a form that is unreadable to anyone who does not possess the correct key, ensuring confidentiality. Unlike signing, which guarantees authenticity and integrity, encryption is used to protect the content of data from unauthorized access, whether it’s at rest, in transit, or in use.

> **Note:** Encryption ensures **confidentiality**, but does not inherently verify the sender or **integrity** of the data — signing is needed for integrity guarantees.
> 
##### **Key Concepts**

- **Symmetric Encryption:**
    - Uses a single shared key for both encryption and decryption.
    - Fast and efficient for large amounts of data.
    - Example: AES (Advanced Encryption Standard) for disk encryption or database fields.

- **Asymmetric Encryption:**
    - Uses a **public/private key pair**.
    - Public key encrypts the data; only the private key can decrypt it.
    - Example: TLS/SSL for secure web traffic, email encryption, or encrypting a small secret before sending to a server.

- **Use Cases:**
    - Protecting sensitive information (passwords, PII, API secrets).
    - Securing communications between client and server (HTTPS, SSH session encryption).
    - Encrypting files or databases to prevent unauthorized access.
