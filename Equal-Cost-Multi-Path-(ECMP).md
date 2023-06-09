The Linux networking stack supports Equal Cost Multi Path (ECMP) by adding multiple next-hops to the route. 
# ECMP Routes  
To install a route with multiple next-hops:  
` ip route add 192.168.2.0/24 nexthop via 192.168.0.2 dev sw1p1 weight 1 nexthop via 192.168.1.1 dev sw1p1 weight 1`  

To edit an existing next-hop configuration, you need to delete the entire ECMP route and then re-add it with a modified next-hop configuration.  

## Linkdown Next-hops  
When the netdevice goes down, the next-hops that are using it are marked with a `linkdown` flag. By default, the routing subsystem tries to forward packets through them. This behavior is defined by `sysctl setting net.ipv4.conf.default.ignore_routes_with_linkdown`.  

To set the kernel routing subsystem to exclude next-hops with link down from its ECMP group, use the following setting:  
`$ sysctl -w net.ipv4.conf.sw1p1.ignore_routes_with_linkdown=1`  

Setting `net.ipv4.conf.default.ignore_routes_with_linkdown=1` in the **sysctl** configuration files will make this option the default for all netdevices on the system.  

## Multi-path Hash Policy  
Setting **sysctl** to `net.ipv4.fib_multipath_hash_policy` controls which hash policy to use for multipath routes.  
This setting is ignored by the Switchdev driver. L4 (5 tuple) hash is always used. 
