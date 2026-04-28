## What is Ansible?
Ansible is an **automation and configuration management tool** used to manage servers and automate IT tasks. It is used because in real environments we may have many servers, and doing tasks like installing software, updating configurations, or deploying applications manually on each server would take a lot of time and could lead to mistakes. Ansible solves this problem by allowing us to automate these tasks from one central machine. It works in a simple way where we write instructions in a YAML file called a **playbook**, which defines what tasks need to be done, and Ansible connects to the target servers through **SSH** without requiring any agent to be installed on those servers. When we run the playbook, Ansible follows the instructions and performs the same tasks on all the specified servers automatically, making system management faster, consistent, and easier.

## How Ansible works ?
Ansible works in a very simple but powerful way. You install it on one main machine called the control node, and from there it connects to all your servers using SSH — so you don’t have to install any software on them. You keep a list of your servers in an inventory file, and write your automation steps in easy-to-read YAML playbooks. When you run the playbook, Ansible connects to each server, executes the tasks one by one using its built-in modules, and brings them to the state you want — like installing a package or restarting a service. The best part is that it’s idempotent, meaning it won’t repeat tasks that are already done, so everything stays consistent and error-free.

## What is difference between is shell scripting and ansible?
Shell scripting is like writing a list of Linux commands in a text file and telling the computer to run them one by one. It works fine if you’re automating something small on a single server. But when you have to manage dozens or hundreds of servers, it becomes messy — you’d have to log into each one and run the script manually. That’s where Ansible comes in. Ansible automates tasks across multiple machines from one central control node using simple YAML playbooks. It’s smarter because it checks the state of the system first — so if something is already done, it won’t repeat it. It’s also agentless, so you don’t have to install anything on your target servers. In short, shell scripting is manual and procedural, while Ansible is automated, declarative, and scalable.

## What is  Passwordless Authentication?
Passwordless authentication is a method that allows one system to log in to another system without typing a password each time. It is used because in automation tools like Ansible, the control machine needs to connect to many remote servers repeatedly, and entering a password every time would make automation impossible and slow. Instead of passwords, it uses **SSH key-based authentication**, which is more secure and convenient. In this process, we generate a pair of keys on the control machine called a **public key** and a **private key**. The public key is copied to the remote server, while the private key stays safely on the control machine. When Ansible tries to connect to the server through SSH, the server checks the public key and verifies it with the private key, and if they match, the login is allowed automatically without asking for a password. This makes automated communication between systems secure and smooth. 

## What is Ansible Inventory?
Ansible Inventory is a file where we define the list of servers that Ansible will manage and automate. It is needed because Ansible must know which machines it should connect to when running a playbook, especially when we are working with multiple servers in real environments. Instead of manually specifying server details every time, we keep them organized in the inventory file, which can include server IP addresses, hostnames, and groups of servers like web servers or database servers. This makes management easier because we can run automation tasks on a single server, a group of servers, or all servers at once. Ansible uses this inventory file to know where to connect through SSH and on which machines it should execute the tasks defined in the playbook, making automation structured and scalable.

## What is Ad-hoc Commands?
Adhoc commands in Ansible are simple one-line commands used to perform quick tasks on remote servers without writing a full playbook. They are useful when we need to do small or immediate actions like checking server connectivity, installing a package, restarting a service, or copying a file. Instead of creating a YAML playbook for such small tasks, Ansible allows us to run a direct command from the terminal, which saves time and makes troubleshooting easier. It works by using the Ansible command along with the inventory to specify which hosts the command should run on and which module should perform the task. When the command is executed, Ansible connects to the target servers through SSH and runs the task immediately, making it a quick and convenient way to manage servers. ⚙️

## What is Ansible Playbook?
Ansible Playbook is a YAML file where we define a set of automation tasks that Ansible should perform on managed servers. It is used because in real environments we often need to perform multiple steps in a specific order, like installing packages, creating users, configuring services, and starting applications, and doing these tasks manually on many servers would be time-consuming and error-prone. A playbook allows us to write these steps as code so the same process can be repeated consistently anytime. It works by defining plays that specify which hosts from the inventory should be targeted and what tasks should be executed using Ansible modules. When we run the playbook using the `ansible-playbook` command, Ansible connects to the specified servers through SSH and executes each task step by step, ensuring the desired configuration is applied automatically and consistently across all servers.

## Why Ansible is called agentless?
Ansible is called agentless because it does not require any special software or agent to be installed on the target servers to manage them. This is useful because installing and maintaining agents on every server can increase complexity, consume resources, and require extra maintenance when there are many machines in an environment. Instead, Ansible works by using existing communication methods like **SSH for Linux servers** (and WinRM for Windows) to connect directly from the control node to the managed nodes. When we run a command or playbook, Ansible temporarily sends the required module to the remote server through SSH, executes the task, returns the result, and then removes the module. Because of this approach, Ansible can automate servers without installing any persistent agent, making the setup simpler, lightweight, and easier to manage.

## How does Ansible works?
Ansible works as an automation tool that allows us to manage and configure multiple servers from a single machine called the **control node**. It is useful because in real environments we often have many servers and performing tasks manually on each one would be slow and error-prone, so Ansible helps automate these tasks and keep configurations consistent. It works by using an **inventory file** where we define the list of target servers, and a **playbook** written in YAML where we describe the tasks we want to perform, such as installing software or starting services. When we run the playbook, Ansible connects to the target machines through **SSH**, temporarily sends small modules to execute the required tasks, collects the results, and then removes those modules after execution. This process allows Ansible to automate server management in a simple, consistent, and agentless way across multiple systems.

## What is YAML?
YAML is a simple and human-readable format used to write configuration files. In tools like Ansible, Kubernetes, and Docker Compose, YAML helps define settings, infrastructure, and automation steps in a clean and structured way. Basically, YAML lets us describe what we want the system to do in a very readable format, which reduces mistakes and makes automation much easier.

## Ansible Concepts
### 1. Playbook
A Playbook is a YAML file that defines a series of actions to be executed on managed nodes. It contains one or more **plays** that map groups of hosts to roles.
```
---
- name: Configure web servers                     # 2. PLAY (execution unit)
  hosts: web                                      #   Specifies which hosts to target
  become: yes
  tasks:                                          # 4. TASKS (individual actions)

    - name: Install nginx package                 # Task uses a MODULE
      apt:                                        # 3. MODULE (performs specific action)
        name: nginx
        state: present

    - name: Copy website file                     # Another task using a module
      copy:
        src: index.html
        dest: /var/www/html/index.html

    - name: Start nginx service                   # Another task using a module
      service:
        name: nginx
        state: started
        enabled: yes

    - name: Create an S3 bucket using AWS collection   # Using a module from a COLLECTION
      amazon.aws.s3_bucket:
        name: ritesh-demo-bucket
        state: present
```

### 2. Play
Ansible Play is a set of tasks in a playbook that are executed on a specific group of servers defined in the inventory. It is useful because in real environments we may have different types of servers like web servers, database servers, or application servers, and each group may need different configurations or tasks. A play allows us to target a particular group of hosts and define what actions should be performed on them. It works inside a playbook where we specify the **hosts** the play should run on and then list the **tasks** that need to be executed using Ansible modules. When the playbook runs, Ansible reads the play, connects to the specified servers through SSH, and executes the tasks step by step on those machines to achieve the desired configuration.

### 3. Modules
Ansible modules are small programs or tools that Ansible uses to perform specific tasks on managed servers. They are needed because when we automate server management, we perform many types of actions such as installing packages, creating users, copying files, or starting services, and instead of writing complex scripts for each task, Ansible provides ready-made modules to handle them. These modules make automation simpler, consistent, and easier to understand. When we write tasks in an Ansible playbook or run an adhoc command, we call these modules to perform the required action. During execution, Ansible connects to the target servers through SSH, temporarily sends the required module to the server, runs it to complete the task, and then removes it after execution, which allows Ansible to automate tasks efficiently without needing any agent installed on the servers.

**What are some common Ansible modules you've used or come across?**
In Ansible, there are many commonly used modules that help automate day-to-day tasks, such as the command and shell modules for running commands on remote machines, the copy and file modules for managing files and permissions, the apt and yum modules for installing or removing packages depending on the operating system, the service module for starting or stopping services, and the user module for managing users; we use these modules because they simplify repetitive system administration work and provide a standard, reliable way to manage infrastructure without writing custom scripts; in practice, we include these modules inside playbooks as tasks, pass required parameters like package name or file path, and Ansible executes them on target nodes while ensuring the system reaches the desired state in an efficient and idempotent way.

### 4. Tasks
Ansible Tasks are the individual actions that Ansible performs on managed servers as part of a playbook. They are used because automation usually involves multiple steps such as installing packages, creating users, copying files, or starting services, and each of these actions needs to be clearly defined so Ansible knows what to do. A task basically tells Ansible which module to use and what operation should be performed. In a playbook, tasks are written under the **tasks section**, and they run one by one in the order they are defined. When the playbook is executed, Ansible connects to the target servers through SSH and runs each task using the specified module, ensuring that the required configuration or action is applied correctly on the servers.

### 5. Collections
Ansible Collections are a packaged set of Ansible content that includes modules, roles, plugins, and other related files organized together for a specific purpose or technology. They are useful because as Ansible grew, many modules and features were created for different platforms like cloud services, networking devices, and operating systems, and managing all of them in one place became difficult. Collections help organize and distribute this content in a structured way so users can easily install and use only the components they need. They work by grouping related automation resources under a namespace and collection name, and we can install them using the `ansible-galaxy collection install` command. Once installed, Ansible can access the modules or roles from that collection when running playbooks, making automation more modular, organized, and easier to maintain.

## What is Ansible Role?
Ansible Roles are a way to organize and structure playbook content by grouping related tasks, variables, files, templates, and handlers into a reusable directory structure. They are used because in real projects playbooks can become very large and complex, and managing everything in a single file becomes difficult. Roles help break automation into smaller, organized components so the same configuration can be reused across different playbooks or environments. They work by following a standard folder structure where tasks, variables, handlers, templates, and files are stored in separate directories, and then the role is simply called inside a playbook. When the playbook runs, Ansible automatically reads the role structure and executes the tasks in the correct order on the target servers, making automation cleaner, reusable, and easier to maintain.

**1. tasks/**  
Contains the main list of actions the role will perform (like install package, start service).

**2. handlers/**  
Contains actions that run only when notified (e.g., restart a service after config change).

**3. files/**  
Static files to be copied to managed hosts.

**4. templates/**  
Files with variables inside, usually using Jinja2 (.j2) templates.

**5. vars/**  
Variables that are used within the role.

**6. defaults/** 
Default variables for the role, which can be overridden.

**7. meta/** 
Metadata about the role — dependencies, author info, supported platforms.

**8. library/** 
Custom modules or plugins used within the role.

**9. Module_defaults/**
Default module parameters for the role.

**10. Lookup_plugins/**
Custom lookup plugins for the role.

**Directory Structure of an Ansible Role**
```
<role_name>/
  ├── defaults/
  │   └── main.yml
  ├── files/
  ├── handlers/
  │   └── main.yml
  ├── meta/
  │   └── main.yml
  ├── tasks/
  │   └── main.yml
  ├── templates/
  ├── vars/
      └── main.yml
```

## What is Ansible Vault?
Ansible Vault is a feature that protects sensitive data by encrypting it inside Ansible files. We use Vault to secure passwords, API keys, and other secrets so they don’t remain in plain text or get exposed in Git. It works by applying AES-256 encryption to files and requires a vault password to view or use that data. With Vault, we can create, edit, encrypt, or decrypt files safely, and Ansible decrypts them only at runtime. It keeps automation secure and production-ready.

## What is Error Handling?
Error handling in Ansible is a way to control what should happen if a task fails during playbook execution. It is important because when automation runs on multiple servers, sometimes a task may fail due to issues like a service not existing, a package installation error, or a configuration problem, and we may not always want the entire playbook to stop because of that failure. Error handling allows us to manage such situations so automation can continue running or take alternative actions. It works by using features like **ignore_errors**, **failed_when**, **block**, **rescue**, and **always**, which allow us to define how Ansible should behave when a task fails. For example, we can tell Ansible to ignore a failure and continue with the next task, or define a rescue task that runs only when an error occurs, ensuring the playbook execution is more controlled, reliable, and fault-tolerant.

## What is Policy as Code?
Policy as Code is the practice of defining and managing rules or policies using code instead of manually enforcing them. It is used because in modern infrastructure and cloud environments there are many security, compliance, and operational rules that must always be followed, and managing them manually can lead to mistakes or inconsistencies. By writing these policies as code, we can automate enforcement and ensure systems always follow the required standards. It works by defining rules in a configuration or policy language and integrating them into automation tools or CI/CD pipelines, so whenever infrastructure is created or modified, the system automatically checks whether it follows the defined policies and blocks or reports anything that violates them. This approach makes governance automated, consistent, and easier to manage at scale. 🔐

### Order of execution of tasks Ansible
Only when task number one is complete and successful, Ansible will move to the task number two and only when task number two is complete and successful, Anible will move to the task number three. 
