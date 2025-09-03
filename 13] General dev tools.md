

---
# SSH/SCP
**What it is:**
SSH (Secure Shell) is a utility that is most commonly used to create a secure remote connection over port 22. It is probably the most widely used remote connection protocol in the world. It provides encrypted communication between two systems, ensuring the confidentiality and integrity of data. SSH is considered the successor to Telnet, which transmits data in plaintext and is therefore much less secure for remote connections.

**ssh syntax:**
SSH is most commonly used in this format: 
`ssh username@Some.IP.address 
with this format we enter a username @ a target accessible IP, hostname, or FQDN and are then prompted for the users password to authenticate and establish the connection. Some other commonly used SSH syntax is:

1 . `ssh -i something.pem   user@IP` : -i specifies an identity file that has the private key for key-based ssh authentication.


**SSH key pairs:**
how it works:
1. **Generate the Keys**
    - You create a key pair on your computer. This includes a private key and a corresponding public key. we do this with the `ssh-keygen` command

2. **Copy the Public Key**
    - You give the public key to the server you want to access. This is usually done by adding it to a special file on the server (`~/.ssh/authorized_keys`).

3. **Connecting to the Server**
    - When you try to connect to the server, it challenges your client by sending a message encrypted with your public key.
    - Your client decrypts the message using your private key and sends back a response.
    - If the server can verify the response, it knows you have the correct private key and grants you access.


---
# WSL
**What it is:**
WSL (Windows Subsystem for Linux) is a Windows feature that allows users to run a Linux environment directly on Windows without the need for a traditional virtual machine. It provides compatibility for Linux commands and tools, making it commonly used for development, scripting, and performing Linux operations on a Windows system. WSL is also often used as a backend for Linux containers with Docker Desktop on Windows.


**How to install:**
[Install WSL | Microsoft Learn](https://learn.microsoft.com/en-us/windows/wsl/install)



**How to use:**
use the `wsl` command in PowerShell to enter a non-privileged Linux session in your installed distro.

use the `wsl -u root` command to enter a privileged session in your installed distro

use the `wsl  [command here]` to run a one-off Linux command in your PowerShell terminal







---

[Network Topology Types: Complete Overview (nakivo.com)](https://www.nakivo.com/blog/types-of-network-topology-explained/)