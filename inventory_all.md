# Inventory Global vars

In a large environment there are almost always variables/properties that must be  
set on a global level, like the name of the organization among other things.  
This can be done by adding these to a yaml file in the group_vars/all directory.  
The files in this directory will all be included by default as a machine is targeted  
by ansible.  

Typically some of variables found in all/\*.yml files:

* corporate variables, like name
* central management servers
* time servers
* authentication providers
* ....

It is a best practice to separate these variables into files that have descriptive names.

so the file for the corporate variables could be named like:

organization.yml

````yaml
---
organization_name: example
organization_domain: example.com
....
````

The file for central management servers:

management.yml
````yaml
---
monitoring_server: 192.168.10.2
logging_sever: 192.168.10.11
logon_server: 192.168.10.4
````

As you add files,you will be adding variables, be sure to keep those names either unique  
or expect those to be overridden in maybe a lower level..  
Our inventory tree looks as follows now:  
````
.
├── group_vars
│   ├── all
│   │   ├── management.yml
│   │   └── organization.yml
│   └── all.yml
├── hosts.ini
└── host_vars
````


[Adding host specific variables](inventory_host_vars.md)  
[back](README.md)  
