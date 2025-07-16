
# Web concepts

1. **Web Applications & Request-Response Cycle:**

- **Web applications** operate on a **request-response cycle**, where a client (usually a browser) sends a request to a server, and the server responds with data (e.g., HTML, JSON, etc.).
- This communication typically occurs over the **HTTP** protocol, which is based on a **client-server model** where the client (browser or app) makes requests and the server provides responses.

3. **Network Socket Connection:**

- To enable this communication, a **Connection** is established between the client and the server. This connection is made through a network socket (linux IPC, hard port like 80 on the server side and ephemeral on the client side) and acts as a "tunnel" through which the data (requests and responses) is transmitted.
- Typically, the socket connection is based on **TCP** (Transmission Control Protocol) for reliable, ordered, and error-checked communication.

5. **HTTP/2 and Multiplexing:**

- In **HTTP/2**, the protocol introduces **multiplexing**, allowing multiple streams of data (requests and responses) to be sent over a **single connection** (socket). This helps to overcome limitations in HTTP/1, such as the head-of-line blocking issue, and reduces the overhead of having to establish multiple connections for concurrent requests.
- Multiplexing allows the server and client to send multiple requests and responses at once, without having to wait for one to finish before sending the next.

7. **HTTP/1 and Multiple Connections:**

- In **HTTP/1.x**, to avoid limitations like **head-of-line blocking** (where one request blocking all others), browsers often open **multiple TCP connections** to the server. This allows parallel requests and responses but also introduces overhead (e.g., more memory usage, connection management).
- Because of this, HTTP/1.x often requires several parallel connections to perform efficiently when multiple resources need to be fetched (e.g., images, scripts, stylesheets).

9. **Socket Connections Managed in Kernel Space:**

- The actual management of the **network socket connections** (e.g., establishing, tracking, and closing connections) is handled by the **kernel** of the operating system. The kernel keeps track of all active network connections, including details such as IP addresses, ports, connection states, and protocols used.
- While the details of the socket (like the TCP control block, buffers, etc.) are maintained in **kernel space**, you can view certain **metrics** and information about active connections (e.g., the 5-tuple, connection state, etc.) using tools like **ss** or **netstat**.

**In Summary:**

Web applications rely on a **request-response cycle** over a **network socket** (typically using **TCP**). **HTTP/2** improves this by using **multiplexing** to allow multiple requests and responses on a **single connection**, whereas **HTTP/1** often uses **multiple connections** to overcome protocol limitations. The **kernel** manages the underlying socket connections, and **metrics** about these connections (like state, IPs, and ports) can be viewed through system commands like **ss** and **netstat**.

References + additionals_-

[protocol theory - Why is a TCP Socket identified by a 4 tuple? - Network Engineering Stack Exchange](https://networkengineering.stackexchange.com/questions/54344/why-is-a-tcp-socket-identified-by-a-4-tuple)





# Technologies

**web servers**

apache
nginx
gunicorn

in mem data stores
memchached
redis