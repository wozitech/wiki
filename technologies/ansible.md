![Ansible Logo](/uploads/logos/ansible-logo.png "Ansible Logo"){.pagelogo}
<!-- TITLE: Ansible -->
<!-- SUBTITLE: A quick summary of Ansible -->

# Quick Reference
Tips and Tricks:
* https://blog.serverdensity.com/what-ive-learnt-from-using-ansible-exclusively-for-2-years/

WOZiTech public github repo: https://github.com/wozitech/ansible.
# Overview
Ansible is a server configuration management tool. It is agentless, requiring no software to be deployed; with the subtle exception of SSH and for some activities, such as, docker containers, python 2/3.

Fedora 29 is supports ansible for python V3; but much of ansible is mainly python V2, with some V3 (depending on modules consumed).

# Core Ansible
Firstly, ansible uses YAML (as taken from the Ruby language) for its definition.

Ansible starts with a playbook, essentially:
* variables
* hosts
* tasks

The start of the playbook must look like (the triple dash is significant):
```
---
- hosts: all
```

## Variables
Variables are as with any programming language; they hold a value that can be used elsewhere; namely, used within other variables (composites) or within task definitions. Variables in ansible are evaluated using the double brace syntax: `{{ variable_name }}`. Depending on the context, you are most like required to enclose the double brace within quotes, viz:
```
vars:
		lvm_mount: /var/data/myMount
		lvm_sub_mount: "{{ lvm_mount }}/subdir"
```

Notice above how the quotes are used around the whole of the variable assignment not just in expanding the variable `lvm_mount`.

In addition to listing variables individually, a playbook can import variables, using the `var_files` declarative:
```
  vars_files:
    - ../../../../common/ansible/vars/centosVars.yml
    - ../../vars.yml
```

Unlike a playbook, the variables file has no format required other than is must be in YAML format. E.g. `../../var.yml`:
```
network_gw: "10.0.0.2"      # gateway firewall
netmask: "255.255.255.0"
webserver_ip: "10.0.0.21"
```

## Core Modules
Ansible has an extensive set of core modules for crafting Linux servers, including `users`, `groups`, `yum/apt`, `systemd` and `firewalld` and easily extended with other modules via `pip` (itself a core module).

One interesting aspect of ansible is the ability to copy/template target files and then run the resutling file through validation; an example is that of [sudoers configuration](https://github.com/wozitech/vagrant/blob/master/common/ansible/tasks/centosTasks.yml).

In most cases, these modules run on the ansible host (not the target). But some modules do have target dependencies as I found out with docker [`docker-py`](https://github.com/wozitech/vagrant/blob/master/common/ansible/tasks/docker_prep.yml).

You can of course create your own 'local modules' through a simple directory structure and explicitly include such 'modules':
```
.../
  my.yml
  files/
	tasks/
	templates/
	vars/
	modules/
		moduleA/
			files/
			tasks/
			templates/
			vars/
```

Inside `.../my.yaml`:
```
---
- hosts: all
  vars_files:
    - ../../../../common/ansible/vars/centosVars.yml
    - ../../vars.yml

  vars:
    NON_SSL_PORT: 80
    SSL_PORT: 443
		
  pre_tasks:
    - import_tasks: tasks/network.yml

  roles:
    - { role: bertvv.dnsmasq, become: yes }
    
  tasks:
    - import_tasks: tasks/nginx.yml
    - import_tasks: tasks/letsencrypt.yml

	post-tasks: []
```

## Templates
Ansible templates use [jinja2](https://docs.ansible.com/ansible/2.6/user_guide/playbooks_templating.html); an expressive templating language, great at easily creating customised configuration files, such as, [nginx conf](https://github.com/wozitech/vagrant/blob/master/enterprise/ansible/hosts/proxy/templates/wiki-reverse.conf.j2) and [networking conf](https://github.com/wozitech/vagrant/blob/master/common/ansible/templates/ifcfg.j2).

## Roles

*mention galaxy*

## 

# TODO
WOZiTech journey into Ansible is only just starting. On our journey ahead is:
* Transform the [custom tasks](https://github.com/wozitech/vagrant/tree/master/common/ansible/tasks) to Ansible roles
* Hashicorp's Vault is great for storing keys/passwords; fetch provisioning secrets:
	* User public/private keys of passwordless SSH authentication
	* wiki.js users including default admin) from Vault

