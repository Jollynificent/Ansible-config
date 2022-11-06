Documentation for ansible

#install jenkins 
`sudo apt install default-jdk-headless`
`wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -`
`sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'`
`sudo apt update`
`sudo apt-get install jenkins`
`sudo systemctl status jenkins`
`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`

#install and check ansible version
`sudo apt update`
`sudo apt install ansible`
`ansible --version`

#test if files are saved in folder
`sudo ls /var/lib/jenkins/jobs/ansible/builds/`
`ls /var/lib/jenkins/jobs/ansible/builds/2/archive/`

prepare dev environ with vscode

On vscode
`git branch`
`git status`
`git checkout -b prj-11`

#create directory and folders
`mkdir playbooks`
`cd playbooks`
`touch common.yml`

#create directory and folders
`mkdir inventory`
`cd inventory`
`touch dev.yml staging.yml uat.yml prod.yml`

#update dev.yml with the commands
[nfs]
172.31.37.84 ansible_ssh_user='ec2-user'

[webservers]
172.31.44.171 ansible_ssh_user='ec2-user'
172.31.47.124 ansible_ssh_user='ec2-user'

[db]
172.31.38.84 ansible_ssh_user='ec2-user' 

[lb]
172.31.34.216 ansible_ssh_user='ubuntu'

#update common.yml with the commands
---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
    - name: ensure wireshark is at the latest version
      yum:
        name: wireshark
        state: latest

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
    - name: Update apt repo
      apt: 
        update_cache: yes

    - name: ensure wireshark is at the latest version
      apt:
        name: wireshark
        state: latest
        
#commit changes to github
`git status`
`git add playbooks`
`git add inventory`
`git commit -m "made some changes"`

#setup ansible inventory and test
`eval `ssh-agent -s``
`ssh-add jenkins.pem`
`ssh-add -l`
`ssh -A ubuntu@3.8.207.49`
`ansible-playbook -i /var/lib/jenkins/jobs/ansible/builds/4/archive/inventory/dev.yml /var/lib/jenkins/jobs/ansible/builds/4/archive/playbooks/common.yml`

#check wireshark installation on instances(web2, db and lb)
`ssh ec2-user@13.40.209.3`
`wireshark --version`
`exit`

db
`ssh ec2-user@13.42.50.183`
`wireshark --version`
`exit`

lb
`ssh ubuntu@3.10.55.163`
`wireshark --version`
`exit`
