Port isolation (also called ‘protected ports’ or ‘pvlan edge’) enables you to control how traffic is forwarded between ports that are connected at the same bridge domain. Traffic between isolated ports is prevented regardless of their VLAN membership. However, isolated ports can still communicate with non-isolated ports such as uplink to access WAN.  

Port Isolation enables/disables traffic forwarding between any pair of ingress/egress ports in a single-device topology and back-to-back topology. Traffic should be paired based on the source ePort that represents the source physical port.

The following image shows how port isolation is implemented.
![Port Isolation Overview](images/port_isolation_overview.png)

## Linux Support
Port isolation is supported by Linux kernels version 4.18, or higher. The support is provided on the port level (that is, not per port and VLAN). Isolation is always applied on netdev switch ports.

Linux Command:

`bridge link set dev DEV [isolated {on|off}]`



Following are examples of the linux command above.  

To isolate a bridge port, enter the following command: 
```
$ bridge link set dev sw1p1 isolated on 
```
Isolating a single bridge port has no effect until at least one other port of the same bridge is also isolated: 
```
# bridge link set dev sw1p2 isolated on 
```
Now the bridge ports sw1p1 and sw1p2 are unable to send or receive any packet to or from each other. However, these interfaces can still communicate normally with any other unisolated port on the bridge. 

## NOTES: 
* Port isolation is done on the port level, affecting all traffic between isolated ports regardless of their VLAN membership.
* The bridge's self-interface is not subject to port isolation.
* This setting is available through (rt) netlink, and it has no equivalent in ioctl/brctl. 
