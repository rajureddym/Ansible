**Ansible:**

- Ansible is an open-source platform that facilitates 
    - Provisioning/Automation
    - Config management
    - Application deployment

**Features:**
- Declarative
    - define what you want then ansible take care of remaining
- Agent-less
    - push based configuration management tool that doesn't require you to install anything on host machines to run these configurations or installs
    - takes advantage of SSH mechanism to run playbook against specified hosts
- Idempotent
    - changes are applied if the config is drifted from initial setup or declared config
- Community driven
    - already developed playbooks, tasks
    - published as collections in ansible galaxy
- It requires minimal resources, so there’s low overhead
    - Python is the only required dependency

**Ansible concepts:**

- `Control Node`: A machine with installed ansible to run ansible commands/playbooks 
- `Managed Nodes`: Servers that are managed with Ansible, also called as hosts. Ansible is not installed on it
- `Inventory`: List of managed nodes
- `Collections`: distribution format for ansible content includes playbooks, roles, modules and plugins. We can install collections through Ansible Galaxy
- `Modules`: The units of code Ansible executes
- `Tasks`: The units of action in Ansible
- `Playbooks`: Ordered lists of tasks, saved so you can run those tasks in that order repeatedly 

**`Modules`:**
- also referred to as “task plugins” or “library plugins”  are discrete units of code that can be used from the command line or in a playbook task
- Ansible executes each module, usually on the remote managed node, and collects return values
- You can invoke a single module with a task, or invoke several different modules in a playbook
- Each module supports taking arguments. Nearly all modules take `key=value` arguments, space delimited
- some example modules
    - apt/yum, copy, file, command, shell, script, raw 

*module development*
- All modules return JSON format data. This means modules can be written in any programming language
- Modules should be idempotent, and should avoid making any changes if they detect that the current state matches the desired final state 
- When used in an Ansible playbook, modules can trigger ‘change events’ in the form of notifying handlers to run additional tasks.


**`Inventory:`**
- A list of managed nodes. An inventory file is also sometimes called a “hostfile”. 
- Your inventory can specify information like IP address or hostname for each managed node
- consists of hosts & groups

*Patterns*: Targeting hosts and groups
- Patterns let you run commands and playbooks against specific hosts and/or groups in your inventory
- An Ansible pattern can refer to a single host, an IP address, an inventory group, a set of groups, or all hosts in your inventory
- Patterns are highly flexible - you can exclude or require subsets of hosts, use wildcards or regular expressions, and more

*common patterns*
- All hosts: `all or *`
- One host:  `host1`
- Multiple hosts: `host1:host2` (or host1,host2)
- One group: `webservers`
- Multiple groups: `webservers:dbservers`
- Excluding groups: `webservers:!atlanta`       //all hosts in webservers except those in atlanta
- Intersection of groups: `webservers:&staging` //any hosts in webservers that are also in staging

**`Playbooks:`**
- written in yaml and are easy to read, write, share and understand
- invokes ansible modules and things called tasks, executable sequentially from top to bottom
- playbook is a file containing one or more plays/set of plays
- Playbooks can include variables along with tasks

*`Plays`*
- ordered sets of tasks that executes against host selection from inventory
- syntax:
    - name of play 
    - set of hosts that it run against
    - variables/vars
    - conducting user i.e. remote_user
    - set of tasks

*`Tasks`*
- run sequentially
- uses modules to define tasks such as installing apache on remote machine using yum module 
    - can pass argument as key value pairs to module
- Handler Tasks 
    - special tasks that run at end of play if notified by another task
    - triggered only one time
    - example, if a configuration file gets changed notify a service restart it needs to run

**Roles:**
- Roles provide a framework for fully independent, or interdependent collections of variables, tasks, files, templates, and modules
- the role is the primary mechanism for breaking a playbook into multiple files. This simplifies writing complex playbooks, and it makes them easier to reuse
- enable you to group together action that we run all the time
- Any logical entity which can be reused as a reusable function, that entity can be moved to role. for example,
>
    - hosts: apache-node 
    roles: 
    - {role: install-apache} 
    - {role: start-apache} 

- A role directory structure contains directories: defaults, vars, tasks, files, templates, meta, handlers. Each directory must contain a `main.yml` file which contains relevant content

- By default, Ansible looks for roles in two locations:
    - in a directory called roles/, relative to the playbook file
    - in /etc/ansible/roles
    - Alternatively, you can call a role with a fully qualified path inside playbook 
    >
        roles:
        - role: '/path/to/my/roles/common'


**Ansible ad-hoc commands**
- uses the /usr/bin/ansible command-line tool to automate a single task on one or more managed nodes
- are quick and easy, but they are not reusable and are great for tasks you repeat rarely
- Ad-hoc tasks can be used to reboot servers, copy files, manage packages and users, and much more. You can use any Ansible module in an ad-hoc task
- Ad-hoc tasks, like playbooks, use a declarative model, calculating and executing the actions required to reach a specified final state. They achieve a form of idempotence by checking the current state before they begin and doing nothing unless the current state is different from the specified final state
- examples
    - reboot: 
        > `ansible atlanta -a "/sbin/reboot"`
    - copy files: 
        > `ansible atlanta -m ansible.builtin.copy -a "src=/etc/hosts dest=/tmp/hosts"`
    - service: 
        > `ansible webservers -m ansible.builtin.service -a "name=httpd state=started"`


**Ansible Galaxy:**
- is a repository for Ansible Roles that are available to drop directly into your Playbooks to streamline your automation projects
- Ansible Galaxy is essentially a large public repository of Ansible roles. Roles ship with READMEs detailing the role’s use and available variables. Galaxy contains a large number of roles that are constantly evolving and increasing

**Ansible Tower:**
- Ansible Tower (formerly ‘AWX’) is a web-based solution that makes Ansible even more easy to use for IT teams of all kinds. It’s designed to be the hub for all of your automation tasks
- you can centralize and control your IT infrastructure with a visual dashboard, role-based access control, job scheduling, integrated notifications and graphical inventory management



*Some ansible commands*

> ansible all -m ping -vvv

> ansible-playbook -i hosts apache-playbook.yml --private-key <key_pair_file_path>