# **DEPRECATED**

This repository is now deprecated. Please see https://github.com/goetzrieger/ansible-labs for much better demo and training guidelines.

----

# Ansible Demo Notes

## Background

### Aim
To show Ansible's features and possibilities including Tower.

### Target audience
Technical people who want to see Ansible Tower in action with certain examples.

### Legend of emoticons
- :question: Question to ask the customers to enable interaction with customer
- :wrench: Examples of usage for Operations
- :notes: Examples of usage for Developers
- :necktie: Examples of usage for Managers

## concepts
- Ansible is automation - and thus about saving time
- Ansible is simple - integrates seamless into existing infrastructure
- Ansible is powerful - from system management to application development
- Ansible is agentless - no further software needed
- Ansible is engine: triggered via command line or via web interface/API
- Ansible is language of "playbooks": set of "tasks", working steps which need to be run
- Ansible is enterprise framework: Tower can deliver RBAC, single centralized place, user friendly GUI, shows results
- Automation:
  - executing tasks across multiple machines
  - talking in the language of the domain
  - executing multiple tasks in a row
  - separating tasks from rights
  - integration with surrounding infrastructure
- tasks can be either generic cli commands
- or simplified by parametrized modules
- no client needed: uses present operating system remote management technology
- any machine: Ansible itself runs anywhere, like an admin laptop, configuring others
- push!

## Demo

### Questions :question:
- What is your largest pain right now?
- What takes most manual work?
- What services in your business are not integrated with each other right now but seem simple to connect?

### Start: command line [skip if existing knowledge]
- :question: Do you often access multiple machines via command line?
- :question: Do you know dancing/dstributed shell?
- :question: Can you talk in the language of the problem, instead of generic commands?
- show inventory of a simple setup on command line
- explain host entries, groups
- inventory can be static file like example, or executed script; think of existing CMDB or Satellite or anything
- on CLI: ping a machine, shows that is alive - and Ansible works!
- explain that SSH is used: the current user connects with current rights
- show a simple task: `ansible host -m command -a "uname -a"`
- this was direct command - explain difference between simple commands and usage of modules
  - simple commands are just command line action
  - user needs to know the commmand line
  - modules instead use language of the actual problem
  - just fill the parameters, don't think about commands in the background
- ansible with module user: `ansible host -m user -a "name=abc state=present"`
- this fails - explain the need for rights
- shortly explain how Ansible actually connects: via SSH, with the actual SSH user
- repeat task with sudo rights: `ansible host -m user -a "name=abc state=present" -b`
- where to get info about module parameters: `ansible-doc user`, and `ansible-doc -s user`
- windows: has own modules, like win_ping
- :white_check_mark: Ansible can contact multiple nodes at the same time
- :white_check_mark: commands can already be distribution agnostic, even OS agnostic
- :white_check_mark: use case: instant execution of single tasks across server landscapes
- :white_check_mark: example: get state of all running servers right away
- :white_check_mark: example: deploy OpenSSL update on all relevant machines right away
- :white_check_mark: can save time already compared to plain ssh or even dsh
- :white_check_mark: missing yet: stacks of tasks, describing entire setups
- :wrench: Emergency openssh update of all productive systems in one go.
- :wrench: Launch a statistics script on all database servers.
- :notes: Restart failed web daemon on development machines.
- :necktie: Gather state of all available machines, even if they are not centrally managed yet.

### Playbooks
- :question: Do you already write your own automation scripts?
- :question: Take your average shell script - how long is it, how easy to read/understand?
- :question: Can you use variables, from differenct sources?
- open simple playbook, for example https://github.com/liquidat/ansible-demo-apache-simple , `apache-setup.yml`
- highlight become, explain when and that it is necessary (sudo, others are possible)
- show that tasks are stacked, are executed in order
- go through tasks, highlight different modules
- mention: tasks can be serialized to be only applied to sets of a specific group (load balancers, etc.)
- show another one, for example: https://github.com/ansible/ansible-examples/blob/master/wordpress-nginx/roles/mysql/tasks/main.yml
- highlight conditionals, mention variables
- highlight loops
- execute a playbook, show results on command line
- :white_check_mark: easy to read
- :white_check_mark: tasks can be stacked
- :white_check_mark: playbooks script automation
- :white_check_mark: can be run on demand, multiple times
- :white_check_mark: can save even more time compared to the need to write scripts yourself
- :white_check_mark: But what are variables, where do they come from?

### interlude: variables [skip if no time]
- :question: How to map small differences between setups?
- :question: Aspects about a system - where to get them from?
- :question: External information stores - how to incorporate them?
- variables can replace values in playbooks
- or in file templates
- either manually defined, or from the machine or from external sources
- show setup command for single host
- show filter
- highlight `ansible_os_family`
- show another, static source for variables: vars file
- back to playbook
- :white_check_mark: variables are a great way to gain more flexibility
- :white_check_mark: many are predefined, manual addition possible
- :white_check_mark: manual either in playbooks, inventory, local on the machine, or via files
- :white_check_mark: variables from other machines can also be accessed
- :white_check_mark: variables make playbooks much more re-usable, saves even more time

### Playbooks, part 2
- take another example, like: https://github.com/ansible/ansible-examples/blob/master/lamp_simple_rhel7/roles/web/tasks/main.yml
- highlight include statement, they can be stacked as well
- explain handlers, for example in https://github.com/ansible/ansible-examples/blob/master/wordpress-nginx/roles/mysql/tasks/main.yml , explain links/connections with tasks
- explain concept of blocks like in Python; example in: http://docs.ansible.com/ansible/playbooks_blocks.html
- three different kinds: block, rescue, always
- can be used to group sets of tasks
- also error/exception handling
- :white_check_mark: multi OS, multi host, multi distribution
- :white_check_mark: tasks can not only be stacked, but also grouped
- :white_check_mark: in combination with variables very flexible
- :white_check_mark: templates add another level - either for configuration files or even for scripts
- :white_check_mark: entire setups can be described in Playbooks
- :white_check_mark: use case: complex tasks, especially orchestration of interdependent servers
- :white_check_mark: example: access LB, deactivate host, access monitoring, deactivate host, access host, stop service, update software, start service, check log files, activate host in monitoring, activate in LB
- :white_check_mark: can save time by automating even complex interconnected tasks, regular or not
- :white_check_mark: missing yet: not central place, no tracking what was executed when and by whom
- :notes: :wrench: Trigger build on Jenkins, get build, copy to staging, deactivate in load balancer, pause in monitoring, stop service, remove old version, deploy new version, start service, run tests; then unpause monitoring, active in load balancer, send mail to team.

### Tower, basics
- :question: Do you know which automated scripts are run right now in your environment? When, and by whom?
- show main interface
- explain concepts of projects (playbooks or sets of playbooks), show imported projects and show Git as example
- highlight possibility of importing different branches, and also commit ID
- explain inventories, show imported inventories; can be groups of groups; each group can be manually entered or be connected to online services or generic scripts
- explain that one node can be in multiple groups, even on the same level; no need for fixed tree structure
- job templates: they assign actual playbooks to specific inventories, ready to be executed when needed
- show job template, discuss also the possibility to on the fly change verbosity or "check" mode
- show surveys, that they can query data which are only in the knowledge of the executer
- notifications: can be used to inform via mail or web hook or even IRC when a job was executed - or only when it failed
- workflow manager: multiple jobs can be connected to each other, with conditions of fail or success
- show the workflow editor, it is a UI!
- connect some templates - even for different results
- jobs: when a job template is executed, a job comes up, the job has the results of the execution
- execute jobs, show results
- highlight additional information on left side
- click through play on the right side, show detail information pop up when clicked on task
- job execution cron-like: show scheduling in job templates
- show API: got to URL/api, pick organizations, got to bottom of page, create a new organization and show it
- :white_check_mark: web interface; REST API
- :white_check_mark: central place for all tasks, able to verify and document what was done and when
- :white_check_mark: can perform automation on its own (scheduling)
- :white_check_mark: different playbooks can be stitched together
- :white_check_mark: saves time via central, reusable place for all work
- :white_check_mark: missing yet: Playbooks still require user to have rights on target machines; how to separate?
- :notes: When user commits code, Jenkins runs build, afterwards Tower is called via REST and software is deployed.
- :necktie: automatically run network wide checks nightly from Tower, all can see results.


### Tower, RBAC
- :question: How can you run powerful scripts by people who know when to run them, without providing them too many rights?
- show setup module: organizations, teams, users
- show idea of credentials: cannot be looked up, stored in AES 128 in database
- can be different even for different templates (for example one as check, the other as execute)
- highlight or create teams and users, give permissions to a template as executor
- login as user - compare to admin login
- show portal mode
- mention dry runs
- :white_check_mark: separation of rights on machines and rights to execute playbooks is now given
- :white_check_mark: reliable tracking of who is allowed to execute what, and what was executed when by whom
- :white_check_mark: automation can now include teams and users without privileged access to machines
- :white_check_mark: use case: provide playbooks to be executed by developers, but without handing out login data
- :white_check_mark: example: destroy and re-setup development environment: access build server, publish correct branch, access dev - machines, download app from build server, stop old app, remove, deploy new, start, run deployment tests
- :white_check_mark: example: clean redis-cache once in a while
- :white_check_mark: saves time, since self service of automation tasks now possible
- :notes: Team development executes cache clean script without having actual access rights to the target system.
- :wrench: The facility manager executes the backup script weekly. ;)

## Break out sessions / advanced topics

### Callback URLs in Tower
- go to job templates, show callback URL
- explain how it can be called by freshly provisioned hosts
- requires dynamic inventory, explain why

### System Tracking
- go to inventory, show system tracking
- data are stored with each run
- can be compared over time

### Ansible roles
- tasks just describe what to do to get to some state - roles describe what needs to be there to have a state
- more abstract; idea is to make them shareable: everyone needs a web server, a database
- everything which describes a role: templates, tasks, variables, default values, etc.
- example role to set up Apache: https://github.com/geerlingguy/ansible-role-apache
- of course strongly depends on the qualities of the role writer
- there are very abstract, high quality roles out there, even OS agnostic
- Ansible Galaxy has community repository of roles: https://galaxy.ansible.com/
- use case: describe environments via re-usable roles
- :white_check_mark: modular approach of assigning tasks to machines
- :white_check_mark: still follows the Ansible way, still easy to write, adopt
- :white_check_mark: use case: standardized components in the data center to build apps (always MySQL, always nginx, etc.)
- :white_check_mark: example: Wordpress server has multiple roles: database, web server, scripting language tuning
- :white_check_mark: save time by reusing work of yourself and others

### REST API
- provides integration into other systems
- supports RBAC
- provides callback URLs for freshly deployed VMs to enable auto-provisioning
- 100 % of all functions are covered
- :white_check_mark: integrate Tower with other components of IT environment
- :white_check_mark: Tower can be the glue between various tools (REST API + can contact and call everything)
- :necktie: Workflow-Management system needs to execute statistics script on database servers, does this via REST API call against Tower which has the necessary playbooks and user rights to access the DB servers and gather statistics
- :notes: Jenkins sets up and destroys test environment during build; Tower has necessary playbooks and rights to bring up and down up2date test enviroments

### activity stream
- shows activity inside Tower
- audit tool to follow up on changes of job templates, git updates, etc.
- separation between work (and credentials) for machines and information about the central automation

### push vs pull
- push vs pull essentially depends on the requirements
- to keep in mind: many CM systems require a message bus these days...
- where pull is fixed, and only possibility: clone GIT repo on each machine, execute ansible locally
- there are modules and documentation in place
- :white_check_mark: Ansible pull is possible, but requires some extra work
- :white_check_mark: saves time since local network and policies do not have to be changed

### trouble shooting
- use blocks or ignore_errors and conditionals
- use debug msg to output variables
- do rollback (or for example shutdown) if needed
- `-vvv` on command line for ssh debugging
- `--step`
- `--check`
- `--diff`
- `--start-at-task`
- :wrench: Troubleshooting during development of playboooks

### tags
- tags vs conditionals
- limiting to certain tags
- `--skip-tags`

### Ansible console
- `ansible-console`
- ansible environment, can call all modules
- features tab completion

### ansible-lint
- can be used to verify playbooks

### CLI details
- `--list-tags` - show all available tags
- `--list-hosts` - list all hosts which would be affected
- `--syntax-check` - check if the syntax is right after all
- `--list-tasks` - list all tasks

### lookup
- can look up generic data from generic sources
- used like a variable
- CSV, DB, DNS, etc.
