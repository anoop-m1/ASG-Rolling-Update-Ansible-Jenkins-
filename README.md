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
### How To Use
Ansible Installation article is in pre-request section so please check out the pre-request section.
```sh
amzon-linux-extras install -y ansible2
yum install git -y
git clone https://github.com/yousafkhamza/ASG-rolling-update.git
cd ASG-rolling-update

---Please-Change Your-Credentials---

ansible-playbook main.yml
```
---
## Architecture with Jenkins Automated

- Architecture

<a href="https://ibb.co/yY99LWk"><img src="https://i.ibb.co/HxSSmnP/Untitled.jpg" alt="Untitled" border="0"></a>
---
## Behind the playbook
_I just explained the primary thing ASG Rolling update and Which variables I used so if you have any further doubts please look at the YAML file complete._
### ASG Rolling update (_Primary Code_)

