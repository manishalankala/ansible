# ansible



##########ansible ###########

https://galaxy.ansible.com for ready made playbooks


Configuration management tool - to maintain medium size infra with automation

to install applications,configuration,updates and maintain so on

reduce management complexity

ssh public key & private key they communicate

pip install ansible(depends on python version)
yum -y epel-release( some packages of ansible )
yum -y install ansible

ansible --version


then generate public/private key in master 

ssh-keygen

cd .ssh

then id_rsa & id_rsa.pub

place public key to the node by creating  vi authorized_keys and put the public key

then copy to the node

ssh-copy-id alma252@nodeip


in master node where ansible is installed
 go to /etc/ansible/hosts file
 add webserver ip in hosts file
 
 [webserver]
 192.......
 
 try checking first if its working on not
 
 ansible -m ping webserver
 if success then working

 we can work with ansible in 2 ways
 1. playbooks
 2. ad-hoc (on the fly we are installing using adhoc commands)
 
 
 lets do 
 
 ansible -m shell -a "yum -y install httpd" webserver

## we can write a yaml file install multiple applications on the nodes and run the ansible playbook

go to node and give httpd --version to see if its installed 

systemctl status httpd


we can also do the same from master (ansible -m shell -a "systemctl status httpd" webserver or  ansible -m service -a "name=httpd state=started" webserver


what is playbook

set of instructions file to do a paticular tasks on target(host) or group of target(host)

How playbook works?

it works with modules (yum,service,file,etc...) and inventory file (/etc/ansible/hosts)

how playbook starts?

--------

- name: sample playbook
  hosts: target hosts or hosts name specifed in (/etc/ansible/hosts)


let's do 

cd ansible_playbooks

vi sample_playbook.yml
 
---
- name: sample playbook
  hosts: webserver
  tasks:
  - name: sample file       -<------ this step specifies action
    file:
	  path: /tmp/sample.txt
	  owner: root
	  mode: 0644
	  state: touch
  - name: install apache
   yum:                      <----- using yum module
     name: httpd
	 state: latest
  - name: start apache service
    service:
	   name: httpd
	   state: started
  - name: copy sample template file
    template:
		 src: index.html
		 dest: /var/www/html/
		 
		 
		 
save and run the playbook in host machine

 ansible-playbook sample-playbook.yml
 
 check on node if its installed
 



next part if we want to group of tasks to be worked on node we come up with ansible roles

ansible role:
group of playbooks in one file,will help reduce code complexity,increase the code reusability
 
 
 
 
go to master 

ansible-galaxy init lamp     (lamp  linux apache mysql php)
ls 
tree


defaults - main.yml
files - handlers - main.yml (service stop/start)
meta - main.yml - 
README.md
tasks -- main.yml( install apache)
templates
test -- inventory --- test.yml
vars -- main.yml


so we are placing 
 
cd/ansible/tasks/

vi httpd.yml

---
- name: install apache(httpd)
   yum:
two spaces name: httpd
    state: latest
- name: start apache
  service:
     name: httpd
	 state: start
- name: copy index.html file
  copy:
    src: index.html
	dest: /var/www/html/
	



vi php.yml

---
- name: install php
  yum:
    name: php
	state: latest
- name: copy phpinfo file
  copy:
   src: phpinfo.php
   dest: /var/www/html
 notify: restart apache server
 
 
 vi mysql.yml
 
 - name: install mariadb
    yum: 
   name:  mariadb
   state: latest
- name: install mariadb server
   yum:
     name: mariadb-server
	 state: latest
- name: install php_mysql
  yum: 
    name: phpmysql
	state: latest
- name: start mariadb
  service:
    name: mariadb
	state: started
  
 
 
 One thing to note till centos6 ----mysql after that name changed to mariadb centos7
 
 
(tasks will define in main.yml) 
 
 vi main.yml
 
---
# tasks file for lamp
- include_tasks: httpd.yml
- include_tasks: php.yml
- include_tasks: mysql.yml

:wq!


go to files  in ansible lamp directory

cd files
vi index.html

hello welcome

vi phpinfo.php
<?phpinfo():
?>

go to handlers (so here notify what we mentioned same we are mentioning here)

there will be main.yml


vi main.yml

---
# handlers file for lamp
- name: restrat apache server
  service:
     name: httpd
	 state: restarted
	 
	 
:wq!





cd ansible lamp


Try to check using ping to from ansible server to node

ansible -m ping lampserver

to confirm if we go to /etc/ansible/hosts  file we can find

[lampserver]
172........




cd 

ls

lamp directory

vi mylamp.yml

---
- hosts: lampserver
  become: true
  roles:
  - lamp
  
  :wq!
  
check the syntax 

ansible-playbook mylamp.yml --syntax-check

next we can execute the playbook

ansible-playbook mylamp.yml

we go error in httpd.yml file

we need to mentioned state as started state: start

go to child node and verify

