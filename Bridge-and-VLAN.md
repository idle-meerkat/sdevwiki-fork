Linux bridge is a way to connect two Ethernet segments together in a protocol independent way. Packets are forwarded based on the Ethernet address, rather than the IP address (like a router). Since forwarding is done at Layer 2, all protocols can go transparently through a bridge.  
The Linux bridge code implements a subset of the ANSI/IEEE 802.1d standard. [1]. The original Linux bridging was first done in Linux 2.2, then rewritten by Lennert Buytenhek. The code for bridging has been integrated into 2.4 and 2.6 kernel series.  

Linux Bridge supports the following bridges types, defined by 1EEE 802.1Q standard:  
- VLAN Unaware Bridge : Bridge that does not recognize VLAN Tagged Packets. This is the default.
- VLAN Aware Bridge : Bridge that recognizes packets with VLAN Tag and Insert or Remove Tag Headers.

## Bridge Device Configuration
* Create a Bridge (By default linux bridge is VLAN unaware):  
`$ ip link add name br0 type bridge`
* Delete a Bridge:  
  `$ip link del dev br0`
* Make a Bridge VLAN Aware:  
`$ ip link set dev br0 type bridge vlan_filtering 1`

A Linux bridge forwards packets based on FDB data.  
* To display bridge FDB data:  
`$ bridge fdb`  
Output:  
`52:54:00:12:35:01 dev sw1p1 master br0 permanent`  
`00:02:00:00:02:00 dev sw1p1 master br0 offload`   
`00:02:00:00:02:00 dev sw1p1 self`  
`52:54:00:12:35:02 dev sw1p2 master br0 permanent`  
`00:02:00:00:03:00 dev sw1p2 master br0 offload`  
`00:02:00:00:03:00 dev sw1p2 self`  
`33:33:00:00:00:01 dev eth0 self permanent`  
`01:00:5e:00:00:01 dev eth0 self permanent`  
`33:33:ff:00:00:00 dev eth0 self permanent`  
`01:80:c2:00:00:0e dev eth0 self permanent`  
`33:33:00:00:00:01 dev br0 self permanent`  
`01:00:5e:00:00:01 dev br0 self permanent`  
`33:33:ff:12:35:01 dev br0 self permanent`  

Entries with `offload` flag are externally learned entries (hardware FDB)


## VLAN Device Configuration
* Configure VLAN device on top of a switch port  
`$ ip link add link sw0p1 name sw0p1.5 type vlan id 5`
* Delete a VLAN device  
`$ ip link del dev sw0p1.5`

## Bridge Membership Configuration
* Adding a net device port to the bridge  
`$ ip link set dev sw0p1 master br`  
* Removing a net device port from the bridge   
`$ ip link set dev sw0p1 nomaster`  
## VLAN Aware Configuration  
* Adding 2 ports sw0p1 and sw0p2 to a VLAN aware bridge  
`$ ip link set dev br0 type bridge vlan_filtering 1`  
`$ ip link set dev sw0p1 master br0`  
`$ ip link set dev sw0p2 master br0`  
* Show PVID of a port. By default, ingress/egress untagged packets use the default port PVID.  
`$ bridge vlan show dev sw0p1` 
`port         vlan ids` 
`sw0p1         1     PVID Egress Untagged`  
* Add a port to VLAN  
`$ bridge vlan add vid 20 dev sw0p1`  
`$ bridge vlan show dev sw0p1`  
Output:  
`port         vlan ids`  
`sw0p1           1   PVID Egress Untagged 20`  
* Change the PVID of the Port using the PVID flag  
`$ bridge vlan add vid 20 dev sw0p1 pvid`  
`$ bridge vlan show dev sw1p5`  
Output:  
`port         vlan ids`  
`sw1p5          1 Egress Untagged 20 PVID`  
## VLAN Unaware Configurations  
Multiple VLAN-unaware bridges can be created. This can be used, for example, to bridge two VLAN devices with different VIDs.  
`$ ip link add link sw0p1 name sw0p1.10 type vlan id 10`  
`$ ip link add link sw0p2 name sw0p2.20 type vlan id 20`  
`$ ip link add name br1 type bridge`  
`$ ip link set dev sw0p1.10 master br1`  
`$ ip link set dev sw0p2.20 master br1`  
## Bridge Port Configurations  
The following bridge port attributes can be configured:
* Learning – Controls whether a given port will learn MAC addresses from received traffic or not.  
 If learning is off, the bridge will end up flooding any traffic for which it has no FDB entry. By default this flag is on.
* Flooding – controls whether a given port floods unicast traffic for which there is no FDB entry. By default, this flag is on.  

To set learning and flooding attributes:  
`$ bridge link set dev DEV learning {on/off} flood {on/off}`  

## Static and Sticky FDB Entries
Recently, a new entry type “sticky” was introduced in Linux bridge. In Linux, a static FDB can be roamed to a different port via learning. Sticky FDB entries cannot be moved. 
Because of the current infrastructure of the switchdev FDB notification chain, there is no indication which type of entry was added. Thus, all entries are treated as static.  Once the first upstream patch is published, a request with changes to switchdev fdb notification the chain will add support for the entry type.

* To add a static FDB entry:  
`$ bridge fdb add ADDR dev DEV master static [vlan VID]`  
* To delete the static FDB entry:  
`$ bridge fdb add ADDR dev DEV master static [vlan VID]`  

# Bridge and VLAN Limitations
* Changing bridge mode while switch ports are enslaved to bridge generates an error.
* Switchdev does not put all ports into a bridge by default.

