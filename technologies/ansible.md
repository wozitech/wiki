![Ansible Logo](/uploads/logos/ansible-logo.png "Ansible Logo"){.pagelogo}
<!-- TITLE: Ansible -->
<!-- SUBTITLE: A quick summary of Ansible -->

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