## L3 Interface

## Static Route
When an IP address in assigned to a Switchdev interface or its master bridge - a routing interface is created. 

For each address and its broadcast and network addresses, traps are configured in PP to make sure that appropriate packets are delivered to the kernel 

For example:

`$ sudo ip addr add 192.168.1.1/24 sw1p1`<br />
`$ sudo ip link set dev sw1p1 up`<br />
`$ sudo ip addr add 192.168.2.1/24 sw1p2`<br />
`$ sudo ip link set dev sw1p1 up`

NOTE: A configuration is applied only after the interface is set to admin UP 

### Bridge Routing Interface

To create router interfaces on top of bridge netdev, an IP address has to be assigned. 
For .1Q bridge, a router interface can be created for each of its upper VLAN device.

For .1D to bridge itself only. 

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

### Nexthop Routes

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

Entries marked with offload flag are programmed to the PP 

 
## Virtual Router Interface (VRF)

The VRF device, combined with IP rules, provides the ability to create virtual routing and forwarding domains (a.k.a. VRFs, VRF-lite) in the Linux network stack. Packets are received on an enslaved device and are switched to the VRF device in the IPv4 and IPv6 processing stacks, giving the impression that packets flow through the VRF device. Similarly, on egress routing rules are used to send packets to the VRF device driver before being sent out the actual interface. 

To create a VRF device: 

`$ sudo ip link add vrf-blue type vrf table 10`
`$ sudo ip link set dev vrf-blue up`

### Enslaving Devices by VRF

In order to bind a device to a VRF, the device must be enslaved by the specified VRF device. For example:

`$ sudo ip link set dev DEV-NAME master vrf-blue`

   Where DEV-NAME is the switchdev interface name, e.g.: sw1p1 or its upper device like bridge or vlan interface. 

When the device is enslaved, locally connected routes for it are automatically moved to the table associated with VRF device. Any additional routes depending on the enslaved device are dropped and will need to be reinserted to the VRF FIB table following the enslavement. 

Set a default route in each table to prevent lookup from leaking into other tables:

`$ sudo ip route add table 10 unreachable default`

### Adding Routes to a VRF Table

To add a routed VRF device, the specified table ID must be passed to the IP route command:

`$ sudo ip route add table 10...`

### Neighbor Entries in VRF

To list neighbor entries associated with devices enslaved to a VRF device, add the master option to the IP command:

`$ ip neigh show vrf NAME`
`$ ip neigh show master NAME`

### Removing a Device From a VRF

To remove a device from a VRF:

`$ sudo ip link sw1p1 nomaster`

When a device is removed locally, connected routes for it are automatically moved to the local table. Any additional routes depending on the enslaved device are dropped and will need to be reinserted to the VRF FIB table following the enslavement. 
