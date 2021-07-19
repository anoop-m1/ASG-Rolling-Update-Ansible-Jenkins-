# ASG-Rolling-Update-Ansible-Jenkins-
ASG Rolling Update (Ansible + Jenkins)

# ASG Rolling Update (Ansible + Jenkins)
[![Builds](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)


---
## Features
- ASG Rolling updates through ansible-playbook (_Primary_)
- Includs ELB + ASG + Security group infrastructure on this ansible-playbook
- No need for hosts (Inventory file) for ASG under client servers. Because its work with Dynamic Inventory
- Furthermore, I have included a test website with user-data for more clarification
- Easy to handle and everyone can change the ASG (Count, Project_Name.. etc values)
- No need to install any dependencies like boto and boto3 (Please note that if you have using Ansible 2.2+ and python 2.7)
---
## Pre-Requests
- Install Ansible on your Master Machine
- Create an IAM user role under your AWS account and please enter the values once the playbook running time
##### Installation
[Ansible2](https://docs.ansible.com/ansible/2.3/index.html) (For your reference visit [How to install Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html))
##### IAM Role Creation
[IAM Role Creation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create.html)
##### Ansible Modules used
- [yum](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/yum_module.html) 
- [pip](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/pip_module.html)
- [ec2-key](https://docs.ansible.com/ansible/latest/collections/amazon/aws/ec2_key_module.html)
- [copy](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/copy_module.html)
- [ec2-group](https://docs.ansible.com/ansible/latest/collections/amazon/aws/ec2_group_module.html)
- [debug](https://www.google.com/search?q=debug+%2B+ansible&rlz=1C1ONGR_enIN928IN928&oq=debug+%2B+ansible&aqs=chrome..69i57.5092j0j4&sourceid=chrome&ie=UTF-8)
- [ec2_lc](https://docs.ansible.com/ansible/latest/collections/community/aws/ec2_lc_module.html)
- [ec2_elb_lb](https://docs.ansible.com/ansible/latest/collections/amazon/aws/ec2_elb_lb_module.html)
- [ec2_asg](https://docs.ansible.com/ansible/latest/collections/community/aws/ec2_asg_module.html)
- [ec2_instance_info](https://docs.ansible.com/ansible/latest/collections/community/aws/ec2_instance_info_module.html)
- [add_host](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/add_host_module.html)
- [git](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/git_module.html)
- [file](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/file_module.html)
- [pause](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/pause_module.html)
---
---
## Architecture with Jenkins Automated

- Architecture

<a href="https://ibb.co/yY99LWk"><img src="https://i.ibb.co/HxSSmnP/Untitled.jpg" alt="Untitled" border="0"></a>
---
## Behind the playbook
_I just explained the primary thing ASG Rolling update and Which variables I used so if you have any further doubts please look at the YAML file complete._
### ASG Rolling update (_Primary Code_)
```sh
---

- name: "Bulding Dynamic Inventory Of The AutoScaling Group"
  hosts: localhost
  vars_files:
    - new.vars
  tasks:

    - name: "Fetching Ec2 Instance Details"
      ec2_instance_info:
        aws_access_key: "{{ access_key }}"
        aws_secret_key: "{{ secret_key }}"
        region: "{{ region }}"
        filters:
          "tag:aws:autoscaling:groupName": "devops_asg"
          instance-state-name: [ "running"]
      register: ec2

    - name: "Creating Dymaic Inventory"
      add_host:
        name: "{{ item.public_ip_address }}"
        groups: "instances"
        ansible_host: "{{ item.public_ip_address }}"
        ansible_port: 22
        ansible_user: "ec2-user"
        ansible_ssh_private_key_file: "{{ key_name }}"
        ansible_ssh_common_args: "-o StrictHostKeyChecking=no"
      with_items:
        - "{{ ec2.instances }}"

- name: "Website From GitHub"
  hosts: instances
  become: true
  serial: 1
  vars_files:
    - new.vars
  tasks:

    - name: "Installing Packages"
      yum:
        name:
          - httpd
          - php
          - git
        state: present


    - name: "Cloning Repository from Git"
      git:
        repo: "{{ git_url }}"
        dest: "{{ clone_dir }}"
      register: repo_status

    - name: "Disbaling ELB Health Check"
      when: repo_status.changed == true
      file:
        path: /var/www/html/health.html
        state: touch
        mode: 0000

    - name: "Off loading Ec2-Instance From ELB"
      when: repo_status.changed == true
      pause:
        seconds: "{{ health_time }}"


    - name: "Copying New Content To documentRoot"
      when: repo_status.changed == true
      copy:
        src: "{{ clone_dir }}"
        dest: /var/www/html/
        remote_src: true


    - name: "Restarting/enabling Application"
      when: repo_status.changed == true
      service:
        name: httpd
        state: restarted
        enabled: true

    - name: "Enabling ELB Health Check"
      when: repo_status.changed == true
      file:
        path: "/var/www/html/{{ health_page }}"
        state: touch
        mode: 0644

    - name: "Loading Ec2-Instance to ELB"
      when: repo_status.changed == true
      pause:
        seconds: "{{ health_time }}"
```
