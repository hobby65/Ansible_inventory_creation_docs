# Inventory Basics

The most basic version of an inventory is a single file, this file can be written  
in 2 formats:

* ini format  
* yaml format

We will use the ini format, as it's a well known format, anyone can read an ini file. 

The hosts.ini(or hosts.yml) is the heart of any inventory, this defines what hosts are  
in the inventory (eg. usable by ansible) and the relations between the hosts and the rest  
of the inventory but that comes later.  

As said before, the simplest form of an inventory is a single file, that is the hosts.ini  
by itself.  

Example hosts.ini:
````
[ALL]
host1.example.com
host2.example.com
````

In its most basic form, this is a working version of an inventory, both the hosts in this file  
can be targeted by ansible using the hostname. Provided the hostname is resolvable through dns.  

a playbook can be run against one of these machines with the following command:  
````
ansible-playbook play.yml -i hosts.ini -e hosts=host1.example.com
````
The playbook has no other variables availlable then the hostname, so all other variable must be in  
the playbook itself. This is not very manageble when you environment consists of several hundreds  
of machines, every machine has at least some different parameters en config. An inventory like this  
would force you to make a special custom playbook for every machine.  

This is not how automation is meant to work, you should be able to write a playbook that is generic  
for all or at least a group of hosts.  

This is where the group_vars kick in...  

[Adding global variables](inventory_all.md)  
[Back](README.md)  
