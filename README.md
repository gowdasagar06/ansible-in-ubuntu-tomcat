Ansible Intro
What is Configuration Management
How Ansible Works?
Ad Hoc commands
Parallelism and Shell Commands
File Transfer
Managing Packages
Gathering Facts
Playbooks
Playbook Structure
Create a Playbook      
The Different YAML Tags
Roles
Creating a New Role
Utilizing Roles in Playbook
Breaking a Playbook into a Role
Ansible – Variables
Exception Handling in Playbooks
Loops
Blocks
Conditionals
Ansible – Advanced Execution
How to Limit Execution by Tasks
How to Limit Execution by Hosts


Ansible Intro

Ansible is an open-source automation and configuration management tool that simplifies and streamlines various IT tasks, including software provisioning, configuration management, application deployment, and task automation.

Brief introduction to Ansible

Agentless Automation: Ansible is an agentless tool, which means it doesn't require any agent or additional software to be installed on the managed hosts. Instead, it relies on SSH (for Unix-like systems) or WinRM (for Windows systems) to establish connections and execute tasks remotely.

Infrastructure as Code (IaC): Ansible uses a declarative, human-readable language called YAML (Yet Another Markup Language) to define the desired state of your infrastructure and automation tasks. This allows you to write infrastructure configurations as code.

Playbooks: In Ansible, you write automation tasks and configurations in Playbooks, which are YAML files. Playbooks define a series of tasks to be executed on remote hosts. You can specify roles, variables, and dependencies within a playbook.

Roles: Roles are reusable sets of tasks, variables, and templates organized into a directory structure. They allow you to modularize your Ansible code for better organization and code reuse.

Inventory: The inventory is a list of managed hosts that Ansible will interact with. It can be defined in a simple text file or generated dynamically from various sources. You can group hosts and assign variables to them in the inventory.

Modules: Ansible provides a wide range of modules that enable you to perform various tasks, from package management to file manipulation, service management, and more. Modules are executed on the managed hosts.

Idempotence: Ansible enforces idempotence, meaning you can run the same playbook multiple times without causing unintended side effects. It checks the current state of the system and only applies changes when necessary.

Ad-Hoc Commands: Ansible allows you to run ad-hoc commands to perform quick, one-off tasks on remote hosts without the need to write a playbook. For example, you can use Ansible to restart a service, check disk space, or update packages.

Automation and Orchestration: Ansible is not limited to server configuration but can automate various aspects of IT operations, including network configuration, cloud provisioning, and application deployment. It can also be used for orchestrating complex multi-step tasks.

Community and Ecosystem: Ansible has a strong and active community, and there are many pre-built roles, playbooks, and collections available in Ansible Galaxy. This ecosystem makes it easy to leverage the work of others and speed up your automation efforts.

Extensibility: You can extend Ansible's functionality by writing custom modules and plugins in various programming languages


What is Configuration Management

Configuration management in Ansible refers to the process of defining, implementing, and maintaining the desired state of your IT infrastructure and systems using Ansible's automation capabilities. It involves ensuring that all the servers and devices in your environment are configured correctly, consistently, and securely.

How Ansible Works?

Components
Control node
The machine from which you run the Ansible CLI tools (ansible-playbook , ansible, ansible-vault and others). You can use any computer that meets the software requirements as a control node - laptops, shared desktops, and servers can all run Ansible. Multiple control nodes are possible, but Ansible itself does not coordinate across them, see AAP for such features.

Managed nodes
Also referred to as ‘hosts’, these are the target devices (servers, network appliances or any computer) you aim to manage with Ansible. Ansible is not normally installed on managed nodes, unless you are using ansible-pull, but this is rare and not the recommended setup.

Inventory
A list of managed nodes provided by one or more ‘inventory sources’. Your inventory can specify information specific to each node, like IP address. It is also used for assigning groups, that both allow for node selection in the Play and bulk variable assignment. To learn more about inventory, see the Working with Inventory section. Sometimes an inventory source file is also referred to as a ‘hostfile’.

Playbooks
They contain Plays (which are the basic unit of Ansible execution). This is both an ‘execution concept’ and how we describe the files on which ansible-playbook operates. Playbooks are written in YAML and are easy to read, write, share and understand. To learn more about playbooks, see Ansible playbooks.

Plays
The main context for Ansible execution, this playbook object maps managed nodes (hosts) to tasks. The Play contains variables, roles and an ordered lists of tasks and can be run repeatedly. It basically consists of an implicit loop over the mapped hosts and tasks and defines how to iterate over them.

Roles
A limited distribution of reusable Ansible content (tasks, handlers, variables, plugins, templates and files) for use inside of a Play. To use any Role resource, the Role itself must be imported into the Play.

Tasks
The definition of an ‘action’ to be applied to the managed host. Tasks must always be contained in a Play, directly or indirectly (Role, or imported/included task list file). You can execute a single task once with an ad hoc command using ansible or ansible-console (both create a virtual Play).

Handlers
A special form of a Task, that only executes when notified by a previous task which resulted in a ‘changed’ status.

Modules
The code or binaries that Ansible copies to and executes on each managed node (when needed) to accomplish the action defined in each Task. Each module has a particular use, from administering users on a specific type of database to managing VLAN interfaces on a specific type of network device. You can invoke a single module with a task, or invoke several different modules in a playbook. Ansible modules are grouped in collections



Ad Hoc commands

Ad hoc commands in Ansible are one-time, on-the-fly commands that you can run directly from the command line, without the need to create a dedicated Ansible Playbook. These commands are useful for quickly performing specific tasks or checks on remote hosts without the overhead of writing and organizing a full Playbook


Ping the Remote Hosts to check if they are reachable and responsive using the ping module:
ansible web_servers -m ping

Check the Disk Space on remote hosts using the df module:
ansible web_servers -m df

Update Package Cache on Debian-based systems using the apt module:
ansible web_servers -m apt -a "update_cache=yes"

Restart a Service, for example, to restart the Apache web server using the service module:
ansible web_servers -m service -a "name=nginx state=restarted"


Create a Directory using the file module:
ansible app_servers -m file -a "path=/var/www/myapp state=directory"

Copy a File from the local machine to remote hosts using the copy module:
ansible web_servers -m copy -a "src=/path/to/local/file dest=/remote/destination/file"


Gather System Facts from remote hosts using the setup module to get detailed information about the hosts:
ansible all -m setup


Execute a Custom Shell Command on remote hosts using the command module. For example, to check the status of a custom service:
ansible web_servers -m command -a "systemctl status nginx.service"



Parallelism and Shell Commands

In Ansible, parallelism refers to the ability to execute tasks on multiple hosts simultaneously, which can significantly improve the efficiency and speed of automation. When it comes to running shell commands in Ansible, parallelism can be especially important in scenarios where you need to perform tasks on a large number of remote hosts
Parallel Execution:

Ansible can execute tasks in parallel by default. It determines the level of parallelism based on the system's resources and configuration. The forks setting in your Ansible configuration file (ansible.cfg) controls the maximum number of parallel processes. By default, Ansible uses 5 forks, but you can adjust this number to match your infrastructure's capacity.

if you want to run a shell command across all your web servers to check the uptime, Ansible will execute the command on each web server simultaneously, taking advantage of parallelism
ansible web_servers -m command -a "uptime"

File Transfer

In Ansible, file transfer is a common operation that allows you to copy files between your control node (the machine where you run Ansible) and remote hosts. Ansible provides modules and methods for transferring files, ensuring that you can easily manage and distribute files to your target systems


ansible-playbook -i inventory.ini install_nginx.yml

Using the copy Module:
- name: Copy a file to remote hosts
  hosts: web_servers
  tasks:
    - name: Transfer a file
      copy:
        src: /path/to/local/file.txt
        dest: /remote/destination/


Using the synchronize Module:
- name: Synchronize files with remote hosts
  hosts: app_servers
  tasks:
    - name: Synchronize a directory
      synchronize:
        src: /home/ubuntu/ansible/
        dest: /home/ubuntu/gowda/


Using SCP and rsync:
- name: Copy a file using SCP
  hosts: web_servers
  tasks:
    - name: Transfer a file using SCP
      command: scp /path/to/local/file.txt user@{{ inventory_hostname }}:/remote/destination/


Managing Packages
In Ansible, managing packages on remote hosts is a common task, and Ansible provides several modules to help you perform package management tasks. These modules enable you to install, update, or remove software packages on various operating systems and distributions. Here are some of the key Ansible modules for managing packages:

apt Module (for Debian/Ubuntu):

The apt module is used for managing packages on Debian and Ubuntu-based systems. You can use it to install, remove, and update packages and repositories.
- name: Install a package on Debian/Ubuntu
  hosts: debian_servers
  tasks:
    - name: Install Apache2
      apt:
        name: apache2
        state: present

yum Module (for CentOS/RHEL):

The yum module is used for managing packages on CentOS and Red Hat-based systems. You can use it to install, remove, and update packages and repositories.
Example to install a package

- name: Install a package on CentOS/RHEL
  hosts: centos_servers
  tasks:
    - name: Install Nginx
      yum:
        name: nginx
        state: present


Gathering Facts:

In Ansible, "Gathering Facts" refers to the process of collecting information about the managed hosts in your infrastructure. These facts include details about the host's operating system, hardware, network configuration, available software packages, and other system attributes. Gathering facts is the initial step performed by Ansible when connecting to remote hosts before executing tasks.

- name: Gather and use facts
  hosts: all
  tasks:
    - name: Display the operating system
      debug:
        msg: "The OS on {{ inventory_hostname }} is {{ ansible_facts['ansible_distribution'] }}"

Using Ad hoc command 
ansible all -m setup


Playbooks

Playbooks are a fundamental concept in Ansible and serve as a way to define and execute automation tasks on remote hosts. They are written in YAML format and allow you to describe the desired state of your infrastructure, define a series of tasks, and specify the order in which those tasks should be executed.

Tasks: A playbook consists of a list of tasks that define what needs to be done on remote hosts. Each task includes a module to execute, module-specific parameters, and an optional name for the task.

Hosts: You specify the target hosts or groups of hosts in the playbook using the hosts directive. These hosts are typically defined in your Ansible inventory.

Roles: Playbooks can use roles, which are reusable sets of tasks and templates organized in a directory structure. Roles allow you to modularize your automation code and make it more maintainable.

Variables: You can define and use variables in playbooks to make your tasks more flexible and dynamic. Variables can be set globally, at the playbook level, or at the task level.
---
- name: Configure web server
  hosts: web_servers
  vars:
    http_port: 80
  tasks:
    - name: Configure Apache
      template:
        src: apache.conf.j2
        dest: /etc/httpd/conf/httpd.conf


Handlers: Handlers are special tasks that are executed only when notified by other tasks. They are typically used to restart services or perform other actions when a configuration change occurs.

Conditionals: You can use conditionals in playbooks to control task execution based on the state of the system or gathered facts. Ansible provides a range of built-in conditional statements.

Includes and Imports: Playbooks can include or import other playbooks, making it easier to organize and reuse code.

Error Handling: Ansible provides error handling capabilities in playbooks, allowing you to define what should happen in the event of task failures.

Encryption: Playbooks can be encrypted using Ansible Vault to protect sensitive information, such as passwords and secret keys.

Playbook Structure:
Play
- name: My First Play
  hosts: web_servers
  tasks:
    # Tasks go here
Tasks
- name: Install Apache Web Server
  apt:
    name: apache2
    state: present
Handlers
handlers:
  - name: Restart Apache
    service:
      name: apache2
      state: restarted
Variables
vars:
  http_port: 80
Conditionals
when: ansible_os_family == 'Debian'


Create a Playbook 

Installing the Apache web server on remote servers. This playbook includes a single play with one task to install Apache.

---
- name: Install Apache Web Server
  hosts: web_servers
  tasks:
    - name: Update APT package cache
      apt:
        update_cache: yes

    - name: Install Apache2
      apt:
        name: apache2
        state: present

    - name: Start Apache service
      service:
        name: apache2
        state: started
Execute the Playbook:

To run the playbook, use the ansible-playbook
ansible-playbook my_playbook.yml


The Different YAML Tags

In Ansible, YAML tags, also known as YAML aliases or YAML anchors, are used to reference the same data multiple times within a YAML file. These tags allow you to define data once and reuse it elsewhere in the YAML document. While YAML tags can be useful for creating more concise and maintainable YAML files, they are not specific to Ansible and are a feature of YAML itself.

YAML Anchor (Tag):

To create a YAML anchor, you precede a data structure (e.g., a dictionary or list) with an ampersand (&) followed by the anchor name.
To reference the anchor later in the document, you use an asterisk (*) followed by the anchor name.
The anchor and reference must have the same name.

example 1:

web_server_defaults: &web_defaults
  port: 80
  document_root: /var/www/html

web_servers:
  - name: server1
    {{ *web_defaults }}
  - name: server2
    {{ *web_defaults }}

example 2:
---
- name: Configure web server
  hosts: web_servers
  vars:
    &http_port 80
    &doc_root /var/www/html
  tasks:
    - name: Create web server config
      template:
        src: my_web_server_template.j2
        dest: /etc/httpd/conf/httpd.conf
      vars:
        http_port: *http_port
        document_root: *doc_root



Roles
Creating a New Role



ansible-galaxy init <role_name>

tasks: Contains the main tasks for your role.
handlers: Contains handler tasks, which are notified by other tasks.
defaults: Contains default variable values.
vars: Contains other variable files.
files: Contains static files that need to be transferred to the hosts.
templates: Contains Jinja2 templates.
meta: Contains metadata about the role.








github Secret Token -password

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



Install Ansible by pip
python3 -m pip install --user ansible

In Ubuntu 
sudo systemctl start ansible

Create an inventory by adding the IP address
[myvirtualmachines]
192.0.2.50
192.0.2.51
192.0.2.52

Verify the hosts in your inventory.

ansible all --list-hosts
hosts (1):
  192.0.2.50
  192.0.2.51
  192.0.2.52


Set up SSH connections so Ansible can connect to the managed nodes.

Add your public SSH key to the authorized_keys file on each remote system.

Test the SSH connections, for example:

ssh username@192.0.2.50

Ping the managed nodes.

ansible all -m ping


Building an inventory

Create a new inventory file named inventory.yaml or inventory.ini

Add a new group for your hosts then specify the IP address or fully qualified domain name (FQDN) of each managed node with the ansible_host field. The following example adds the IP addresses of three virtual machines in KVM:

virtualmachines:
  hosts:
    vm01:
      ansible_host: 192.0.2.50
    vm02:
      ansible_host: 192.0.2.51
    vm03:
      ansible_host: 192.0.2.52


Verify your inventory. If you created your inventory in a directory other than your home directory, specify the full path with the -i option.

ansible-inventory -i inventory.yaml --list

Ping the managed nodes in your inventory. In this example, the group name is virtualmachines which you can specify with the ansible command instead of all.

ansible virtualmachines -m ping -i inventory.yaml

Create variables
Variables set values for managed nodes, such as the IP address, FQDN, operating system, and SSH user, so you do not need to pass them when running Ansible commands.

Variables can apply to specific hosts.

webservers:
  hosts:
    webserver01:
      ansible_host: 192.0.2.140
      http_port: 80
    webserver02:
      ansible_host: 192.0.2.150
      http_port: 443


Creating a playbook
Playbooks are automation blueprints, in YAML format, that Ansible uses to deploy and configure managed nodes.

Playbook
A list of plays that define the order in which Ansible performs operations, from top to bottom, to achieve an overall goal.In Ansible, you write automation tasks and configurations in Playbooks, which are YAML files. Playbooks define a series of tasks to be executed on remote hosts. You can specify roles, variables, and dependencies within a playbook.

Play
An ordered list of tasks that maps to managed nodes in an inventory.

Task
A list of one or more modules that defines the operations that Ansible performs.

Module
A unit of code or binary that Ansible runs on managed nodes.
