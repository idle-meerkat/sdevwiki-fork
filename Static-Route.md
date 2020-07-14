When an IP address in assigned to a switchDev interface or its master bridge - a routing interface is created.  
For each address and its broadcast and network addresses, traps are configured in PP to make sure that appropriate packets are delivered to the kernel.  
For example:  
`$ sudo ip addr add 192.168.1.1/24 sw1p1`  
`$ sudo ip link set dev sw1p1 up`  
`$ sudo ip addr add 192.168.2.1/24 sw1p2`  
`$ sudo ip link set dev sw1p1 up`  
**NOTE**: A configuration is applied only after the interface is set to admin UP  

## Bridge Routing Interface
To create router interfaces on top of bridge netdev, an IP address has to be assigned.  
For .1Q bridge, a router interface can be created for each of its upper VLAN devices.  
For .1D a router interface can be created to bridge itself only.  
.1D Bridge example:  
`$ ip link add name br0 type bridge`  
`$ ip link set sw1p3 master br0`   
`$ ip link set sw1p3 up`  
`$ ip addr add 192.168.3.1/24 dev br0`  
`$ ip link set br0 up`  
.1Q Bridge example:  
`$ ip link add name br1 type bridge vlan filtering 1`  
`$ ip link set sw1p4 master br0`  
`$ ip link set sw1p4 up`  
`$ ip link add link br0 name br0.10 type vlan id 10`  
`$ bridge vlan add dev br0 vid 10 self`  
`$ ip addr ass 192.1684.1/24 dev br0.10`  

## Nexthop Routes  
Static routes are added using ip route command, provided by iprote2 package.  
Example of adding static routes:  
`$ sudo ip route add 10.10.1.0/24 via 192.168.1.2 dev sw1p1`  
To show Linux Routing table:  
`$ sudo ip route`  
`$ 192.168.0.0/24 dev sw1p1  proto kernel  scope link  src`  
`$ 192.168.0.1/24 offload`  
`$ 192.168.1.0/24 dev sw1p2  proto kernel  scope link  src`  
`$ 192.168.1.1/24 offload`  
`$ 192.168.2.0/24 via 192.168.0.2 dev sw1p1 offload`  
Entries marked with offload flag are programmed to the PP.   
