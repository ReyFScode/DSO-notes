# **Linux notes (Debian + RHEL) + Bash scripting**
# **Section 1) General knowledge (any distro)**
All of the below section concepts will (95%) be the same on any Linux distro you use.

---

## Core OS/Hardware terminology
***OS terminology***
**heap** - segment of memory that can be controlled dynamically, essentially a free pool of memory that can run your application.

**stack** - segment of memory where specific data like local variables or function calls gets added and removed in "last in, first out"

**process** - refers to a current program under execution, linux processes run isolated in the background so they do not interrupt each others execution, we can view them with the `ps aux` command. **Everything running in Linux is considered a process.**

**Service** - refers to a process that are managed by the service manager (e.g. systemd), Services are triggered by the service manager based on certain events or conditions (e.g., at boot, shutdown, on-demand, or at specific times). For example, a service may start automatically when the system boots (e.g., `sshd` for SSH access), or it may start on a timer, or be manually started by the user. Services are a way we ensure particular critical processes that are important for the system are always running when necessary.

**threads** - a thread is a lightweight process, processes can create threads to do work quickly and concurrently we can see threads per process in the nlwp field when we run `ps aux -L`.Threads will share the same resources and memory as the process that spawned them.

**syscall** - procedure that an application invokes to make calls from the user space to the kernel space to perform a task. e.g. for vim to open a file it must make an open() system call to the kernel to perform that task.

**hardlink** - a hardlink is a a directory entry that points to a selected file, if the file is deleted its data still exists in the hardlink, and data changes in one are reflected in the other ala bindmount. They share the indode number of their link source so they are resource efficient. they cannot span filesystems. created with the `ln` command, often used for backup systems since they perserve the data of their origin file but do not take up additional disk space

**softlink** - a soflink is also a symbolic link to a file but it has its own inode assignment, can span filesystems. if the source file is deleted the link file "dangles" and points to nothing. created with `ln -s`. we often softlink applications to /bin when we want them to be available by command.

**inode** - unix data struct that contains metadata about the file.

***hardware terminology***
**CPU** - central processing unit, control unit for computer, normally indicated by the term socket (place where the cpu goes)

**core** - independent processing component that comprises a CPU, there can be multiple

**Thread (CPU context)** - instructional pathway for CPU core, there can be multiple

**logical cpus** - usually the metric that is used to determine how many cpus a system has, gotten by multiplying sockets x cores per socket x threads per core, e.g. two cpu sockets with 4 cores per socket and 2 threads per core gives us 16 logical cpus 



---


## **Text Editors:**
   - Linux offers various text editors for editing configuration files, scripts, and documents.
   - Common text editors include Vi, Vim, Nano, and Emacs. The core editors you will most likely encounter are Vi/Vim and Nano.

**Core Editor usage:**
   - **Vi/Vim:** Vi is not fun, you can use the same save/discard instructions as you would for vim but if you have to pick between Vi and Vim....use Vim...take my word on that. Install vim using `apt-get install vim/yum install nano`(distro dependent).  Use `vim [filename]` command to open Vim editor. Press `i` to enter insert mode, make changes, then press `Esc` followed by `:wq` to save and exit, or use `Esc` followed by `:q!` to discard changes.
     
   - **Nano:** This is probably the most user friendly editor. Install nano using `apt-get install nano/yum install nano` (distro dependent). After installation use `nano [filename]` to enter the nano editor, use `ctrl+x   |   y` to save changes and `ctrl+x   |   n` to not save.


___


#### **Basic Commands / operators:**
#### **commands**
- `ls`: List directory contents, specify `-al` for a more detailed view.
- `du -h`: Displays the disk usage of files and directories (in human readable format). Example: `du -h /path/to/directory` displays disk usage of a target directory  in human-readable format.
- `df -h`: Shows the amount of disk space available on the host filesystem (in human readable format). Example: use `df -h` to display host disk stats in human-readable format.
- `cp [filename] [/target/dir]` : Copy files and directories, specify -r for directories.
- `mv [filename] [/target/dir/filename]`: Move or rename files and directories.
- `rm [filename]`: Remove files and directories,  for directories add -r after *rm*.
- `mkdir [dir_name]`: Create directories.
- `mkdir -p something/{1,2,3/something_else}` - mkdir -p allows you to create a directory structure, the above command would make:
	├── something
	│      ├── 1
	│      ├── 2
	│      └── 3
	│           └── something_else

- `touch [filename]`: Create new file.
- `pwd` : Prints the current working directory
- `lscpu` : Prints details about the system processor architecture. 
- `nproc` : simply prints the number of logical cpus the system has.
- `echo "something here"`: Prints text to stdout (console), adds a newline after text automatically
- `printf "something here"`: print text to stdout, no newline appended 
- `some command | tee ./file`: takes the output of command pre-pipe and streams it to stdout & a file at the same time, *teeing* is good for logging implementation in scripts and for recording troubleshooting sessions.
- `cat / cat -b` : gets the contents of a file, -b is used to add line numbers

#### **operators**
- `>> / >`: Operator used to pipe output to a file (called output redirection, can be an existing file or new file) can be used with commands or with echo. It is important to note that **>>** will append text to the end of the file whilst **>** will overwrite the file so be careful. 
	e.g.:
	- `echo "hello world" >> new.txt` will append "hello world" to a file called new.txt, if it doesn't exist new.txt will be created.
	- `ls -al > new.txt` will overwrite the contents of new.txt with the output of the *ls -al* command, will create a new file if there is no new.txt.
	
- `<` : this operator is used for input redirection, while > pipes output from a command to a file, this output passes file contents to a command, for instance instead of having to do `cat test.txt | grep "something"` to search for 'something' in the test.txt file we can just do `grep "something" < test.txt` saving us some characters

- `|` : This is called the pipe operator, this is used to pass the output value of one command to another.
	e.g.: 
	- `lscpu | grep -i vulnerability | sed 's/a/b/g'` will take the output value of *lscpu* and pass it to the *grep -io* command which will do a case-insensitive search for any lines with "vulnerability" it will then pass this parsed output to the sed command and replace 'a' with 'b' giving you a final output of  all lines with the world "vulnerability" with all 'a' characters replaced with 'b'.


#### The holy trinity: Sed, Grep, and Awk:
These three tools allow for powerful output transformations and are often pipelined alone or in combination e.g `command  | sed ... | awk ... | grep`

 1) **`awk` (Text Processing):** **A versatile tool for pattern scanning and processing.**
      - Example: `awk '{print $1}' file.txt` to print the first column of a text file. or `docker image ls | awk '{print $2}` would be used to only print the tag (second) column of the docker image list command.
      
2) **`sed` (Stream Editor):** **Used for filtering and transforming text.**
      - Example: `sed -i 's/old/new/g' file.txt` to replace all occurrences of 'old' with 'new' in a file. 
      - you can use `sed -i '2d' ./file.txt` to delete a line in a file (replace 2 with the line number you want to delete, starts at 1 not 0). 
      - For ALL use cases If you don't specify `-i` (short for insert) the changes wont be written to the file, not specifying `-i` first is a good way to test your replacement to ensure its performing the desired behavior before actually writing changes.
      - NOTE - see section below for comparison between `tr` & `sed`
      
3) **`grep` (search):** **Used to search for text in output/files, output can be tailored with flags.**
	Some useful grep flags (you can combine these for highly customized outputs):
	- `-i`: case insensitive, ignores case, `grep -i "bob`" will find both "BOB" & "bob"
	- `-o`: only, matches only the output vs the line of origin. e.g with a file that has the contents "my name is bob" running `grep "bob" somefile.txt ` will output the line that contains bob: "*my name is bob*", specifying `grep -o` will only output "bob"
	- `-v` : regex to match everything EXCEPT what is specified e.g. if you have a file with the contents: 
			**bob** 
			**joe** 
			**sam** 
			**bob1**
			**fred**
			the command: *cat file.txt | grep -v "bob"* will output "joe sam fred" omitting everything that includes "bob." This is useful for when you have a massive amount of data and need to quickly remove a set of values. data must be newline separated.
	- `grep -R  /dir/path/` : recursive grep, super cool! allows you to search all contents of a directory for a match
	- `grep -E "something|else" ./somefile`: extended regex matching option, very versatile for regex operations BUT most commonly used to allow you to search for a set of matches (here we look for something & else), separate targets with a single pipe `|`

#### **More useful Commands:**
- `tr`
	[Tr Command in Linux with Examples | Linuxize](https://linuxize.com/post/linux-tr-command/)
	sometimes quicker then sed, can be used to replace/delete characters from standard input to output e.g = `echo "hello" | tr 'hell' 'bell'` output "bello".
	- you can use `tr "\n " "` in any combination to eliminate newlines/spaces e.g. `ls | tr " " "\n"` will print the ls command with each entry on a newline (replaces the spaces)
	*sed v TR*
	- If you need to perform complex text transformations involving patterns or conditions, `sed` is the better choice. 
	-  If you are looking to perform simple character-level replacements or deletions, `tr` is a more lightweight and efficient option.

- `watch [some command here]` - prefacing a command with 'watch' will loop it so that you can continuously monitor output, e.g. `watch kubectl get pods` will watch your k8s pods so you can see events live.

- `tree`: installable utility used to print the directory structure to console as a tree (filepath diagram). Must be installed via package manager.

- `nohup [command] &` - runs a command in the background as a process giving it a PID and appending its output to a file called nohup.out, e.g. `nohup echo 'hi' ; sleep 10 &` runs this in the background (can be seen with ps aux), and appends the output 'hi' to the nohup.out file in the present working directory. will indicate when process terminates.

- `mktemp -d    /     mktemp --tmpdir=/some/path`: used to create a temporary directory (looks something like /tmp/tmp.iZFg0nVSxw), -d will create the directory in /tmp --tmpdir= will place the directory in a specified location. e.g `mktemp --tmpdir="$(pwd)"` will create the directory in the current working directory as specified by the pwd command.
  
- `openssl s_client -connect [serverName/IP:port]`: built-in to most linux systems, can be used to test if target server port is reachable and reports on it, useful for testing stuff like LDAP (389), ssh (22), and database ports (e.g mysql=3306). 
  **Note** that the intention of the `openssl s_client` command is primarily used to test and debug SSL/TLS connections. It connects to a server over SSL/TLS and provides detailed information about the SSL/TLS handshake, including certificates, supported ciphers, and encryption details. This makes it useful for testing **secure connections** (i.e., ports that use SSL/TLS like HTTPS, IMAPS, SMTPS, etc.).
  
- `set -x / set +x` - This command turns on command tracing, which causes the shell to print each command to standard error (stderr) as it's executed, along with its expanded arguments. '-' for on, '+' for off. not super useful in the terminal (unless you're using logic) but very useful for scripts



---


#### **Operation mechanisms:**
The term "operations" encompasses various methods for invoking commands, accessing variables, and performing actions in Bash.

1. **Direct variable referencing** - the most simple operation this simply calls a variable, e.g.
```
somevar = 50 
echo "$somevar" 
#outputs 50
```

2. **subshell execution** - used to run a command in a subshell and return its output e.g.
```
echo date is: "$(date)"

#outputs 'date is: output of the date command here' double quotes around the command are not required but it is best practice and not using them can result in unintended outputs
```

3. **arithmetic expansion** - allows for bash native simple math, note that when you use this you call variables without the preceding $ e.g
```
number=90
echo $((number / 2 + 3))
#outputs 48, divides 90 by 2 and adds 3
```

4. **String manipulation operations** - allows for moderately complex string manipulation
```
teststr=hellogoodbye
echo ${teststr:0:5}
#does a slice from index 0 to 5 (exclusive), outputs hello

echo ${teststr/h/y}
#does a replacement of h to y (sed-like syntax), outputs yellogoodbye
```


---

#### **Quoting:**
**Best practices-**

**double quotes (`"..."`)** - when referencing variables to ensure they expand properly as a single string without splitting on spaces or triggering filename globbing. This is the safest and most common usage in scripts. 
99.99% OF THE TIME WE USE AROUND COMMANDS e.g. `X="$(pwd)"        /         echo "$(pwd)"`

**Single quotes (`'...'`)** - prevent any kind of expansion, making them ideal for literal strings where you don’t want variables or commands interpreted. 

**Unquoted variables** - are only appropriate when you explicitly want word splitting (e.g., for iterating over whitespace-separated items) or filename pattern expansion, but this can lead to subtle bugs if the variable contains spaces or unexpected characters.

**Example**
```bash
filename="My File.txt"
echo $filename     # My File.txt  ← Split into "My" and "File.txt" (can be bad)
echo "$filename"   # My File.txt  ← Correct usage

pattern="*.log"
echo $pattern      # Might expand to matching files (globbing)
echo "$pattern"    # *.log        ← Prevent globbing (safe)

echo "$(pwd)" # echoes output of pwd command
echo '$(pwd)'  #echoes $(pwd) literally

foo="something other else"
for x in $foo; do echo "$x"; done 
^#echoes:
#something 
#other
#else
BUT
for x in "$foo"; do echo "$x"; done
^#echoes: something other else
WHY?
-  the first example--
- The shell expands $foo to: `something other else`
- Then splits it by whitespace into: `something`, `other`, `else`
- So the loop runs 3 times
-  the second example--
- The shell sees "$foo" (with quotes), so it **treats it as a single string**
- No word splitting occurs
- So the loop runs **once**, with one string: `something other else`
```


---

## Logic operations

#### **FOR**
`iterate through a sentance (simple)` -
```
foo="something other else"

for x in $foo; do echo "$x"; done
```
the above iterates through foo (expanded to three strings separated by spaces) and outputs:
something
other
else

`iterate through a list of command outputs ` -
```
```bash
for i in $(find ./*); do echo $i >> file.txt; done
for i in $(find ./*); do echo $i | grep 'mc' ; done
```
the above iterates through every object in the current directory and appends it to a file, we can then operate on this file if needed, we can also directly grep on each object only outputting elements with 'mc'.


#### **while**
`do something indefinitely (while true), must have a 'do' and 'done'`
```
while true ; do echo hello ; sleep 2 ; done
```
the above echoes hello, waits 2 secs, and repeats


---


# Troubleshooting best practices/essential high level commands
I learned this from: [Netflix_Linux_Perf_Analysis_60s.pdf](https://www.brendangregg.com/Articles/Netflix_Linux_Perf_Analysis_60s.pdf)

***Essential troubleshooting comms (first 60 seconds)***
some of these require the sysstat package to be installed, if that is required an * will be next to the command.

- `uname -a ; ip a ; nproc / lscpu` - This is my contribution, this will give you the system name, kernel version, CPU architecture (x86/86_64), and the ip address and network interfaces for the system, and the number of logical cpus on the machine (important for ensuring you know if saturation is occurring). nproc will give a single number, lscpu will give an in-depth cpu summary, up to you which to use.


- `df -h --total`  - shows disk space across devices, -h makes sizes human readable and --total is a flag to provide a full space summary at the end, sometimes this flag is helpful sometimes not.


- `free -wh` - this command prints a memory usage snapshot, the main thing we are looking at here is that 'free' isn't close to 0, ~0 will indicate that we are low/out of memory space. -w is for wide output (more detailed), -h is for human readable.
  

- `uptime` - this command is a way in which you can view the load averages of the system. It will also show you the time the system has been up. Example output (8 core system):
```
$ uptime 
23:51:26 up 21:31, 1 user, load average: 30.02, 26.43, 19.02
```
While the time the system has been up can be a valuable metric the load averages are a very useful tidbit, load averages are the averages of load over 1,5,and 15 minutes. The load averages are given as raw values, and they represent the _number of processes waiting for CPU time_. A load average value of 30.02 means that, on average, there are about 30 processes waiting for CPU resources in the last minute. A value < the amount of logical CPUS indicates that there is no overload and thusly no waiting processes. This is not the best way of checking this, vmstat is better.


- `dmesg -H | tail [-x]` / `dmesg -H | less` - the dmesg command allows us to read system messages, when we say system messages we mean very low level messages from the kernel ring buffer.. Running `dmesg -H | tail ` will give us the last 10 system messages, we can get more by specifying e.g. -20 to get the last 20, piping to less will allow us to use the enter key to scroll through all messages.
  

- **`vmstat -w 1`** - This command provides a powerful way to check memory statistics as they relate to processes. When run with a `1` argument, `vmstat` refreshes the output every second, allowing us to monitor changes over time and catch any anomalies in real-time. `-w` formats the table nicely since it can be a little ugly without it. Here is an example of the output:
```
$ vmstat -w 1
--procs-- --------------memory--------------  ---swap-- -----io-- -system-- -------cpu--------
   r    b     swpd      free      buff    cache   si   so    bi    bo   in   cs  us  sy  id  wa  st
   1    0      0            308        234     1348    0    0     10    1   12   37   0   0  100   0     0   
   0    0      0            308        234     1348    0    0      0     0     77  252  0  0 100   0     0
```

 **Key Columns to Check**:

1. **r** (run queue): - This column shows the number of processes **currently running on the CPU or waiting for CPU time**. These processes are **actively ready** to run and just need CPU time to execute. A value greater than the number of CPU cores indicates that the system is under CPU saturation, with more processes needing CPU time than there are CPUs to handle them.
    
    - **Interpretation**: If the **r** value exceeds the number of CPU cores, it indicates CPU saturation (i.e., too many processes waiting for CPU time).
      
2. **b** (blocked queue): - This shows the number of processes that are **blocked** because they are waiting on I/O operations (e.g., waiting for data from the disk or network, or if a program is trying to access data in memory and the required memory is not available (due to a **page fault**)). These processes are **not ready to execute** until their I/O operations complete.
    
3. **free**:  
    Free memory available in kilobytes. This value shows how much unused memory the system has.
    
4. **si** (swap in) and **so** (swap out):
    
    - **si**: The amount of memory swapped in from disk to RAM.
    - **so**: The amount of memory swapped out from RAM to disk.
    - **Interpretation**: Non-zero values for **si** and **so** may indicate that the system is running low on memory and is swapping processes in and out of disk, which can significantly degrade performance.
5. **us** (user CPU time), **sy** (system CPU time), **id** (idle CPU time), **wa** (waiting for I/O), **st** (stolen time):  
    These columns show how CPU time is being used across all CPUs, broken down into:
    
    - **us**: Time spent executing user-space processes.
    - **sy**: Time spent executing kernel-space processes (system time).
    - **id**: Time the CPU was idle.
    - **wa**: Time the CPU was waiting for I/O (e.g., disk or network).
    - **st**: Time the CPU is stolen from virtual machines (relevant for virtualized environments like Xen or KVM).
    
    **Interpretation**:
    
    - A high **wa** value indicates a disk bottleneck because the CPU is idle while waiting for I/O operations to complete.
    - A **sy** value greater than 20% suggests that the kernel might be inefficiently handling I/O, which may require further investigation into the system's I/O performance.
    - **us + sy** gives the total percentage of time the CPU is actively working on processes (excluding idle and wait time).


- ` ps aux --forest / ps aux -L` - this command lists all the processes running on the system with extreme verbosity, especially as it pertains to commands that invoked them, since this is simply a snapshot it is easy to miss processes that come and go quickly, to explore processes in a more interactive way we use `top`. If we do see something that seems suspicious and we want to analyze the startup command we can use ps with grep to target that particular process. --forest indicates we want a process tree (shows parent/child processes), -L shows we want to see # of threads per process (nlwp).
  

- `top` - this is an enormously useful utility, top gives us two primary areas of information, an upper level which contains cpu and memory information, and a lower level which contains a process table (the top level also has a task state summary and uptime info). unlike some other commands top wont just print output it is an actual utility that you can interact with. when you enter the top utility you should hit '1' and 'e' to open up an entry for each cpu core and to change the process table memory values to something more human readable. you can use arrow keys to scroll up/down/L/R on the process table. A downside to top is that it is harder to see patterns over time, which may be more clear in tools like vmstatand pidstat, which provide rolling output. Evidence of intermittent issues can also be lost if you don’t pause the output quick enough, Ctrl+S to pause, Ctrl+Q to continue, and the screen clears.
	***CPU table stats + meaning:***
	- **us:** Amount of time the CPU spends executing processes for people in "user space."
	- **sy:** Amount of time spent running system "kernel space" processes.
	- **ni:** Amount of time spent executing processes with a manually set nice value.
	- **id:** Amount of CPU idle time.
	- **wa:** Amount of time the CPU spends waiting for I/O to complete.
	- **hi:** Amount of time spent servicing hardware interrupts.
	- **si:** Amount of time spent servicing software interrupts.
	- **st:** Amount of time lost due to running virtual machines ("steal time").
	
	***Process table stats + meaning:***
	- **PID:** Process ID.
	- **USER:** The owner of the process.
	- **PR:** Process priority.
	- **NI:** The nice value of the process.
	- **VIRT:** Amount of virtual memory used by the process.
	- **RES:** Amount of resident memory used by the process.
	- **SHR:** Amount of shared memory used by the process.
	- **S:** Status of the process. (See the list below for the values this field can take).
	- **%CPU:** The share of CPU time used by the process since the last update.
	- **%MEM:** The share of physical memory used.
	- **TIME+:** Total CPU time used by the task in hundredths of a second.
	- **COMMAND:** The command name or command line (name + options).



- * `mpstat -P ALL 1` - mpstat is for diving into cpu statistics, we run -P ALL to specify that we want details for all logical cpus, and 1 to set the command to output every 1 second (to track stats over time). running just `mpstat` will give us the stats of all cpus as one unit. 


- * `pidstat / pidstat 1 / pidstat -p [pid] 1` - the pidstat command allows us to view cpu utilization of processes at a point in time, over time, or cpu utilization of a specific process. just using pidstat gives us a point in time snapshot of all processes running at the current moment and their cpu usage. pidstat 1 gives us a rolling output of processes running + their usage, pidstat -p pid 1, gives us a rolling output for the usage of a particular process.
  
  
- * ` iostat 1 / -xz` - this command is used to get a snapshot of disk statistics (read/write per second, etc.) refreshing each second, -xz gives us highly verbose output.
	look for: 
	- *avgqusz:* The average number of requests issued to the device. Values greater than 1 can be evidence of saturation
	- *%util:* Device utilization. This is really a busy percent, showing the time each second that the device was doing work. Values greater than 60% typically lead to poor performance
	

- * `sar -n DEV 1 / sar 1` - sar with -n DEV will print network interface metrics over 1 second intervals, rxkB/s and txkB/s, as a measure of workload. sar 1 will simply print cpu utilization metrics over 1 second intervals.


- * `sar -n TCP 1`  - prints tcp connection metrics over 1 second intervals, you are primarily looking at:
	-*active/s*: number of locally initiated connections/s
	-*passive/s*: number of remote initiated connections/s
	-*retran/s*: number of retransmissions/s

- `journalctl -xr` - all the above commands (for the most part) checked kernel space information, this command is for user space level logs; it prints the contents of the systemd journal showing service logs with -x giving explanations where applicable, -r shows newest entries first, file can be enormous so we want new first for troubleshooting accuracy
  

## Additional troubleshooting commands

`lsof` - This command shows all open files and what process they are opened by. `lsof -p [pid]` allows us to filter this and check which files are opened by a target process

`fuser -v ./*` - this command find all processes that are associated/using a file or directory (-v gives us verbose output), the above example uses a wildcard to target all files in our directory but we can target specific files by path + name if required

`fuser -v -n tcp [port]` - this command allows us to find all processes that are tied to a specific network socket, e.g replacing port with 22 would return sshd.

`nc -v` - the netcat utility is useful for port scanning/listening examples:
-zv : `nc -zv google.com 443` will ping google.com at port 443, useful for testing open ports, we can also use this for port scanning by specifying a port range e.g `nc -zv localhost 1-10000`
-l : `nc -lv 1234` sets up listening on target port

`nmap [IP]   /    nmap [IP] -p-` - this command (nmap must be installed) invokes the nmap utility to scan a target IP for all open ports (nmap alone does a scan for a commonly used set of ports (quicker), -p- scans all ports (longer/more in depth))

`mtr IP` - mtr is like ping and traceroute had a baby, we get packet/accessibility information plus the hops to the target. very useful

`watch [some command here]`: prefacing a command with 'watch' will loop it so that you can continuously monitor output, e.g. `watch kubectl get pods` will watch your k8s pods so you can see events live.



---


## File System Hierarchy:
   - Linux follows a hierarchical file system structure defined by FHS (filesystem hierarchy standard)
   - Common directories include `/bin`, `/sbin`, `/usr`, `/etc`, `/home`, `/var`, and `/tmp`.
   - Use the `ls` command to list directory contents, `cd` to change directories, and `pwd` to print the current directory.
#### Root Filesystem information:
   - In Linux, the root filesystem, denoted by `/`, is the top-level directory of the filesystem hierarchy.
   - It contains essential system files, directories, and configurations necessary for the operating system to function.
   - Key directories in the root filesystem include:
     - `/bin`: Essential user binaries (commands).
     - `/sbin`: System binaries (commands for system administration sbin = sudo bin).
     - `/etc`: System configuration files.
     - `/home`: Home directories for regular users.
     - `/var`: Variable data, such as logs, spool files, and temporary files.
     - `/tmp`: Temporary files.
     - `/usr`: Contains exes, libraries, and source material for many system programs
     - `/lib`: contains more libraries for executable binaries
     - `/media`: Mount point for removable media
     - There are more root level directories, these are just the primary ones, this guide has information on all the possible root level dirs: https://linuxhandbook.com/linux-directory-structure/


---


## The Linux boot process

1- power on initiates BIOS/UEFI firmware (uefi is more common nowadays) to load from ROM.

2- BIOS/UEFI runs the POST check, simply a check to ensure all hardware is baseline functional and responds to power. POST errors will be piped to the screen/shown through light flashes on the physical device.

2- UEFI (reads from partition table) or BIOS (reads from master boot record), searches for a disk with a boot partition. If a hard drive with a boot partition is found it loads the bootloader from this partition into memory

3- the bootloader (commonly GRUB2) is then responsible for loading the OS kernel into the computers memory

4- once the kernel is loaded is takes control of system resources and begins to initialize the OS. To assist with this it sets up an initial ramdisk which is a temporary filesystem in memory.

5- The initialization process starts by loading device drivers and running secondary hardware checks, then it mounts the root filesystem, next it starts the master init process (often systemd).

6- `systemd` handles loading daemons/services (and starts them if the unit file specifies), configures networking, and low-level tasks, preparing the system to the specified start target (graphical, multi-user, etc.).the OS is now live and the system is ready.



---


## SystemD
**What is it?**
Systemd is one of the most common init systems / system management programs used in modern Linux distributions. It functions as the first userspace process started by the kernel during the boot process (typically PID 1). Systemd is responsible for initializing the system, starting and managing services (via unit files), and coordinating system components like logging (`journald`) and networking (via tools like `systemd-networkd`). and ultimately bringing the system to a specified target state (e.g., `graphical.target` or `multi-user.target`).
While the system is running, systemd continues to manage processes it directly started, supervise services, log events (via `journald`), and provide interfaces for controlling system state — such as shutdown, reboot, or sleep.
**note:**

  -**Child processes** of services are grouped and tracked by systemd via control groups (cgroups), but unless explicitly configured, systemd won't automatically manage or restart individual child processes.
    
  -**User-launched processes** (like ones started from a terminal or desktop environment) are outside the scope of system service management, but can be tracked via `systemd-logind` in terms of session accounting.

## Terminology: 
- **Unit file / unit**
A unit file in `systemd` is a configuration file that describes a resource managed by `systemd` (called a unit), units can be something such as a service (process managed by systemd), mount point, or target. Unit files specify details like dependencies, startup order, and conditions under which a service or target should start. They contain key information for `systemd` to control and manage various system components.
	**unit types:**
		- `.socket`: manages network or IPC sockets and can start services on-demand
		- `.timer`: replaces `cron` for scheduled tasks
		- `.mount`: manages mount points
		- `.target`: grouping mechanism for unit activation states
		- `.path`: watches filesystem paths and triggers services on change
		- `.service`: The most commonly interacted with unit, defines a long-running background process (a service) that systemd should start, manage, and optionally restart if it fails. There are different types of `.service` behaviors: *Type=simple* the default, systemd starts the process and considers it "started" as soon as the main process is launched, *Type=forking* for traditional daemons that fork into the background, *Type=oneshot* for short-lived scripts or tasks

- **Service (daemon)**  
A service (or daemon) is a process that `systemd` can manage, normally loaded at boot. Services may be configured to run automatically at boot or to start upon specific triggers or conditions, like a manual start. These services often run in the background, handling system tasks such as logging or network management.

- **Target**  
A target in `systemd` is a grouping of unit files that allows the system to reach a specific state or mode. Each target defines a boot goal, such as reaching a basic system state, a multi-user state, or a graphical interface. For example, `default.target` is often set to a multi-user or graphical target by default, while `graphical.target` includes all necessary services to support a graphical user interface in addition to standard services.

 - **Drop-in**
 a drop-in is a supplementary configuration file that will override or extend the configuration of a unit file (like a service) without modifying the original unit file itself. If a drop-in contains a section like `[Service]`, it overrides only the settings in that section, not the whole unit. Using drop-ins keeps your changes separate from system-provided files & the changes won’t be overwritten by package updates.
 
## Systemd utilites:
Systemd has a number of utilities that can be used to interact with it, the three most important are `systemctl`, `journalctl`, and `loginctl`

**systemctl:** this command is used to directly manage the core systemd utility, with an emphasis on service management, some important syntaxes are shown below:
- `systemctl` - run raw this command shows a list of all active units adding -all will show all units active/inactive
- `systemctl --all` - shows a list of all units in any status
- `systemctl start/stop/restart/reload [serviceName]` - allows you to start/stop/restart a service, restarting will stop & start the whole process over again, reloading allow the process to remain running and tells the system to re-read configuration files, this is used if you make changes to the service file.
- `systemctl enable/disable [serviceName]` - allows you to enable/disable a service, enabling a service flags it so that systemd starts it at boot, disabling removes this flag
- `systemctl status [serviceName]` - will show the status of a service (active/inactive) and tail its logs, `systemctl status` without specifying a service name will give you a snapshot of systemd's state on the system, stuff like overall state, jobs (pending activities like starting/stopping services), failed units, and control group hierarchy
- `systemctl daemon-reload` - this command will tell systemd to load/reload all configuration files into memory, this is used when we create a new unit file or modify a unit file. Note that nothing is restarted so if we modify an existing unit file the changes will be loaded but the service will still run with the old unit file specifications until it is individually restarted/reloaded or the system reboots and systemd reinitializes all units as part of the boot process.
- `systemctl cat [serviceName]` - shows the unit file contents, where it is located (first comment line) & associated drop-ins.
- `systemctl edit [serviceName]` - This command lets you safely create or edit drop-in files to override or extend an existing unit (like a service) — without touching the original unit file. It opens your default text editor & creates (or edits) a file that looks something like *../override.conf* which targets the unit file and lets you specify only the parts you want to override, such as environment variables, resource limits, dependencies, or commands. To remove the override run: `sudo systemctl revert [serviceName]` It deletes the drop-in and restores the unit to its default state.
  - *Other less-common commands* (systemctl + ) 
	  - `mask / unmask`: masking tells systemctl to symlink the unit file to /dev/null making it unstartable unless we unmask it
	  - `list-sockets`: shows all active sockets managed by systemd
	  - `list-dependencies [serviceName]`: shows unit dependencies


**journalctl:** this utility is used to interact with the systemd logging component, some important syntaxes are shown below:
- `journalctl / journalctl -x -f -r` - `journalctl` shows all systemd logs since init. Note for below, flags can be combined (e.g. -xf):
	- `-x` will make it verbose and add explanations where it can.
	- `-f` will follow the logstream live (a common flag combo is -xf)
	- `-r` will show newest logs first
- `journalctl -u [serviceName]` - shows systemd logs for a particular service, can be combined with other flags either as a stadalone flag or in a combo (e.g. *-xfu*, *-xru*, or *-x -u*, all work)


**loginctl:** this utility is used to interact with the systemd logging login manager component, this lets you manage user sessions and related processes; some important syntaxes are shown below:
- `loginctl` - shows all active login sessions (alias for loginctl list-sessions)
- `loginctl show-session [sessionNumber] -all` - used to gain comprehensive insights into a specific session, such as detailed user information, their session state, and other session-specific properties, session number can be found via the loginctl command. -all ensures even empty values are shown.
- `loginctl show-user [username]` - You can use this command to retrieve all available information about a particular user logged into the system.
- `loginctl list-users -H [hostname]` - Assuming systemd remote management is set up (via systemd-homed or SSH), this command allows you to inspect user information on a remote machine, which is useful for centralized management and monitoring of user sessions across a network or distributed environment without having to log directly into each server. Replace *hostname* with the remote node’s actual hostname or IP address.


---


## kernel / user space

**What is kernel space:**  
Kernel space is the memory area where the kernel (the core of the operating system) operates and manages system resources directly. Processes in kernel space have full, unrestricted access to hardware resources, which allows the kernel to perform low-level tasks such as process management, memory management, and hardware interactions. Only the kernel and its privileged operations execute in kernel space, ensuring system stability and security.

**What is user space:**  
User space is the area of the operating system where user applications run and interact with the OS through GUIs, terminals, or command-line interfaces. Programs in user space operate with limited privileges and access representations or abstractions of kernel-space resources rather than directly accessing them. This separation protects the system by isolating user applications from critical system functions.

**How user space communicates with kernel space:**  
User space communicates with kernel space primarily through system calls (syscalls) — predefined functions provided by the kernel that enable user processes to request services like file operations, process control, or memory management. Common examples include `fork()`, `read()`, `write()`, and `kill()`. This controlled access helps prevent unauthorized or harmful modifications to the core of the OS, maintaining system integrity and security.


---


## File Descriptors
 
In Linux, everything is treated as a file. Because of this, processes have an easy way to interact with system objects. File descriptors (FDs) are non-negative integers used by processes to manage file access, as well as handle input and output. Each process has its own file descriptor table, which is associated with its PID.

The most well-known file descriptors are:

- **0** - stdin
- **1** - stdout
- **2** - stderr

When a new process is created, it inherits the standard-stream file descriptors (0,1,2) from its parent, but it can also open additional files, which are assigned the next available non-negative integer as its file descriptor from its table. The number of file descriptors a process can have in its table is limited and can be viewed with `ulimit -n`.

Since EVERYTHING is a file, FDs can be assigned to ANYTHING that needs to be opened/interacted with: files, sockets, pipes, devices, etc. Even stdin, stdout, and stderr are references to data streams, represented as files (STDIN_FILENO, STDOUT_FILENO, and STDERR_FILENO).

we know the /proc directory contains data/info on all processes so to see the associated file descriptors for a PID we can simply run (to show the links that the file descriptors have made to opened files) ls -l /proc/PID/fd.


---


## Process management
 
 Processes are instances of a running program, the process encompasses the entire execution context (current state, resource usage, etc.). Every process in linux is assigned a unique PID.

**Core process terminology:**
- **Parent:** The process that creates (or spawns) another process. It has control over and can communicate with the child processes it creates.
- **Child:** A process that is created by another process (the parent). It inherits some of the parent’s environment and runs as a separate instance under the parent’s control.
- **Exit Status:** A code returned by a process upon its completion, indicating success or failure. A status of `0` usually signifies success, while any non-zero value indicates an error or specific exit condition.
- **PID:** Stands for Process ID, a unique numerical identifier assigned by the operating system to each running process for tracking and management.

**Process states**:
A process can be in one of 4 states: 
- *Running*: The process is actively using the CPU.
- *sleeping*: The process is waiting for some event (like I/O) or free resources to complete.
- *Stopped*: The process has been stopped, usually by a signal.
- *Zombie*: The process has completed execution but still has an entry in the process table. (More on this below)


**Zombie Processes:**
"When a process dies on Linux, it isn't all removed from memory immediately -- its process descriptor stays in memory (the process descriptor only takes a tiny amount of memory). The process's status becomes EXIT_ZOMBIE and the process's parent is notified that its child process has died with the SIGCHLD signal. The parent process is then supposed to execute the wait() system call to read the dead process's exit status and other information. This allows the parent process to get information from the dead process. After wait() is called, the zombie process is completely removed from memory...This normally happens very quickly, so you won't see zombie processes accumulating on your system. However, if a parent process isn't programmed properly and never calls wait(), its zombie children will stick around in memory until they're cleaned up."[What Is a "Zombie Process" on Linux? (howtogeek.com)](https://www.howtogeek.com/119815/htg-explains-what-is-a-zombie-process-on-linux/)

"Zombie processes don't use up any system resources. (Actually, each one uses a very tiny amount of system memory to store its process descriptor.) However, each zombie process retains its process ID (PID). Linux systems have a finite number of process IDs -- 32767 by default on 32-bit systems. If zombies are accumulating at a very quick rate -- for example, if improperly programmed server software is creating zombie processes under load -- the entire pool of available PIDs will eventually become assigned to zombie processes, preventing other processes from launching"



---


## USE methodology

The USE methodology (stands for **u**tilization, **S**aturation, and **E**rrors) is an established way (akin to a checklist) for performance analysis and linux system troubleshooting.

The USE Method can be summarized as:

> **For every resource, check utilization, saturation, and errors.**

It's intended to be used early in a performance investigation, to identify systemic bottlenecks.

Terminology definitions:

- **resource**: all physical server functional components (CPUs, disks, busses, ...)
- **utilization**: the average time that the resource was busy servicing work
- **saturation**: the degree to which the resource has extra work which it can't service, often queued
- **errors**: the count of error events



---


## Common interview issues and mitigations


**High CPU/Memory utilization** - usually corresponds to a process that is utilizing excessive resources and starving the system.
	**-*Troubleshooting-*** 
	we will see evidence of this issue when using commands like `top` / `ps aux` in the cpu & mem % fields for processes. We can also see evidence of this when running overview commands like `free -h`, `mpstat -P ALL`. We may also see 'out of memory' errors when checking logs via journalctl 
	**-*mitigations*-** 
	> kill the process if non-critical will `kill -9  pid`
	> if its a specific application like docker, database, etc. see if it has memory limits that can be upped/lowered to work within the system constraints
	> supply the system with more memory/distribute the application across multiple nodes (i.e. with k8s)


**Disk space and I/O issues** - characterized by a lack of hard storage causing performance degredation.
	**-*Troubleshooting-*** 
	we will see evidence of this issue when using commands like `df -h` to get storage device availability and abnormal results when running `du -h ` in particular directories. We can also see evidence of this if we run `dmesg` and see filesystem (FS) errors or `iostat -xz 1` and see disk read/write (I/O) waits.
	**-*mitigations*-** 
	> if logging or caching from an application is causing this we can use the `logrotate` utility to specify behavior for compressing and refreshing log directories
	> file system errors may be resolved through running `fsck` on a target partition and remounting. 
	> more storage can be added in to the node


**-----quick terms------**
*full-duplex*  - bidirectional
*half-duplex* - unidirectional
**quick commands-**
`ipcs` - shows shared memory segments, message queus, and semaphores
`mkfifo` - creates a named piped
`kill -l` - lists all signals
`socat` - creates a socket
`ss` - lists network sockets appending `-xln` will list unix domain sockets


---


## IPC (core IPC)
IPC is the method in which processes communicate with each other.

**pipes** - pipes are commonly used for chaining commands, e.g. `cat somefile | grep 'abc'`, pipes pass information from one process to another. They are UNIDIRECTIONAL. Pipes that chain commands are called unnamed pipes, we can also create a named pipe which exists as a file on the filesystem (with the `mkfifo'` command). This allows for a more permanent piped connection

**Shared memory** - Shared memory is a full-duplex IPC mechanism. It is the most efficient method of IPC since it sends no syscall to the kernel. You must be cautious using this because it can cause race conditions (read/writes at the same time). You cannot make this with commands, requires a programmatic approach.

**Message queues** - these differ from application level messaging/queue services. These are created via syscall and thusly kernel managed, they are a First-in first-out way of communication, every read pops the element from the queue. Every read/write is a syscall.


**Signal** - signals are notifications that cause an event (e.g. KILL -9 PID send signal 9 SIGKILL which kills a process, ctrl+c sends signal 2 SIGINTERRUPT), signals can be created by a user, process, or kernel. "crashout" signals 9 & 15 will cause process termination so they can not be created by a process.

**sockets** - sockets (there are a few types) are essentially full-duplex pipes that allow data transfer either internally (unix domain socket) or externally (networks sockets), networks sockets allow for bidirectonal data flow between remote nodes, these are commonly called ports. domain sockets are used for local host communication and allow data  exchange between processes. All

## IPC (synchronization concepts)
**Semaphores** - semaphores are essentially integers that dictate whether a resource can be accessed by a process. semaphores restrict access to a resource to only one process but allow multiple threads to access the resource at the same time. This allows for coordination of resource access.

**Mutex** - a mutex (mutual exclusion) is also a type of resource lock that allows only a single process and a single thread of that process to access a resource at a time, other processes/threads must wait for the mutex lock to release before accessing the resource. 

	^SUMMARY - Mutexes ensure mutual exclusion by allowing only one thread to lock a resource at a time. Semaphores use signaling to coordinate access, enabling multiple threads to share resources.


---

## Curl / Wget
- **curl** (short for _Client URL_) is a powerful, flexible command-line tool used to transfer data between your machine and a remote server using various protocols — most commonly HTTP/HTTPS, but also FTP, SCP, SFTP, SMTP, etc.
	It’s especially useful for interacting with APIs, automating HTTP requests, testing endpoints, and transferring files.


- **wget** is a simple, robust downloader optimized for downloading files, websites, or handling recursive downloads, especially useful in batch operations or mirroring content. It operates on HTTP, HTTPS, FTP, and FTPS protocols

**TLDR;**
- **curl** is better for APIs and scripting.
- **wget** is better for recursive downloads or mirroring.


## `curl`
##### Common curl Use Cases 
##### 1.Downloading Files
```bash
curl -O https://example.com/file.zip      # Save with original name
curl -o myfile.zip https://example.com    # Save with custom name
```

##### 2.Following Redirects (important for short URLs or SSO)
```bash
curl -L https://bit.ly/somefile
```

##### 3.Sending POST Data (Form Submission)
```bash
curl -X POST -d "username=admin&password=1234" https://example.com/login
```

##### 4.Sending JSON to an API (e.g. RESTful endpoints)
```bash
curl -X POST https://api.example.com/v1/data \
  -H "Content-Type: application/json" \
  -d '{"name": "Alice", "email": "alice@example.com"}'
```
the -H is for headers, the -d designates the data to send

##### 5.Authenticated Requests
Basic Auth:

```bash
curl -u user:password https://api.example.com/data
```

Bearer Token (e.g., OAuth2):

```bash
curl -H "Authorization: Bearer <token>" https://api.example.com/secure
```

##### 6.Viewing Only Headers
```bash
curl -I https://example.com
```

##### 7.Uploading Files (via form-data)
```bash
curl -F "file=@/path/to/file.txt" https://example.com/upload
```

##### 8.Verbose Debugging
```bash
curl -v https://example.com
```
#### Flags

| Option         | Meaning                                       |     |
| -------------- | --------------------------------------------- | --- |
| `-L`           | Follow redirects                              |     |
| `-o file`      | Write output to file                          |     |
| `-O`           | Save file with original name from URL         |     |
| `-I`           | Show response headers only                    |     |
| `-H`           | Add custom headers                            |     |
| `-X`           | Specify HTTP method (GET, POST, PUT, etc.)    |     |
| `-F`           | Simulate form submission (useful for uploads) |     |
| `-u user:pass` | Basic authentication                          |     |
| `-d`           | Send data in POST requests                    |     |
| `-v`           | Verbose (show full request/response details)  |     |
| `-s`           | Silent mode (no progress or error output)     |     |


## `wget`
 **wget** is designed mainly for downloading files (especially in batch or recursively)

```bash
# Download a file
wget https://example.com/file.zip

# Save with a specific filename
wget -O newname.zip https://example.com/file.zip

# Resume download
wget -c https://example.com/file.zip
# ^ used if you cancel (pause) with ctrl+C

# Download in background
wget -b https://example.com/bigfile.iso

# Follow redirects
wget --max-redirect=10 https://short.url

# Download all links in a file
wget -i urls.txt

# Mirror a website (recursive)
wget --mirror -p --convert-links -P ./mirror https://example.com
```



---

#### **Linux networking basics**

#### **Linux environment variables **


---

#### links
[The TTY demystified (linusakesson.net)](https://www.linusakesson.net/programming/tty/index.php)

[Linux Performance Analysis in 60,000 Milliseconds | by Netflix Technology Blog | Netflix TechBlog](https://netflixtechblog.com/linux-performance-analysis-in-60-000-milliseconds-accc10403c55)

---
---
---
---





# **Bash scripting**

Start all scripts with:

```bash
#!/bin/bash        < this is non optional
set -euo pipefail   < optional but best practice
set -x                        < for debug as you dev
```

- `#!/bin/bash` is the **shebang**; it tells the OS to use Bash to interpret the script. This avoids unexpected behavior that could happen if the default shell is different (e.g., `/bin/sh` or `dash` on Debian-based systems).
    
- `set -euo pipefail `:  
	- *-e* exits immediately if any command fails
	- *-u* exits if an unset variable is used (helps catch typos and uninitialized values)
	- *-o pipefail* induces a fail if any command in a pipeline fails, pipeline here refers to commands piped together `command1 | command2 | command3`
    
- `set -x`: enables debug mode — each command and its arguments are printed before execution. This helps trace logic, see variable expansions, and find the source of errors. You can turn off tracing later in the script with: `set +x`. you can frame script sections with ' set -x ... set +x ' to target just the section you're debugging.

### Script Debugging Tools
BEST IN MY OPINION
- `shellcheck your_script.sh`: a powerful static analysis tool that checks for syntax issues, bad practices, quoting errors, portability concerns, and more. NOT usually included by default, if you cant install it `bash -n` is a good replacement (See below)

Other Options
- `bash -n your_script.sh`: checks for syntax errors only; it doesn’t execute the script
    
- Run scripts with `bash -x your_script.sh` to enable tracing at runtime without modifying the script
---
---
---
---







# **Debian essentials**
Debian-based distributions, such as Ubuntu, Linux Mint, and others, inherit many of Debian's features and package management system.

#### Helpful information:
 - Use the `man` command to access help manual pages for most commands (e.g., `man ls`).


#### Package Management:
   - Debian-based systems use the Advanced Package Tool (APT) for package management.
   - Use the `apt-get` or `apt` command to install, update, and remove software packages.
   **Core commands:**
     - `sudo apt update`: Update package lists.
     - `sudo apt-get upgrade`: Upgrade installed packages.
     - `sudo apt-get install package_name`: Install a new package.
     - `sudo apt-get remove package_name`: Remove a package.
Installed packages can be found in `/var/cache/apt/archives` APT packages come in the form of .deb files, to install a package from a .deb file we use `dpkg -i name_of_file.deb`


#### Users and Permissions:
**Linux is a multi-user operating system with robust permission control. Each user has a unique username and user ID (UID).**
   - Use the `adduser [username]` command to add new users. for a more simplistic command (not as easy) you can opt to use `useradd`.
   - use the `deluser [username] ; rm -r /home/[username] ` command to delete a user.
   - Use the `addgroup [groupName]` command to add a new group.
   - Use the `delgroup / [groupName]` command to remove a  group.
   - Use the `usermod -aG [groupName] [username]` to add a user to a group & `gpasswd --delete [username] [groupName]` to remove a user from a group
   - Use `groupdel [groupName]` to delete a group.
   - Use the `passwd` command to set or change user passwords.
   - To get all users use: `getent passwd`  (to get all core desktop users you can list the contents of the /home directory), to get all groups use: `getent group`

   - **File permissions are managed using the `chmod`, `chown`, or `chgrp` command.** 
		   - **chmod (Change Mode):**  example -`chmod u+rwx file.txt` grants read, write, and execute permissions to the file owner, you can also use numerical permission denotation e.g. `chmod +777 file.txt` will apply +rwx permissions for all users to file.txt (777 is a bad security practice).
		   - **chown (Change Ownership)**: This command is used to change the owner and optionally the group of a file or directory. For example, `chown john:users file.txt` would change the ownership of `file.txt` to the user `john` and the group `users`.
		   - **chgrp (Change Group)**: This command adjusts the group ownership of a file or directory. For example, `chgrp staff file.txt` would change the group ownership of `file.txt` to the group `staff`.

- **Understanding owners, users, and groups as they relate to Linux permissions:** 
		- **Owner**: The user who created the file or directory. The owner has full control over the file or directory and can modify its permissions.
		- **Group**: Every user on a Linux system belongs to one or more groups. A group is a collection of users who share certain permissions. The group ownership of a file or directory allows all users in that group to have the same level of access to the file or directory.
		- **Users**: Users who are neither the owner nor members of the group associated with a file or directory are classified as "other users." They have a separate set of permissions from the owner and group.

- here is a good reference guide for Linux permission commands/how it all works: https://help.rc.unc.edu/how-to-use-unix-and-linux-file-permissions/



#### Networking:
**Basics:**
- Commands for getting IP addresses (`ifconfig`, `ip a`, and `hostname -i` )
  Use `ifconfig` (if net-tools is installed, if not you can get it using `apt-get install net-tools`), or the `ip a`  (normally built-in, no apt install required) command to display verbose network interface/routing information. If neither `ifconfig` or `ip a` are working you can try `hostname -i` to get the IP address of your device (this command is not verbose and will just give you the primary IP address).
  
-  Troubleshoot network connectivity using tools like `ping`, `traceroute`, `ss`, and `netstat`:
	- **Ping**: Ping checks if a host is reachable by sending ICMP echo requests and measuring the time taken for a response.
	- **Traceroute**: Traceroute traces the route packets take from the source to the destination, showing each hop and the time it takes to reach each node.
	- **Ss**: lists/parses sockets on the host. [ss command in linux - GeeksforGeeks](https://www.geeksforgeeks.org/ss-command-in-linux/#)
	- **Netstat**: Netstat displays network connections, routing tables, information on ports, and interface statistics, helping diagnose network issues and monitor network activity (e.g. `netstat -na` will display connected/listening ports, the most powerful netstat command in my opinion is: `netstat -tulpn` this displays used ports, states, PIDs, and names, this is very very useful). [Netstat command in Linux - GeeksforGeeks](https://www.geeksforgeeks.org/netstat-command-linux/)

**Making temporary system networking configurations:**
you can assign temporary static IP addresses to a network interface via `ifconfig` (if net-tools is installed) or via `ip a` examples:
- **BEST PRACTICE:** `sudo ip a add 192.168.1.100/24 dev eth0` - uses `ip a` to assign a temp static IP to the eth0 interface, you must specify a CIDR block or it will default to /32 (single host network), If there is an already an IP on the interface it will add an additional IP to the interface so that you don't lose connectivity via the primary address.
- **OTHER OPTION** `sudo ifconfig eth0 192.168.1.100 netmask 255.255.255.0` - uses `ifconfig` to assign a temp static IP to the eth0 interface, you must specify a subnet mask or it will default to 255.255.255.0 (/24). If there is already an IP addresses on the interface it will overwrite it so you will lose connectivity if you don't specify default routes as well. 
- To specify a default route you need your default gateway (is probably Subnet_Base.1), you can view it via `netstat -rn` if you currently have an address on the target interface, you can then use `ip route add default via [gateway_Address]` to assign a default route to the gateway.
-  Remove the assigned address with `ip a del [ip/cidr] dev [interface]`.

**Making permanent system networking configurations:**
It is best practice to configure any persistent networking configs through the `/etc/netplan/some_name.yaml` file. Lets look at an example config file:
   ```yaml
network:
  version: 2
  renderer: NetworkManager # sets the network manager to use
  ethernets:
    enp9s0: # Interface to configure
	  dhcp4: false
      addresses: [192.168.0.116/24] # sets a permanent static IP, to set host to use DHCP remove this line and set the dhcp4 line above to true
      gateway4: 192.168.0.1 #optional, sets gateway
      nameservers: #sets DNS nameservers to use, optional
        addresses: [192.168.0.1, 8.8.8.8]
	   routes: #sets default route, optional
         to: default
         via: 192.168.122.1
```
Once the file is configured to your networking specifications use `sudo netplan generate`
to load the plan, `sudo netplan apply` to apply the plan to the system, and then reboot the computer to finalize the changes



#### Mounting Devices:
- Mounting is the process of attaching a filesystem to a directory in the Linux directory tree.
- Filesystems can be mounted with various options, such as read-only (`ro`) or read-write (`rw`), and with specific permissions and settings.
- It is best practice to set permanent mounts in the */etc/fstab* file.
- **`mount` and `umount`:** Used to mount and unmount filesystems. 
- For example, to mount a network share on Ubuntu you would edit the */etc/fstab* file on the host to set up a CIFS (common internet filesystem) mount by adding a line like this (here we assume the share is hosted from 192.168.1.2 and has the letter of P):
```
//192.168.1.2/P/   /folder_to_mount_to   cifs    username=winuser,password=TopSecret   0    0
```
then we would run `mount -a` to mount all devices in fstab, we don't have to specify the password, if we don't we will be asked when we run the command.




---
---
---
---








# **RHEL essentials**
...in progress

---
---
---
---








# Other

OSI/TCP-IP

port knocking for security

`nohup command &` - puts the command in the background as a process (nohup sets no output + process, & commits to background)
`command &> somefile` sets command to output to somefile not to terminal




# INTEGRATE IN...

ADD exec command() - [bing.com/ck/a?!&&p=3390f0fd7c006bdea4672d562be4f6055433186a9e8877add8130d0361879f20JmltdHM9MTcyNDI4NDgwMCZpZ3VpZD0zNDdiZWFiMS00NWU5LTZiYTYtMzQ0ZS1mZTEzNDQzZjZhZWEmaW5zaWQ9NTUxOA&ptn=3&ver=2&hsh=4&fclid=347beab1-45e9-6ba6-344e-fe13443f6aea&psq=what+is+exec+in+bash&u=a1aHR0cHM6Ly9zdGFja292ZXJmbG93LmNvbS9xdWVzdGlvbnMvMTgzNTExOTgvd2hhdC1hcmUtdGhlLXVzZXMtb2YtdGhlLWV4ZWMtY29tbWFuZC1pbi1zaGVsbC1zY3JpcHRz&ntb=1](https://www.bing.com/search?pglt=673&q=what+is+exec+in+bash&cvid=1d4bb09633e646d384c71a9a0701669d&gs_lcrp=EgZjaHJvbWUyBggAEEUYOTIGCAEQABhAMgYIAhAAGEAyBggDEAAYQDIGCAQQABhAMgYIBRAAGEAyBggGEAAYQDIGCAcQABhAMgYICBAAGEAyCAgJEOkHGPxV0gEINzM3OWowajGoAgCwAgA&FORM=ANNAB1&PC=U531)

alias = sets a different name for a command / sequence of commands
[How to Create Bash Aliases | Linuxize](https://linuxize.com/post/how-to-create-bash-aliases/)
[How to list all aliases on Linux - Linux Tutorials - Learn Linux Configuration](https://linuxconfig.org/how-to-list-all-aliases-on-linux)
to list all aliases : `alias -p`
add an alias: `alias alias_name="command_to_run"`
remove an alias `unalias [name]`

ADD info about chaining commands with ';' (for chains that do not require all the previous command to complete successfully ) and with '&&' (for chaining commands that require the previous command to execute successfully)


SED
sed replace is (-i replaces permanently in file, no -i only replaces in output) `sed -i 's/something/somethingElse/(g if you want global [all instances])'

sed delete : `sed '1d` specify line number + d to delete that line

TR
[Tr Command in Linux with Examples | Linuxize](https://linuxize.com/post/linux-tr-command/)
can be used to replace/delete characters e.g = `echo "hello" | tr 'hell' 'bell'` output "bello" or you can use `tr "\n " "` to eliminate newlines in a file e.g. `ls | tr " " "\n"` will print the ls command with each entry on a newline

sed v TR
- If you need to perform complex text transformations involving patterns or conditions, `sed` is the better choice. 
-  If you are looking to perform simple character-level replacements or deletions, `tr` is a more lightweight and efficient option.



tricks:

 combining multi-command output in the same line, use **echo -n** e.g. `echo -n "$(pwd)" ; echo "/somefile"` to display the pwd + custom filename on the same line.

add setfacl for adding access to a directory .../ file?

Strace - traces system calls for specified command: e.g. *strace ls*

can be commed like: *while true; do echo hello; done*

**env** - lists env vars
