# Inventory Group vars

Group_vars can add a lot of dimension to you inventory, there is a right way  
and, if there is a right way, there is also a less right way (maybe not wrong  
but labour intensive and error prone).  

You can add hosts to a group, and add variables (properties) to that group.  
These properties will be known to any member host of that particular group.  

So we will extend our previous inventory by adding a group file to the inventory  
and see what happens...  

The tree view of the inventory after adding the group_vars/group1.yml file:  
````
.
├── group_vars
│   ├── all
│   │   ├── management.yml
│   │   └── organization.yml
│   └── group1.yml
├── hosts.ini
└── host_vars
    ├── host1.example.com
    ├── host2.example.com
    └── host3.example.com
````
In this group1.yml file we added some properties that are common for all hosts in this group.

````yaml
---
fontend1:
  network: 192.168.10.0
  netmask: 255.255.255.0
  default: 192.168.10.1
  dns: 8.8.8.8
  
sys_type: linux
rhel
  major_version: 8

s: 
  memory: 1024
  cpu: 1
m:
  memory: 2048
  cpu: 2
l:
  memory: 4096
  cpu: 4
````
just adding the file, will not change anything, we still need to add the systems to the group, this  
can be done by editing the hosts.ini and adding the group there and add the hosts to that group:  
````
[ALL]
host3.example.com

[group1]
host1.example.com
host2.example.com
````
As you see in the above example, we added two hosts to the goup1 and added a third host which is not a member  
of that group.  

This means that the variables defined in the file group1.yml will be availlable in host1 and host2, but not in host3.  
Those variables can be used in any playbook you run against these two hosts.  
Now we add a second group.. group_vars/group2.yml  
````yaml
---
net_network: 192.168.11.0
net_netmask: 255.255.255.0
net_default: 192.168.11.1
net_dns: 8.8.8.8

sys_type: linux
rhel
  major_version: 8

s:
  memory: 1024
  cpu: 1
m:
  memory: 2048
  cpu: 2
l:
  memory: 4096
  cpu: 4
````
and we make the third host part of that group through hosts.ini:
````
[ALL]

[group1]
host1.example.com
host2.example.com

[group2]
host3.example.com
````
We can now use a playbook that uses the network variables to do something, without the need to create   
2 separate specific plays for each network. This used with many variables can be very powerfull.  

But there is a pitfall here, you already can see duplicate values in the group_vars files, and with many more   
variables added to these files, you will find that managing these will consume a lot of time and might lead to  
errors as some will get changed and others forgotten.  
We will address this in the next chapter.  

[Adding layers to your inventory](inventory_multi_layers.md)  
[Back](inventory_group_vars.md)  

