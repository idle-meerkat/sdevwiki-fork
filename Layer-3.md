## L3 Interface

## Static Route

## Virtual Router Interface (VRF)

The VRF device, combined with IP rules, provides the ability to create virtual routing and forwarding domains (a.k.a. VRFs, VRF-lite) in the Linux network stack. Packets are received on an enslaved device and are switched to the VRF device in the IPv4 and IPv6 processing stacks, giving the impression that packets flow through the VRF device. Similarly, on egress routing rules are used to send packets to the VRF device driver before being sent out the actual interface. 

To create VRF device: 

`$ sudo ip link add vrf-blue type vrf table 10`
`$ sudo ip link set dev vrf-blue up`

### Enslaving Devices by VRF

In order to bind a device to a VRF, the device must be enslaved by the specified VRF device. For example:

`$ sudo ip link set dev DEV-NAME master vrf-blue`

   Where DEV-NAME is the switchdev interface name, e.g.: sw1p1 or its upper device like bridge or vlan interface. 

When the device is enslaved locally connected routes for it are automatically moved to the table associated with VRF device. Any additional routes depending on the enslaved device are dropped and will need to be reinserted to the VRF FIB table following the enslavement. 

Set a default route in each table to prevent lookup from leaking into other tables:

`$ sudo ip route add table 10 unreachable default`

### Adding Routes to a VRF Table

To add a routed VRF device specified table id must be passed to ip route command:

`$ sudo ip route add table 10...`

### Neighbor Entries in VRF

To list neighbor entries associated with devices enslaved to a VRF device add the master option to the ip command:

`$ ip neigh show vrf NAME`
`$ ip neigh show master NAME`

### Removing a Device From a VRF

To remove a device from a VRF:

`$ sudo ip link sw1p1 nomaster`

When a device is removed locally connected routes for it are automatically moved to the local table. Any additional routes depending on the enslaved device are dropped and will need to be reinserted to the VRF FIB table following the enslavement. 
