# Introduction
An inventory is a set of variables that defines an environment for ansible.  
In this environment ansible finds the values needed to perform actions on machines  
or a group of machines. There will be no tasks in an inventory, only values.  

An inventory is defined in yaml or json format, whatever you prefer..  
We will discuss the yaml format for readability, but all yaml files can easily  
be converted into json format.  

In this documentation we will take you through the process of creating an inventory   
in its simplest form, to an inventory with multiple sites with different parameters  
for subsets and how to arrange them in your inventory.  

This guide is by no means a 'must do', but it will help you to setup an inventory  
that can handle multiple sites in one inventory.  

The inventory described here, can be a git repository, but can also be the result of  
a script that generates the result as described. I will describe the resulting inventory  
only, for there are too many ways and sources to describe a script that will deliver the   
desired output.  
Having the desired output, will help you to make the script for a 'dynamic' inventory  
from the sources that are availlable to you.  

While it is possible to create 1 inventory that holds 'all' of your environments, it is  
maybe not advisable to do this from scratch, an error in your inventory, might disable  
your ability to run playbooks in your entire organization, thus stopping all automation until  
this error is fixed. In a later stage these inventories can be merged, if required.  

On the [ansible documentation](https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html) site, the creation of   
an inventory is described in detail. While reading this is almost mandatory, you can get lost in the possibilities.  
In these pages we will provide you with step-by-step information and the reasons why..  

## Inventory structure
An inventory is basically a directory structure with files that describe the various objects in the inventory.

The (directory) structure 
````
.
├── group_vars
│   └── all
└── host_vars

````
The basic structure is shown above, this is as seen from the inventory root directory.  
This is the structure we will be filling with files to create a working version of an inventory. 

Next step is the inventory basics..

[The basics of an inventory](inventory_basics.md)  
[Adding global variables](inventory_all.md)  
[Adding host specific variables](inventory_host_vars.md)  
[Adding group vars](inventory_group_vars.md)  
[Multi layer inventory](inventory_multi_layers.md)  


