The following options are available in the prestera driver for LAG configuration:  
* Bonding method  

The Linux bonding driver provides a method for aggregating multiple network interfaces into a single logical "bonded" interface. The current version of the bonding driver is available in the “drivers/net/bonding” subdirectory of the most recent kernel source. It is recommended to configure bonding via `iproute2` (netlink) or `sysfs`, the old ifenslave control utility is obsolete.  
## Bonding Method
* To create a LAG bond device:  
`$ ip link add name bond1 type bond`  
* To remove a LAG bond device:  
`$ ip link del dev bond1`  
* To set operational mode:  
In general, the bond device can support different operational modes. The operational mode can be set using following command:  
`$ ip link set dev bond1 type bond`  
**NOTE**: LACP (802.3ad) is the only supported mode.  
* To add a member to a LAG:  
`$ ip link set dev sw1p1 master bond1`  
* To remove a member from a LAG:  
`$ ip link set dev sw1p1 nomaster`  
* To show LAG information:  
`$ cat /proc/net/bonding/bond1`  
 
## Enabling a Member in a LAG  
To enable a specific member in a LAG, the appropriate net_dev interface should be enabled:  
`$ ip link set sw1p1 up`  

## Disabling a Member in a LAG  
In order to disable a specific member in a LAG, enable the appropriate net_dev interface:  
`$ ip link set sw1p1 down`  

## Administrative Status of a LAG Interface  
In order to bring up/down the LAG interface, run the following command:  
`$ ip link set [bond1] [up|down]`  

## MAC Address Setting on a LAG Interface  
The MAC address of LAG interface is the address of first member of this LAG.  
In order to change it, run the following command:  
`$ ip link set [bond1] address [XX:XX:XX:XX:XX:XX]`  

## MTU Setting on a LAG Interface  
In order to set MTU on the LAG interface, run the following command:  
`$ ip link set [bond1] mtu [XX]`  

## Speed and Auto-Negotiation Setting on a LAG Interface  
The speed and auto-negotiation setting are not supported for bond interface.  
In order to configure the speed or auto-negotiation, complete the following actions:  
* Disable LAG interface (ip link set [bond1] down)
* Disable LAG members(ip link set [sw1pX] down)
* Configure speed or auto-negotiation for every LAG member
* Enable LAG members(ip link set [sw1pX] up)
* Enable LAG interface (ip link set [bond1] up)

## Adding a LAG Interface to a Bridge  
To add a LAG interface to a bridge, run the following command:  
`$ ip link set [bond1] master br0`  

## Adding a LAG Interface to a VLAN
To configure a VLAN on the LAG interface, run the following command:  
`$ bridge vlan add vid [XX] pvid dev [bond1]`  

## Setting Bridge LAG Interface Attributes  
To configure bridge port attributes for a LAG, run the following command:  
`$ bridge link set dev [bond1] learning off flood off`  

## Add IP address to a LAG
To configure an IP address for a LAG, run the following command:  
`$ ip addr add [192.168.1.2/24] dev [bond1]`  

## VRF  
To set a bond interface as a part of virtual router, run the following command:  
`$ ip link set dev [bond1] master [vrf_name]`  


