# Adding layers to your inventory
As we have seen, there are losts of variables in an inventory. We need to arrange   
them into a structure that is maintainable,so we don't get stuck copying them  
hundreds of times and make changes a crime.  
So how do we do this:  

Every organization is different, not the least in IT. So there is no guaranteed  
working cookbook that will work everywhere. Here it comes.....  
You know best how your organization is structured IT wise, its up to you to analyse  
and structure the data into an inventory.  

In this page, we will try to give you the handles to get into the modelling of the   
data in an inventory, after that it is entirely up to you. You shouldn't do this on   
your own, you'll need multiple departments to figure out all the variables for your organization.  

Data you will need:  

* datacenters
* networks
* authentication servers
* vmware hosts/clusters/vcenter(s)
* dns
* time servers
* logging servers
* mail providers
* management servers
* monitoring servers
* hostnames
* ip addresses
* locations
* sub locations
* client sites (optional)

These are just an example of what you might need for your organization and each topic holds  
a specific set of data that can hold duplicates, if you don't organize them in the right way.  
The above can even exist in multiple environments, like dev, acceptance and production.  

You will have to find the logic in this and place each variable on the right spot, so it occurs  
only once with the same value.   

In the (old school)database world, where storage was expensive, there was a design process 'data normalisation'.  
This was done by data analists, to ensure that a value was only stored once in the entire database, so the least  
amount of storage was used to store complex data. Guess what.... the same principal allpies to an inventory  
and can help us manage our inventory.  

## Inventory hierachy

We should look at our inventory as a tree of data that is inherited as we go deeper into the inventory tree.  
Lets give a small example:  
````
└── organization
    ├── authentication.yml
    ├── central_date_time.yml
    ├── central_mail_provider.yml
    ├── central_management_servers.yml
    └── datacenter
        ├── authentication.yml
        ├── deployment.yml
        ├── logging_server.yml
        ├── monitoring.yml
        └── network
            └── host1.yml

````
In the above sample we see a tree of files that represent an inventory. The host in this example inherits all values  
in the files above the host, this defines much more than the host itself, but also its working environment, which  
can be automated through playbooks.  

Try to organise your environment in the way show in the above example, an start doing this for 1 host first.  
If you have this for 1 host, try adding more hosts in the same datacenter. If done correctly, you won't need many   
variables to define the host, in the ideal situation, you should only add the host file with a minimal set of  
values.  

If this is accomplished, now add a network in the same datacenter, and adding hosts to that network should be just as   
easy.  

Extend the same procedure to adding datacenters...even customer sites, and think of where to put the branch in your  
inventory.  
````
.
└── organization
    ├── authentication.yml
    ├── central_date_time.yml
    ├── central_mail_provider.yml
    ├── central_management_servers.yml
    ├── datacenter
    │   ├── authentication.yml
    │   ├── deployment.yml
    │   ├── logging_server.yml
    │   ├── monitoring.yml
    │   ├── network
    │   │   ├── host1.yml
    │   │   └── host2.yml
    │   └── network2
    │       ├── host3.yml
    │       └── host4.yml
    └── datacenter2
        ├── authentication.yml
        ├── deployment.yml
        ├── logging_server.yml
        ├── monitoring.yml
        └── network
            └── host5.yml

````

## How to write this as an inventory
We now know the hierarchie tree for the data. The next step is to convert this data tree into an inventory.  

Everything on the organization level is apparantly global for this organization, so these file will land in  
the ALL section of the inventory:  
````
.
├── group_vars
│   ├── all
│   │   ├── authentication.yml
│   │   ├── central_date_time.yml
│   │   ├── central_mail_provider.yml
│   │   └── central_management.yml
│   ├── dc1_datacenter.yml
│   ├── dc1_network1.yml
│   ├── dc1_network2.yml
│   ├── dc2_datacenter.yml
│   └── dc2_network1.yml
├── hosts.ini
└── host_vars
    ├── host1.example.com
    ├── host2.example.com
    ├── host3.example.com
    ├── host4.example.com
    └── host5.example.com
````
The inventory looks different from the hierarchie structure in the previous section.  
The first part in the all section is not so different, but te second part is.  
You see that in the inventory tree view, all the goup_vars are unstructured in the directory.  
This means the structure must be defined somewhere else, and that is true.  

All hosts are in the host_vars directory, as you can see each host must have a unique name  
for this to work as an inventory.  

The naming of the files in the inventory here is crucial, if there are hundreds of files, naming  
them according to structure is key to keep them together. The naming here is an example, your  
organization will have other requirements or other names..  

The one file that ties this whole inventory together is hosts.ini (or hosts.yml).  
In this file you define the structure of your inventory and where the machines are placed.  
````
[ALL]

[dc1_datacenter]

[dc2_datacenter]

[dc1_datacenter:children]
dc1_network1
dc1_network2

[dc2_datacenter:children]
dc2_network1

[dc1_network1]
host1.example.com
host2.example.com

[dc1_network2]
host3.example.com

[dc2_network1]
host4.example.com
host5.example.com
````
In the above example, host1.example.com inherits vars from the following files:
* dc1_network1.yml
* dc1_datacenter.yml
* all files in the group_vars/all directory

Host5.example.com is in a different datacenter and inherits from the following files:
* dc2_network1.yml
* dc2_datacenter.yml
* all files in the group_vars/all directory

So host5 shall have a different configuration from host1 and host2 on the following items or  
even more as defined in the datacenter.yml or network.yml file:  
- ip address space / subnet / gateway
- will deploy on a different vmware cluster
- will send logging to another server
- will be in an other domain for logins
- have local monitoring servers in dc2

The code to deploy the virtual machine can be the same in all locations, because we have diffentiated  
this in the inventory. This should always be considerd when creating an inventory.  

## Test your inventory

Test your inventory by running th ansible-inventory command and verify if the hosts have all variables your code needs  
for deployment and configuration of the machine in its environment.  
If that is the case, you will probably need to adapt the code to your new inventory structure. But keeping your inventory  
will be much easier now.  

testing your inventory can be as simple as:

`ansible-inventory -i <path_to_inventory> --host <host5.example.com>`

It will output all variables for that host that are found in the inventory

`ansible-inventory -i <path_to_inventory> --graph`

Will produce the following output:
````
$ ansible-inventory -i hosts.ini --graph 
@all:
  |--@ALL:
  |--@dc1_datacenter:
  |  |--@dc1_network1:
  |  |  |--host1.example.com
  |  |  |--host2.example.com
  |  |--@dc1_network2:
  |  |  |--host3.example.com
  |--@dc2_datacenter:
  |  |--@dc2_network1:
  |  |  |--host4.example.com
  |  |  |--host5.example.com
  |--@ungrouped:

````
In the above example output, you see that the structure we defined earlier in our analysis is very  
simular to the output of the graph, this tells us we have done something right here :-)  
You can add layers by writing more group_vars.yml files and add them to your hosts.ini in the same way  
we added these layers.  
In the graph output they will be added too, and with more layers comes more control...  
Be sure to have a naming convention for these groups in place, so you will know where you put a variable.  
Document your layout and your changes...  

[Back to start](README.md)
