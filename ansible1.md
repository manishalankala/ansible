


To install Ansible on ubuntu:

$ sudo apt update
$ sudo apt install software-properties-common
$ sudo add-apt-repository --yes --update ppa:ansible/ansible
$ sudo apt install ansible


https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html


Controller Machine: Machine where Ansible is installed

Inventory: Information regarding servers to be managed

Playbook: Automation is defined using tasks defined in YAML format

Task: Procedure to be executed

Module: Predefined commands executed directly on remote hosts

Play: Execution of a playbook

Role: a Pre-defined way for organizing playbooks

Handlers: Tasks with unique names that will only be executed if notified by another task
