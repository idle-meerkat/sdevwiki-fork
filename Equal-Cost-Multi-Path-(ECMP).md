The Linux networking stack supports Equal Cost Multi Path (ECMP) by adding multiple next-hops to the route. 
# ECMP Routes  
To install a route with multiple nexthops:  
` ip route add 192.168.2.0/24 nexthop via 192.168.0.2 dev sw1p1 weight 1 nexthop via 192.168.1.1 dev sw1p1 weight 1`  

To edit an existing nexthop configuration, you need to delete the entire ECMP route and then re-add it with a modified nexthop configuration.  

## Lindown Nexthops  
When the netdevice goes down, the nexthops that are using it are marked with a `linkdown` flag. By default, the routing subsystem tries to forward packets through them. This behavior is defined by `sysctl setting net.ipv4.conf.default.ignore_routes_with_linkdown`.  

To set the kernel routing subsystem to exclude nexthops with link down from its ECMP group, use the following setting:  
`$ sysctl -w net.ipv4.conf.sw1p1.ignore_routes_with_linkdown=1`  

Setting `net.ipv4.conf.default.ignore_routes_with_linkdown=1` in the **sysctl** configuration files will make this option the default for all netdevices on the system.  

## Multi-path Hash Policy  
Setting **sysctl** to `net.ipv4.fib_multipath_hash_policy` controls which hash policy to use for multipath routes. These are the possible values:  
* 0 - (Layer 3) means only the source and destination IP addresses are used 
* 1 - (Layer 4) 5-tuple is used: the source and destination IP addresses, the source and destination ports, and the IP protocol. 

**Important**: This setting is ignored by the switchdev driver. L4 (5 tuple) hash is always used. 
