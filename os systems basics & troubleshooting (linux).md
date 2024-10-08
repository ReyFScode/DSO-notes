---

---
---
# Core OS terminology




---
# The boot process






---
# Systemd





---
# kernel / user space

**what is kernel space:**

**What is user space:**

**How User speaks to kernel space:**




---
# Process management
 
 Processes are instances of a running program, the process encompasses the entire execution context (current state, resource usage, etc.). Every process in linux is assigned a unique PID.

**Core process terminology:**
-*Parent:*
-*Child:*
-*Exit Status:*
-PID: 

**Process states**:
A process can be in one of 4 states: 
- *Running*: The process is actively using the CPU.
- *Waiting*: The process is waiting for some event (like I/O) to complete.
- *Stopped*: The process has been stopped, usually by a signal.
- *Zombie*: The process has completed execution but still has an entry in the process table. (More on this below)



**Processes in the kernel space**



**Zombie Processes:**
"When a process dies on Linux, it isn't all removed from memory immediately -- its process descriptor stays in memory (the process descriptor only takes a tiny amount of memory). The process's status becomes EXIT_ZOMBIE and the process's parent is notified that its child process has died with the SIGCHLD signal. The parent process is then supposed to execute the wait() system call to read the dead process's exit status and other information. This allows the parent process to get information from the dead process. After wait() is called, the zombie process is completely removed from memory...This normally happens very quickly, so you won't see zombie processes accumulating on your system. However, if a parent process isn't programmed properly and never calls wait(), its zombie children will stick around in memory until they're cleaned up."[What Is a "Zombie Process" on Linux? (howtogeek.com)](https://www.howtogeek.com/119815/htg-explains-what-is-a-zombie-process-on-linux/)

"Zombie processes don't use up any system resources. (Actually, each one uses a very tiny amount of system memory to store its process descriptor.) However, each zombie process retains its process ID (PID). Linux systems have a finite number of process IDs -- 32767 by default on 32-bit systems. If zombies are accumulating at a very quick rate -- for example, if improperly programmed server software is creating zombie processes under load -- the entire pool of available PIDs will eventually become assigned to zombie processes, preventing other processes from launching"

---
# Troubleshooting best practices/essential high level commands

running commands with &, nohup, or & + nohup:


ps -aux

ss

ps

htop



---

#### links
[The TTY demystified (linusakesson.net)](https://www.linusakesson.net/programming/tty/index.php)

[Linux Performance Analysis in 60,000 Milliseconds | by Netflix Technology Blog | Netflix TechBlog](https://netflixtechblog.com/linux-performance-analysis-in-60-000-milliseconds-accc10403c55)

