# Inventory Host vars

The host_vars are variables used mainly to facillitate deployment of a virtual machine.  
Each host has a seperate file to store these variables in.  
So all variables needed to deploy a host can reside in this file. However this is not a best  
practice....   

Typically, you specify ony the vers that are unique to a host in this file, the rest comes from  
the group_vars or the global variables, depending on the environment.  
 
Example host_vars for host1.example.com:

In the inventory, the file will be named: host1.example.com.yml and wil be placed under the directory  
host_vars in your inventory. The content of the file might look like the example below:  
````yaml
hostname: host1.example.com
os:
  name: rhel
  version: 1.0.0
primary_interface:
  ip: 192.168.10.100
  network: frontend1
size: s
type: vm
````
In the above example we see a number of variables, that define a VM ( hence the type ) and its properties.  
We see the following properties:  

* hostname	the hostname of the virtual machine
* os.name	It is a Redhat Enterprise Linux machine
* os.version 	The GIT version of the code to be used for this virtual machine
* ip		The ip address for the primary interface
* network	The network name to be used
* size 		The (T-shirt)sizing of the VM
* type		Its a VM

As these variables seem to describe a virtual machine, they are incomplete, there is no code that will  
deploy this machine with this definition, for there is data missing...  
Let's zoom in a bit...  

## hostname
This is probably one of two variables that is directly useable, a hostname must be unique.

## os.name
The Operating system is as such useable, but there is no version given, so we still don't know  
what to do, we need additional data to be able to build something like a VM.  

## os.version
There the version of the RHEL so could be named, but we chose to refer to the code version that deploys  
the os. So we still need additional data to be sure what to deploy.  

## ip
The ip address is the second variable that is directly useable from this file. But we need additional network  
variables to make it work as a full configuration.  

## network
This name is not the name of a physical network, but a reference to a variable that holds the rest of the network  
definition needed t configure a working interface.  

## size
The T-shirt sizing for the virtual machine, the translation of these sizes will be defined elsewhere in the inventory.  

## type
A type of machine, the definition here tellls us it will be a VM, but the code could cofigure a hardware machine as well.  


As you see here, not all specs of a host are in this file, if that were the case, the file would have been much larger and   
you would resort to copying this to a new file for a new host and change probably just 2 lines...  
A lot of data would be in this file (about 150 lines), you would never touch until...the environment changes...  
Then you would have a lot of work changing all these lines in all files...   

This is where the group_vars come in..


After adding the host_vars to the inventory, the tree would look like:
````
.
├── group_vars
│   └── all
│       ├── management.yml
│       └── organization.yml
├── hosts.ini
└── host_vars
    ├── host1.example.com
    ├── host2.example.com
    └── host3.example.com
````
This could be a working inventory, if all variables to deploy hosts were in the host_vars files for each host.  
But since that is not a best practice, we won't even show you.  
The next step is adding a level of group_vars, in which we will define missing variables.  

[Adding group_vars](inventory_group_vars.md)  
[Back](inventory_host_vars.md)  

