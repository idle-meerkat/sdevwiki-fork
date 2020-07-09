# Access Control Lists (ACL) 
## Supported Actions and Keys
### ACL supported actions [3]
* drop
* trap
* pass
### ACL supported keys [3]
* Ingress interfaces (only switchdev interface)
* Protocol (ethertype)
* src_mac
* dst_mac
* src_ip (IPv4)
* dst_ip (IPv4)
* ip_proto (Tcp/Udp)
* src_port
* dst_port
* vlan_id

## ACL Configuration  
To offload Linux ACL configuration to netdevs, which represent Marvell switch ports, the TC flower filter [6] tool is used. Starting from Linux kernel v5.3  Netfilter [7] can also be used to offload ACL configuration to the netdevs, since it uses a common kernel API to do ACL offload configuration.  

## Create/Add ACL Rules  
Before configuring match rules on switch ports, you must first create the queuing disciplines (qdiscs) to which the flower classifier is attached. In order to prepare for the addition of flower rules, either add the ingress qdisc or clsact qdisc to the port by tc command:  
`$ sudo tc qdisc add dev DEV-NAME {ingress|clsact}`  
Where `DEV-NAME` is the switchdev interface name, e.g.: sw1p1.  

* To create ingress queueing disciplines (qdiscs):
`$ sudo tc qdisc add dev sw1p1 ingress`  
* To create the clsact qdisc:
`$ sudo tc qdisc add dev sw1p10 clsact`  
**NOTE**: there are no functional differences between these two types
* To list the existing qdiscs:
`$ tc qdisc show`  
Output example of the show command:
`qdisc ingress ffff: dev sw1p1 parent ffff:fff1 -------------`  
`qdisc clsact ffff: dev sw1p10 parent ffff:fff1` 

The rest of the examples in this document uses clsact qdisc and generic commands for ACL rule configuration. 
ACL rule configuration uses the following format:
`    tc [ OPTIONS ] qdisc [ add|show|delete ] dev DEV [ ingress|root ] [ handle qdisc-id ] [ protocol PROTO ] [ { prio|pref } PRIORITY ] flower [ flower specific parameters ]`
Where:
    `ingress`  is used for clsact qdisc.
    `root  is used for ingress qdisc.
