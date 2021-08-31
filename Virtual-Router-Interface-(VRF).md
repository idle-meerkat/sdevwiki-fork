
The VRF device, combined with IP rules, provides the ability to create virtual routing and forwarding domains (also called VRFs, VRF-lite) in the Linux network stack. Packets are received on an bound device and are switched to the VRF device in the IPv4 and IPv6 processing stacks, giving the impression that packets flow through the VRF device. Similarly, on egress, routing rules are used to send packets to the VRF device driver before being sent out the actual interface.  

To create a VRF device:  
`$ sudo ip link add vrf-blue type vrf table 10`  
`$ sudo ip link set dev vrf-blue up`  

## Binding Devices by VRF  
In order to bind a device to a VRF, the device must be bound to the specified VRF device. For example:  
`$ sudo ip link set dev DEV-NAME master vrf-blue`  
   Where `DEV-NAME` is the Switchdev interface name, e.g.: sw1p1 or its upper device like bridge or vlan interface.  

When the device is bound, locally connected routes for it are automatically moved to the table associated with VRF device. Any additional routes depending on the bound device are dropped and will need to be reinserted to the VRF FIB table after binding.  

To set a default route in each table to prevent lookup from leaking into other tables:  
`$ sudo ip route add table 10 unreachable default`

## Adding Routes to a VRF Table  
To add a routed VRF device, the specified table ID must be passed to the IP route command:  
`$ sudo ip route add table 10...`  

## Neighbor Entries in VRF  
To list neighbor entries associated with devices bound to a VRF device, add the master option to the IP command:  
`$ ip neigh show vrf NAME`  
`$ ip neigh show master NAME`  

## Removing a Device From a VRF  
To remove a device from a VRF:  
`$ sudo ip link sw1p1 nomaster`  
When a device is removed locally, connected routes for it are automatically moved to the local table. Any additional routes depending on the bound device are dropped and will need to be reinserted to the VRF FIB table after the binding.  
