# **Linux notes (Debian + RHEL) + Bash scripting**
---
# **General knowledge (any distro)**
All of these concepts will (99%) be the same on any Linux distro you use.

#doublecheckthegeneralknowlsedgeacrossRHEL&ubuntu

#how to gather apt packs for offline (apt-rdepends pass to apt-get / .../cache/apt)

#sourceVbash : [linux - What is the difference between executing a Bash script vs sourcing it? - Super User](https://superuser.com/questions/176783/what-is-the-difference-between-executing-a-bash-script-vs-sourcing-it)
#### **Basic Commands / operators:**
- `ls`: List directory contents, specify `-al` for a more detailed view.
- `du -h`: Displays the disk usage of files and directories (in human readable format). Example: `du -h /path/to/directory` displays disk usage of a target directory  in human-readable format.
- `df -h`: Shows the amount of disk space available on the host filesystem (in human readable format). Example: use `df -h` to display host disk stats in human-readable format.
- `cp [filename] [/target/dir]` : Copy files and directories, specify -r for directories.
- `mv [filename] [/target/dir/filename]`: Move or rename files and directories.
- `rm [filename]`: Remove files and directories,  for directories add -r after *rm*.
- `mkdir [dir_name]`: Create directories.
- `touch [filename]`: Create new file.
- `pwd` : Prints the current working directory
- `lscpu` : Prints details about the system processor architecture. 
- `echo "something here"`: Prints text to stdout (console).
- `cat / cat -b` : gets the contents of a file, -b is used to add line numbers

- `>> / >`: Operator used to pipe output to a file (called output redirection, can be an existing file or new file) can be used with commands or with echo. It is important to note that **>>** will append text to the end of the file whilst **>** will overwrite the file so be careful. 
	e.g.:
	- `echo "hello world" >> new.txt` will append "hello world" to a file called new.txt, if it doesn't exist new.txt will be created.
	- `ls -al > new.txt` will overwrite the contents of new.txt with the output of the *ls -al* command, will create a new file if there is no new.txt.
	
- `<` : this operator is used for input redirection, while > pipes output from a command to a file, this output passes file contents to a command, for instance instead of having to do `cat test.txt | grep "something"` to search for 'something' in the test.txt file we can just do `grep "something" < test.txt` saving us some characters

- `|` : This is called the pipe operator, this is used to pass the output value of one command to another.
	e.g.: 
	- `lscpu | grep -i vulnerability | sed 's/a/b/g'` will take the output value of *lscpu* and pass it to the *grep -io* command which will do a case-insensitive search for any lines with "vulnerability" it will then pass this parsed output to the sed command and replace 'a' with 'b' giving you a final output of  all lines with the world "vulnerability" with all 'a' characters replaced with 'b'.


#### **Text Editors:**
   - Linux offers various text editors for editing configuration files, scripts, and documents.
   - Common text editors include Vi, Vim, Nano, and Emacs. The core editors you will most likely encounter are Vi/Vim and Nano.

**Core Editor usage:**
   - **Vi/Vim:** Vi is not fun, you can use the same save/discard instructions as you would for vim but if you have to pick between Vi and Vim....use Vim...take my word on that. Install vim using `apt-get install vim/yum install nano`(distro dependent).  Use `vim [filename]` command to open Vim editor. Press `i` to enter insert mode, make changes, then press `Esc` followed by `:wq` to save and exit, or use `Esc` followed by `:q!` to discard changes.
   - **Nano:** This is probably the most user friendly editor. Install nano using `apt-get install nano/yum install nano` (distro dependent). After installation use `nano [filename]` to enter the nano editor, use `ctrl+x + y` to save changes and `ctrl+x + n` to not save.


#### **Linux networking basics**



#### **Linux environment variables**




#### **User space and kernel space**


#### **Subshell execution / variable expansion**
"" v ''
$()




#### **-----Intermediate/Advanced Concepts:------**
#add-syscalls

##### **Interprocess communication (IPC)** (flesh out a lil more, for clarity, study a little more)
There are a handful of ways that Linux facilitates IPC, we will go over two primary ways here. For an awesome guide on all things IPC use this link: [https://tldp.org/LDP/tlk/ipc/ipc.html](https://tldp.org/LDP/tlk/ipc/ipc.html#:~:text=Processes%20communicate%20with%20each%20other%20and%20with%20the,T%20M%20release%20in%20which%20they%20first%20appeared.)
 1. **Pipes:**
If you remember from above this symbol `|` is a pipe. When are stitch commands together with this symbol you are creating what is called an anonymous pipe. Pipes are one of the simplest forms of IPC in Linux. They allow communication between two related processes, typically a parent process and its child process. Pipes are unidirectional, meaning data flows in one direction. There are two types of pipes: anonymous pipes and named pipes (FIFOs). Anonymous pipes are created using the `|` operator and exist as long as the processes communicating through them are alive. Named pipes, on the other hand, are created using the `mkfifo` command and persist even after the processes that created them terminate.
2. **Sockets:**
Sockets provide a more versatile form of IPC, allowing communication between unrelated processes, either on the same machine or across a network. There are two types of sockets:  TCP/IP sockets (stream sockets (TCP) and datagram sockets (UDP), and raw sockets) and UNIX sockets. TCP/IP sockets provide reliable network connection-oriented communication, while UNIX sockets provide Interprocess communication on the same machine. Sockets are created using the `socket()` system call and can be used with various other system calls like `bind()`, `listen()`, `connect()`, `accept()`, `send()`, and `recv()`. *reference*: https://www.baeldung.com/linux/unix-vs-tcp-ip-sockets

>**Named Pipes V sockets for IPC on the same host:** 
>**FIFO pipes** are chosen for simpler, **unidirectional** data streaming tasks where ease of use and simplicity are paramount.  **Unix sockets** are utilized when you need advanced IPC features, bidirectional communication, or better performance for complex data transfer scenarios.



##### **processes and signals**
A process is defined as a running instance of a program,  we can view processes by running the `ps` command with varying flags `ps -aux` is usually the standard (all processes with a high level of detail), but to get specific process we specify `ps -C [name e.g systemd] `. Signals are sent to processes to invoke an action, the commonly used ones are SIGKILL invoked with `kill -9 [pid]` which forcefully kills a process, or SIGINT invoked with ctrl+c which interrupts a program and requests a graceful termination.




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



#### File System Hierarchy:
   - Linux follows a hierarchical file system structure.
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





#### The holy trinity: Sed, Grep, and Awk:
 1) **`awk` (Text Processing):** A versatile tool for pattern scanning and processing.
      - Example: `awk '{print $1}' file.txt` to print the first column of a text file.
2) `sed` **(Stream Editor)**: Used for filtering and transforming text.
      - Example: `sed -i 's/old/new/g' file.txt` to replace all occurrences of 'old' with 'new' in a file. If you dont specify `-i` the changes wont be written, not specifying `-i` first is a good way to test your replacement to ensure its performing the desired behavior before actually writing changes.
3) `grep` : Used to search for text in output/files, output can be tailored with flags.
	Some useful grep flags (you can combine these for highly customized outputs):
	- `-i`: case insensitive, ignores case, `grep -i "bob`" will find both "BOB" & "bob"
	- `-o`: only, matches only the output vs the line of origin. e.g with a file that has the contents "my name is bob" running `grep "bob" somefile.txt ` will output the line that contains bob: "*my name is bob*", specifying `grep -o` will only output "bob"
	- `-e / -E`: regex or extended regex matching option, e.g with a file that has the contents "*my name is bob*" running `grep -oe "i.*b" somefile.txt ` (notice the combo of o + e)  will only output "*is b*" (.* matches everything from i to b).
	- `-v` : regex to match everything EXCEPT what is specified e.g. if you have a file with the contents: 
		**bob** 
		**joe** 
		**sam** 
		**bob1**
		**fred**
		the command: *cat file.txt | grep -v "bob"* will output "joe sam fred" omitting everything that includes "bob." This is useful for when you have a massive amount of data and need to quickly remove a set of values. This only works with this standalone flag on a set of data separated by newlines.




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
	- **Netstat**: Netstat displays network connections, routing tables, information on ports, and interface statistics, helping diagnose network issues and monitor network activity (e.g. `netstat -na` will display connected/listening ports). [Netstat command in Linux - GeeksforGeeks](https://www.geeksforgeeks.org/netstat-command-linux/)

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

# **RHEL essentials**
...in progress


---

# **Bash scripting**
...in progress

---



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