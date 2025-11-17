## What is Ansible?
Ansible is basically a tool that helps DevOps teams automate boring and repetitive tasks — like installing software or configuring servers — across hundreds of machines at once. Everything is written in simple YAML files called playbooks, which makes it easy to read and maintain. It’s mainly used to save time, avoid human errors, and keep all environments consistent.

## How Ansible works ?
Ansible works in a very simple but powerful way. You install it on one main machine called the control node, and from there it connects to all your servers using SSH — so you don’t have to install any software on them. You keep a list of your servers in an inventory file, and write your automation steps in easy-to-read YAML playbooks. When you run the playbook, Ansible connects to each server, executes the tasks one by one using its built-in modules, and brings them to the state you want — like installing a package or restarting a service. The best part is that it’s idempotent, meaning it won’t repeat tasks that are already done, so everything stays consistent and error-free.

## What is difference between is shell scripting and ansible?
Shell scripting is like writing a list of Linux commands in a text file and telling the computer to run them one by one. It works fine if you’re automating something small on a single server. But when you have to manage dozens or hundreds of servers, it becomes messy — you’d have to log into each one and run the script manually. That’s where Ansible comes in. Ansible automates tasks across multiple machines from one central control node using simple YAML playbooks. It’s smarter because it checks the state of the system first — so if something is already done, it won’t repeat it. It’s also agentless, so you don’t have to install anything on your target servers. In short, shell scripting is manual and procedural, while Ansible is automated, declarative, and scalable.

## Ansible Passwordless Authentication
Ansible uses passwordless authentication so it can connect to servers automatically without stopping to ask for a password. It does this through SSH key-based login. First, we generate an SSH key on the control node and copy the public key to all managed nodes. Once that’s done, Ansible can connect using the private key, which is secure and doesn’t require any manual input. This is important because automation should run smoothly across hundreds of servers without human interaction. It also increases security since passwords don’t need to be shared or stored anywhere.

## Ansible Inventory
Ansible inventory is basically the address book of all the servers you want to manage. It’s a file where you list your servers and organize them into groups like web servers, database servers, dev, or prod. Ansible reads this file to know where to execute your tasks. You can also store connection details such as the username, SSH key, or port directly inside the inventory. When you run a playbook, Ansible checks the inventory and connects to the servers accordingly. In simple words, without the inventory, Ansible wouldn’t know which machines to manage or how to reach them, so it’s the foundation of every automation workflow.

## Ansible Ad-hoc Commands
Ansible ad-hoc commands are simple one-line commands we use to perform quick tasks on remote servers without writing a playbook. They’re great for things like checking server connectivity, installing a package, restarting a service, or gathering system info. Instead of creating a full YAML file, we just run a command using an Ansible module, and Ansible executes it on all or selected servers from the inventory. They are mainly used for quick fixes, troubleshooting, and testing because they’re fast, simple, and require no setup.

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
A Play is a single, complete execution unit within a playbook. It specifies which hosts to target and what tasks to execute on those hosts. Plays are used to group related tasks and execute them in a specific order.

### 3. Modules
Modules are the building blocks of Ansible tasks. They are small programs that perform a specific action on a managed node, such as installing a package, copying a file, or managing services.

### 4. Tasks
Tasks are individual actions within a play that use modules to perform operations on managed nodes. Each task is executed in order and can include conditionals, loops, and handlers.

### 5. Collections
Collections are a distribution format for Ansible content. They bundle together multiple roles, modules, plugins, and other Ansible artifacts. Collections make it easier to share and reuse Ansible content.

## What is Ansible Role?
An Ansible Role is simply a structured folder that organizes everything needed for one specific configuration, like installing Apache or setting up a user. We use roles because playbooks become messy when they grow, and roles help us keep things clean, reusable, and easy to maintain. Roles work by following a fixed folder structure that contains tasks, handlers, variables, templates, and files. When we call a role inside a playbook, Ansible automatically runs all parts of that folder in the correct order. It makes automation scalable and professional.
### Example
**Role Name: apache_role**
``
apache_role/
 ├── tasks/
 │    └── main.yml
 ├── handlers/
 │    └── main.yml
 ├── templates/
 │    └── index.html.j2
 └── vars/
      └── main.yml
```