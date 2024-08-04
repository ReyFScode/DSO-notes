# **Ansible Guide**
Ansible is an open-source agentless ( doesn't require clients on target systems, works over connection protocols like ssh ) automation tool that simplifies the process of managing and configuring systems, applications, and infrastructure.  It follows a declarative approach, allowing users to define the desired state of their systems using simple and human-readable YAML files BUT we can use imperative declaration (exact steps as opposed to a desired state) to perform actions as well. The best way I've heard it described is that ansible wants to be fully declarative but almost always has some imperative elements. Ansible automates repetitive tasks, enabling efficient management of large-scale environments with ease.

One of the best use cases for Ansible is configuration management. It allows you to define and enforce the desired configuration state of your systems, ensuring consistency across multiple servers. With Ansible, you can easily define and apply configurations for various aspects, including packages, services, files, users, and more. This makes it ideal for maintaining a standardized and reproducible infrastructure, reducing manual effort and minimizing configuration errors.

Another key use case for Ansible is application deployment and orchestration. Ansible's playbooks allow you to define complex workflows and automate the deployment of applications across multiple servers. It simplifies the process of installing dependencies, configuring application settings, and managing the entire deployment lifecycle. Ansible's idempotent nature ensures that the desired state is achieved consistently, making it suitable for both initial application deployments and ongoing updates.

Infrastructure provisioning is also a notable use case for Ansible. It integrates with cloud providers, allowing you to automate the creation and configuration of cloud resources such as virtual machines, networks, and storage. Ansible's flexibility allows you to define infrastructure provisioning in a standardized manner, regardless of the underlying cloud platform. This makes it easier to manage and scale infrastructure deployments across different environments, whether in the cloud or on-premises.



---

# **Key Terms:**
- **Playbook**: A YAML file that contains a sequence of tasks, defining what actions should be taken on managed nodes. Playbooks serve as the foundation for automation in Ansible.
- **Task**: A unit of work in a playbook that defines a specific action to be performed, such as installing a package or restarting a service.
- **Inventory**: A list of managed nodes and their characteristics. Inventory can be static (manually defined in files) or dynamic (generated on-the-fly by scripts or plugins).
- **Module**: Small, reusable units of code that Ansible uses to perform tasks. Modules are idempotent, ensuring that they make changes only if necessary.
- **Role**: A collection of playbooks, variables, and other files organized into a directory structure. Roles are used for modularizing and reusing automation code.
- **Fact**: System information about managed nodes collected by Ansible and available as variables. These facts can be used in playbooks to make decisions.
- **Handlers**: Special tasks defined in playbooks that are only executed when triggered by another task, typically used for restarting services after configuration changes.



---

# **Running playbooks**
you use the `ansible-playbook` command to run a playbook you can specify flags to customize how the playbook is run, some examples are:
```yaml
ansible-playbook somePlaybook.yml #runs a playbook with nothing specified. add -v, -vv , -vvv to determine output verbosity.

ansible-playbook -i ./path/hosts somePlaybook.yml # -i specifies a path to a hosts file if it isnt specified it looks in the /etc/ansible dir for one.

ansible-playbook -Kk somePlaybook.yml # -Kk (capital K followed by lowercase k) will prompt you for the ssh and become (sudo) password when the run occurs.

ansible-playbook -u USERname somePlaybook.yml # -u allows you to specify the user to run the playbook as.

ansible-playbook -e "my_var=hello" somePlaybook.yml # -e allows you to set variables at runtime, each var must have its own -e preceding it.

ansible-playbook somePlaybook.yml --tags sometag, someothertag # tags allow you to specify only certain plays in the playbook (they are described via YAML on a play by play basis), --tags allow you to choose the tags you want to run.
```

---

# **Ansible.cfg Files:**
- The **`ansible.cfg`** file is the central configuration file for Ansible.
- Ansible reads this file to determine various configuration settings, such as the location of inventory files, roles path, and module settings.
- Using a centralized `ansible.cfg` file helps maintain consistent configurations across projects.
- Your `ansible.cfg` file does not need to be in the same directory as your playbook. The `ansible.cfg` file is a global configuration file for Ansible, and it can be located in several places, with the following order of precedence:

1. In the current working directory where you run Ansible commands. If you place an `ansible.cfg` file in the directory from which you run Ansible commands, it will take precedence for those commands.
    
2. In the user's home directory. If there is a `~/.ansible.cfg` file in the user's home directory, it will be used as a global configuration file for that user.
    
3. In the `/etc/ansible/` directory. If there is an `/etc/ansible/ansible.cfg` file, it will serve as a system-wide default configuration.

You can choose the most appropriate location for your `ansible.cfg` file based on your needs. Placing it in the same directory as your playbook is not required and is less common. Typically, users place their `ansible.cfg` files in their home directory or in the system-wide configuration directory to provide consistent settings for all their Ansible projects.

**Example .cfg file + common configurations**
``` yaml
[defaults] # begins with defauls
host_key_checking = False  #sets host key checking to false for ssh
```


---


# **Host/inventory Files**
- **Inventory files** in Ansible serve as the source of truth for the list of managed nodes.
- Static inventory files list hosts manually, including their IP addresses, hostnames, and other attributes.
- Dynamic inventory scripts generate the inventory dynamically based on various data sources (e.g., cloud providers, databases).
- Inventory files can also define host groups, which allow you to target specific sets of hosts with your playbooks.
- The hosts file does not need to be in the same directory as your Ansible playbook. The location of the hosts file is configurable and can be specified in your Ansible configuration.
- They are often called *Hosts* or *inventory.ini* Best practice is to call it **Hosts**

	By default, Ansible looks for the hosts file in the `/etc/ansible/hosts` location on the control machine. However, you can specify a different location for your hosts file using the `-i` or `--inventory` command-line option when running Ansible commands or by setting the `inventory` configuration parameter in your Ansible configuration file (`ansible.cfg`).
	
	For example, to use a hosts file located in a different directory, you can specify it like this when running an Ansible playbook:
```bash
ansible-playbook -i /path/to/your/hosts/file playbook.yml
```

Or, you can set it in your `ansible.cfg` file:
```ini
[defaults]
inventory = /path/to/your/hosts/file
```
This allows you to keep your hosts file separate from your playbooks, which can be helpful for organizing and managing your Ansible inventory.

The best practice for hosts files is to specify the name of each host in a group followed by ansible_host like this (ansible host declares the host to connect to, the name preceding it is the name you give the host, it is best practice to do both):
```hosts-file-example
[stack]
Stackhost ansible_host=10.1.20.84
```
You can also do it like this if you just want to group a list of nondescript IP's:
```hosts-file-example
[stack]
10.1.20.84

or like this (better form):
ansible_host=10.1.20.84
```
You can apply host specific variables to a whole group in your hosts file by specifying a vars section for each group like this (example for linux), its best practice to specify at least the ansible user in a hosts var section.
```hosts-file-example
[stack:vars]
ansible_user=some-user
ansible_ssh_pass=some-password  # password for initial ssh connection
ansible_become_pass=some-password # sudo password
```

**Windows Hosts vars**
you have to configure some specific vars for windows host groups
```
[win_host:vars]
ansible_connection=ssh # best practices indicate using ssh as the connection default
ansible_shell_type=cmd # best practices indicate using cmd as the shell default
ansible_user=someUser
ansible_ssh_pass=somePass # because windows has no sudo equivalent and ssh will always open a shell using the highest privs available no become_pass is needed
```


---


# **Using Variables**
Variables in Ansible are used to store and manage data that can be used within playbooks and templates.

Variables can be defined at various levels:
  - **Playbook-level variables**: Defined within the playbook itself.
  - **Inventory-level variables**: Defined in the inventory (hosts) file or in a separate vars file.
  - **Role-level variables**: Included within roles to make them reusable.
- Ansible uses **Jinja2 templating** to interpolate variables in playbooks and templates.
Remember:
- Variable precedence is important; Ansible resolves variable values based on a specific order, with higher precedence variables overwriting lower ones.

#### Variable Example:
- Define a variable in a playbooks top level section:
  ```yaml
  vars:
    my_variable: "Hello, Ansible!" 
    # when declaring like this you must use : as the seperator
  ```

- Define a variable in an external vars file, usually best to make it a .yml: 
  ```yaml
# this is an external file called vars.yml
  variable_in_file: "magic value"
  ```

^ Example: Access and use the above variables in a playbook
  ```yaml
  
- name: test_Play
  hosts: localhost
  connection: local
  
  vars:
    my_variable: "Hello, Ansible!"
  vars_files:
    - /path/to/vars.yml
 
  tasks:
    - name: print the variables
      ansible.builtin.shell:
            cmd: echo {{my_variable}} ; echo {{variable_in_file}}
         ```


- You can also define a variable when you run the playbook command using the -e flag:
  ```yaml
ansible-playbook -i inventory.txt -e "my_var=hello" playbook.yaml
  ```

- You can apply host specific variables to a whole group in your hosts file by specifying a vars section for each group like this (best practice is to separate with =).
```hosts-file-example
[stack:vars]
User + password vars here...
other_Var='value'
other_Var2='other value'
```

- Finally we can register the output of tasks as a variable with the `register` task component:
```
     - name: Run a shell command and register its output as a variable
       ansible.builtin.shell: echo "hello world"
       register: foo_result

     - name: access the variable set above
       ansible.builtin.shell: echo "{{ foo_result }}"
```


variable docs: [Learn how to use Ansible variables with examples – 4sysops](https://4sysops.com/archives/learn-how-to-use-ansible-variables-with-examples/#:~:text=You%20can%20pass%20variables%20via%20the%20command%20line,variables%20when%20running%20your%20playbook%20or%20ad-hoc%20commands.)


---

# **Leveraging Magic variables**
In Ansible, magic variables, also known as "facts," are predefined variables that provide information about the system, the environment, and the configuration of the managed hosts. These variables can be accessed within playbooks and templates, allowing you to make your Ansible playbooks more dynamic and adaptable to different environments. Magic variables are automatically set by Ansible based on the information gathered during the playbook execution.

Here are some key magic variables in Ansible, particularly those related to passing values from the hosts into the playbook:

1. **`{{ inventory_hostname }}`**:
   - This variable contains the name of the current host as defined in the inventory.

2. **`{{ inventory_hostname_short }}`**:
   - Similar to `inventory_hostname`, but only contains the short form of the hostname.

3. **`{{ inventory_hostname.split('.')[0] }}`**:
   - Extracts the first part of the hostname, useful for getting the hostname without the domain.

4. **`{{ inventory_hostname in groups['group_name'] }}`**:
   - Checks if the current host is a member of a specific group.

5. **`{{ inventory_hostname | regex_replace('search_pattern', 'replace_pattern') }}`**:
   - Allows you to perform a regex replacement on the hostname.

6. **`{{ hostvars[inventory_hostname]['variable_name'] }}`**:
   - Retrieves the value of a variable defined on the host. This can be useful when you want to access host-specific variables.

7. **`{{ groups['group_name'] }}`**:
   - Returns a list of all hosts in a specific group.

8. **`{{ ansible_hostname }}`**:
   - Contains the system's hostname as reported by the Ansible `setup` module.

9. **`{{ ansible_distribution }}`**:
   - Returns the distribution name of the OS (e.g., Ubuntu, CentOS).

10. **`{{ ansible_distribution_version }}`**:
    - Returns the version number of the OS distribution.

11. **`{{ ansible_os_family }}`**:
    - Returns the operating system family (e.g., RedHat, Debian).

These magic variables can be used within your playbook to make it more flexible and adaptable to different host environments. For example, you might use them in conditional statements or to dynamically generate configuration files based on the characteristics of each host.

#fleshthisout


---

# **Conditionals/logic + blocks:**
We can use basic conditional statements & logic to control how our playbook runs.

**'When' in tasks**
We can use the `when: ` keyword in our task definition to define when a task should run. This is normally used in conjunction with the `register: ` keyword to make a task contingent on some output from another task, for example:

```YAML

#This first task checks whether a file exists on the target computer and registers the output as some_variable
tasks:
  - name: Check that a file exists
    stat:
      path: /etc/file.txt
    register: some_variable 
    
#to see the particular returned value(s) of a module (can differ per module), to check if our output based logic is sound, we can use debug
- name: Prints var value
  ansible.builtin.debug:
    msg: "{{ some_variable }}"
    
#now we have tasks that respond to the value of the variable we registered, many tasks have specific return values that can be actioned upon, stat has var.stat.exists as one of its option that returns 'true' or 'false' if the file exists

- name: Prints value if file exists
#just like in programming if we just quiery the var and the value is "true", it continues but we could also do when: some_variable.stat.exists == true
  when: some_variable.stat.exists 
  ansible.builtin.debug:
    msg: '{{ some_variable }}" exists'
    
- name: Prints value if file doesnt exist
#NOT sets the desired value as false, we could also do when: some_variable.stat.exists == false
  when: NOT some_variable.stat.exists 
  ansible.builtin.debug:
    msg: '{{ some_variable }}" doesnt exist'

```



**Blocks**



---
# **Roles**
#ADDrolesinfo [How to Use Ansible Roles to Abstract your Infrastructure Environment | DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-use-ansible-roles-to-abstract-your-infrastructure-environment)

Ansible roles are a way we can organize playbooks and create reusable task-sets at the same time. Roles are essentially groups of tasks placed together. 
in our ansible directory (add how to customize location) we create a folder "roles", in this folder we create a folder (named whatever the role should be called e.g. some_role), and inside that a folder called tasks. so we have /etc/ansible/roles/some_role/tasks , inside the tasks folder we put a file called main.yml, we can input the tasks starting at space 0, this is useful because it allows you to not have to worry about spacing + allows for you to create reusable task modules.

```yaml
# /etc/ansible/some_Role/tasks/main.yml
- name: Reboot machine
  ansible.windows.win_reboot:
    test_command: echo 1

- name: Reboot machine
  ansible.windows.win_reboot:
    test_command: echo 1

- name: Reboot machine
  ansible.windows.win_reboot:
    test_command: echo 1

```

We call roles by adding it to the top level of the ansible play, roles will be executed before tasks, you can run roles without specifying additional tasks:
```
- name: test_Play
  hosts: localhost
  connection: local

  roles:
	  - some_role
  tasks:
 
```






---

# **Template Files:**
- **Jinja2 templating** is used in Ansible for rendering dynamic content.
- **Template files** are text files with placeholders (variables) that get substituted with actual values when used.
- They are commonly used with the `template` module to generate configuration files, scripts, and other text-based content.
- Template files have the `.j2` extension to indicate they contain Jinja2 markup.
- Think of it like this, templating is the act of replacing variables, so if you have a .env file that needs a particular value for your program to run, you can template a file called env.J2 file (which will make the .env file by replacing all variables in it and copying it over, see below example)

#### Template Example:
- Create a template file (e.g., `config.j2`) with variable placeholders:
  ```jinja2
  Welcome message: {{ welcome_message }}
  ```

- Use the `template` module in a playbook to render the template:
  ```yaml
  tasks:
    - name: Create configuration file
      template:
        src: config.j2
        dest: /etc/myapp/config.txt
  ```

- Pass variable values when running the playbook (or in hosts/in the play configuration section), however you specify the vars, values will be input for the templated file:
  ```bash
  ansible-playbook my_playbook.yml -e "welcome_message=Hello, World!"
  ```



---

# **Helpful info/Examples:**

#### **Basic setup for plays**:
```
---
#top level section, defines play configs/specs

- name: sample     #name of play (necessary)
  hosts: hosts     #hosts to target (necessary)
  become: yes      #become superuser for  linux (if required)
  vars:            #specify vars with -- name: "value" (if required)
    my_variable: "Hello, Ansible!"
  module_defaults:  #specify module defaults (not necessary)
    ansible.builtin.setup:
      gather_timeout: 60

  tasks:  #specify task secion (necessary)
    # tasks go here
```
Plays have a top level section that contains the names and specifications for the play, stuff like if it should become the superuser, vars, time to gather facts, etc. and a tasks section, under the task section are where the particular play tasks go.

#### **Module defaults & ansible built-in setup**
```
- name: test play
  hosts: test_hosts
  
  module_defaults:
    ansible.builtin.setup:
      gather_timeout: 60
      
   tasks:
```
The `module_defaults` is an optional section of an Ansible playbook which allows you to specify universal playbook-wide default values for certain parameters that are used by multiple modules. In this case, you are setting the `gather_timeout` parameter for the `ansible.builtin.setup` module to 60 seconds. This means that the `setup` module will wait for up to 60 seconds for the remote host to respond before it times out. 
The `ansible.builtin.setup` module is used to gather information about a remote host. This information can then be used by other modules in the playbook. The `setup` module is always called, even if it is not explicitly defined in the playbook.

#### **Turn Off Gathering Facts**
```
- name: test play
  hosts: test_hosts
  gather_facts: false
```
If you don't want any facts gathered for a particular play you can specify this in the play configuration section with `gather_facts: false`, there are some bugs with certain windows versions and ansible fact gathering so this can be useful to resolve those issues.


---

# **commonly used modules:**
#### ansible.builtin.shell
**A bug in some ansible versions doesn't allow you to use the full name "ansible.builtin.shell:", If it doesn't work just use "shell:"**
 This module is used to execute shell commands on target Linux hosts. 
1. **`cmd: >` (folded style):**

   When you use `>`, it is called the "folded style" or "block scalar" style. In this style, newlines in the command are folded into spaces, and leading whitespaces are removed. This is useful when you have a long command that you want to write in a more readable way without including actual newline characters in the command. It's particularly handy for creating cleaner YAML syntax.
   Example:

   ```yaml
  shell:
     cmd: >
       echo "This is a
       multiline
       command"
   ```
   This will be equivalent to:
   ```bash
   echo "This is a multiline command"
   ```
2. **`cmd: |` (literal style):**
   When you use `|`, it is called the "literal style" or "block scalar" style. In this style, newlines and leading whitespaces are preserved as they are written. This is useful when you want to maintain the exact formatting of your command, including line breaks.
   Example:
   ```yaml
  shell:
     cmd: |
       echo "This is a
       multiline
       command"
   ```
   This will be equivalent to:
   ```bash
   echo "This is a
   multiline
   command"
   ```
In summary, the choice between `>` and `|` depends on whether you want to fold the newlines and remove leading whitespaces (`>`) or preserve the exact formatting of your command (`|`). Usually you will use ( > ).

conversely, you don't have to use either of these and can just do:
   ```yaml
   ansible.builtin.shell:
     cmd: command goes here
   ```


#### ansible.windows.win.shell
 This module is used to execute shell commands on target windows hosts. 
   ```yaml
  tasks:
    - name: test
      ansible.windows.win_shell: |
         echo 'hello'
      args:
        executable: cmd
   ```
   This will display "hello" and use the command prompt as the shell to execute in, you can use PowerShell by changing the executable argument to `executable: powershelle.exe / powershell.


#meta end_play & end_host