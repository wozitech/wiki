![Ansible Logo](/uploads/logos/ansible-logo.png "Ansible Logo"){.pagelogo}
<!-- TITLE: Ansible -->
<!-- SUBTITLE: A quick summary of Ansible -->

# Quick Reference
Tips and Tricks:
* https://blog.serverdensity.com/what-ive-learnt-from-using-ansible-exclusively-for-2-years/

WOZ*iTech* public github repo: https://github.com/wozitech/ansible.
# Overview
Ansible is a server configuration management tool. It is agentless, requiring no software to be deployed; with the subtle exception of SSH and for some activities, such as, docker containers, python 2/3.

Fedora 29 is supports ansible for python V3; but much of ansible is mainly python V2, with some V3 (depending on modules consumed).

Why does WOZ*iTech* use ansible? First and foremost; it is simple and easy to learn for any sysadmin. True, YAML is not my preferred format; which a node.js background I prefer JSON. But you soon get used to the identation of YAML and whether to dash prefix your list or not.

Second, it's agentless. All you need is an SSH connection to your target.

WOZ*iTech* using ansible to create [idempotent ](https://www.google.com/search?q=idempotent&ie=utf-8&oe=utf-8&client=firefox-b-ab) and [immutable](https://www.google.com/search?q=immutable&ie=utf-8&oe=utf-8&client=firefox-b-ab). When building an infrastructure there are many good and best practices; remembering to do all them is not possible. But having them all scripted and building your own portfolio of activities means you can start quicker and safer next time. This is especially true in todays DevSecOps world; knowing and demonstrating good build acumen.

We have crafted our own CentOS minimal build Ansible script; there are plenty out there, but this one does what we want it to do and will continue to invest time and knowledge on it over time. It means we can ensure all our CentOS servers are built consistently, but moreso, that they can be built faster. Idempotence means we can rerun the CentOS Base OS script on any server without error or data loss and applying the latest knowledge.

When it comes to patching, there is always that risk that patching a server will fail. Well why not simply build a new instance of the server (virtual of course) with the latest patches; apply necessary tests and then swap out the current instance? Immutable means we can build as many instances as we need knowing they are all built the same and service easily transferred between them.

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

## Core Tasks
Tasks are the mainstay of ansible; they are actionable work, e.g.:
```
- name: Enable DNS inbound on public zone
  become: true
  firewalld:
    state: enabled
    zone: public
    service: "{{ item }}"
  with_items:
    - dns
  when:
		- DISABLE_HOST_FIREWALL is undefined or not DISABLE_HOST_FIREWALL
		- dnsmap.changed
	register: dns_enabled
	```

Ansible has an extensive set of core tasks for crafting Linux servers, including `users`, `groups`, `yum/apt`, `systemd` and `firewalld` and easily extended with other tasks via `pip` (itself a core module).

Any tasks can be repeated using `with_items` which can be a simple list (value) or complex objects. Note, do not use `apt`/`yum` with `with_items`, as it will call apt/yum on each package listed; simply pass as a list all packages to `name` attribute - this is equivalent to writing multiple packages on the command line (`yum install -y packageA packageB packageC`).

There are times when you need to control if a task runs. Any task can capture its output using the `register` attribute; just give it a name and it becomes a variable that can be used in subsequent tasks or for debugging. One of the best uses of the registered variable is to control the running of another tasks, such as a service restart if a given task has changed anything. Ansible has the `when` attribute is the typical set of conditional operators; shown in the above example the Enabling of DNS inbound task will only run is a previously registered variable `dnsmap` has the `changed` result state.

Ansible is not 'write once, run anywhere'; you have to know if your target is ubuntu and therefore need to use `apt` to add/remove packages, or your target is CentOS and therefore need to use `yum`. Ansible when it runs has the (default) option to gather facts about its target, which includes information such as OS type. Using the `when` attribute you can therefore target multiple platforms using different tasks.

One interesting aspect of ansible is the ability to copy/template tasks target files and then run the resulting file through validation; an example is that of [sudoers configuration](https://github.com/wozitech/vagrant/blob/master/common/ansible/tasks/centosTasks.yml).

Ansibles `lineinfile` is of particular to note; a simple method of managing any existing file content ensuring the presence/absence of given text; [used to maintain a set of DNS entries](https://github.com/wozitech/vagrant/blob/master/enterprise/ansible/hosts/proxy/tasks/dns.yml).

In most cases, these tasks execute on the target using information gathered on that target, with the task formulating the necessary commands to run remotely on that target host. However, some tasks rely upon some dependencies on the target as we found out with docker [`docker-py`](https://github.com/wozitech/vagrant/blob/master/common/ansible/tasks/docker_prep.yml).

You can of course compose large provisioning projects into separate sets of tasks (*local modules*) using a simple directory structure and explicitly include such *modules*:
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
TBC - better modules

*mention galaxy*

## Inventory
*TBC - not yet got to work with an inventory; calling ansible provision direct from Vagrant assuming a single host through given the entry playbook.*

# TODO
WOZ*iTech* journey into Ansible is only just starting. On our journey ahead is:
* Transform the [custom tasks](https://github.com/wozitech/vagrant/tree/master/common/ansible/tasks) to Ansible roles
* Hashicorp's Vault is great for storing keys/passwords; fetch provisioning secrets:
	* User public/private keys of passwordless SSH authentication
	* wiki.js users including default admin) from Vault

