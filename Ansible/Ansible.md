## What is Ansible?
Ansible is basically a tool that helps DevOps teams automate boring and repetitive tasks — like installing software or configuring servers — across hundreds of machines at once. Everything is written in simple YAML files called playbooks, which makes it easy to read and maintain. It’s mainly used to save time, avoid human errors, and keep all environments consistent.

## How Ansible works ?
Ansible works in a very simple but powerful way. You install it on one main machine called the control node, and from there it connects to all your servers using SSH — so you don’t have to install any software on them. You keep a list of your servers in an inventory file, and write your automation steps in easy-to-read YAML playbooks. When you run the playbook, Ansible connects to each server, executes the tasks one by one using its built-in modules, and brings them to the state you want — like installing a package or restarting a service. The best part is that it’s idempotent, meaning it won’t repeat tasks that are already done, so everything stays consistent and error-free.

## 