# Introduction: Linux MAC VLAN Interface  
The MAC VLAN can be thought of as a reverse VLAN under Linux. Instead of taking a single interface on the OS side of a network interface and mapping it to multiple virtual networks on the Network side of the interface (one-to-many), a MAC VLAN takes a single network interface and creates multiple virtual ones with different MAC addresses (many-to-one).

The `macvlan` Rx handler re-injects packets with destination MAC addresses as the MAC VLANs to the Rx path so that they are picked up by the IPvX protocol handlers, and undergo an L3 lookup. Note that the driver prevents the MAC VLANs from being enslaved to other devices, to ensure that the packets are picked up by the protocol handler and not by another Rx handler. 

* To create a MAC VLAN device:  
`$ sudo ip link add link sw1p1 type macvlan`  
`$ sudo ip link add link sw1p1 mac1 address 00:11:22:33:44:55 type macvlan`  

# Virtual Router Redundancy Protocol (VRRP)
The Virtual Router Redundancy Protocol (VRRP) is a computer networking protocol that enables automatic assignment of available Internet Protocol (IP) routers to participating hosts. This increases the availability and reliability of routing paths via automatic default gateway selections on an IP subnetwork.  
The protocol achieves this by creating virtual routers, which are an abstract representation of multiple routers, i.e. master and backup routers, acting as a group. The virtual router is assigned to act as a default gateway of participating hosts, instead of a physical router. If the physical router that is routing packets on behalf of the virtual router fails, another physical router is selected to automatically replace it. The physical router that is forwarding packets at any given time is called the master router.[7]  

## VRRP Implementation in Linux
In Linux, VRRP is usually implemented by configuring a MAC VLAN with the virtual router MAC on top of the router interface that is connected to the host / LAN. The MAC VLAN on the master router is assigned the virtual IP (VIP) that the host uses as its gateway.  
The Switchdev open source implements VRRP using the keepalived service.  

## Generic VRRP Network Topology
The following image shows two redundant VRRP-enabled routers sharing the same L2 domain with the host.
![VRRP Network Topology](images/vrrp_network_topology.jpg)  

## Virtual IP and Virtual MAC
A virtual router must use 00-00-5E-00-01-XX as its MAC address. The last byte of the address (XX) is the Virtual Router IDentifier (VRID), which is different for each virtual router in the network. This address is used by only one physical router at a time, and it will reply with this MAC address when an ARP request is sent for the virtual router's IP address.  
Physical routers within the virtual router must communicate within themselves using packets with multicast IP address 224.0.0.18 and IP protocol number 112.  
Routers have a priority of between 1 and 254 and the router with the highest priority will become the master. The default priority is 100. [7]  

## Keepalived Configuration  
Example configuration of keepalived:  
`global_defs {`  
` vrrp_garp_master_refresh 60`  
`}`  

`vrrp_instance vrrp_test {`  
` state BACKUP`  
` interface br0`  
` virtual_router_id 10`  
` priority 150`  
` version 3`  
` advert_int 0.1`  
` use_vmac`  
` virtual_ipaddress {`  
`  192.168.1.1`  
`}`  

Where:   
`interface <interface name>`		# interface to bind  
`virtual_router_id <id>`	# id of Virtual Router   
`priority <prio>`			# priority of current instance  
`version <ver>`				# version of VRRP protocol  
`advert_int <interval>`		# VRRP Advert interval  
`use_vmac`				# Use VRRP Virtual MAC  
`virtual_ipaddress {VIP}`		# Virtual IP addresses  

