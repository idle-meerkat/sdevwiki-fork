## Bridge and VLAN
Linux Bridge supports 2 types of bridges defines by 1EEE 802.1Q standard:
VLAN Unaware Bridge : Bridge that does not recognize VLAN Tagged Packets
VLAN Aware Bridge : Bridge that recognizes packets with VLAN Tag and Insert or Remove Tag Headers
By default Linux Bridge  is VLAN Unaware bridge, but this is configurable.
## Bridge Device Configuration
* Create a Bridge (By default linux bridge is VLAN unaware)
$ ip link add name br0 type bridge
* Delete a Bridge
$ip link del dev br0
* Make a Bridge VLAN Aware
$ ip link set dev br0 type bridge vlan_filtering 1

## VLAN Device Configuration
* Configure VLAN device on top of a switch port
$ ip link add link sw0p1 name sw0p1.5 type vlan id 5
* Delete a VLAN device
$ ip link del dev sw0p1.5
## Bridge Membership Configuration
* Adding a net device port to the bridge
$ ip link set dev sw0p1 master br
* Removing a net device port from the bridge
$ ip link set dev sw0p1 nomaster
## VLAN Aware Configuration
* Adding 2 ports sw0p1 and sw0p2 to a VLAN aware bridge
$ ip link set dev br0 type bridge vlan_filtering 1 
$ ip link set dev sw0p1 master br0 
$ ip link set dev sw0p2 master br0
* Show PVID of a port. By default for ingress/egress untagged packets, default port PVID is used.
$ bridge vlan show dev sw0p1 
port         vlan ids 
sw0p1         1     PVID Egress Untagged
* Add a port to VLAN
$ bridge vlan add vid 20 dev sw0p1 
$ bridge vlan show dev sw0p1 
port         vlan ids 
sw0p1           1   PVID Egress Untagged 20
* Change the PVID of the Port using the PVID flag
$ bridge vlan add vid 20 dev sw0p1 pvid 
$ bridge vlan show dev sw1p5 
port         vlan ids 
sw1p5          1 Egress Untagged 20 PVID
## VLAN Unaware Configurations
Multiple VLAN-unaware bridges can be created. This can be used, for example, to bridge two VLAN devices with different VIDs.
$ ip link add link sw0p1 name sw0p1.10 type vlan id 10 
$ ip link add link sw0p2 name sw0p2.20 type vlan id 20
$ ip link add name br1 type bridge
$ ip link set dev sw0p1.10 master br1 
$ ip link set dev sw0p2.20 master br1
## Bridge Port Configurations
 Learning – Controls whether a given port will learn MAC addresses from received traffic or not. 
 If learning if off, the bridge will end up flooding any traffic for which it has no FDB entry. By default this flag is on.
 Flooding – controls whether a given port floods unicast traffic for which there is no FDB entry. By default, this flag is on.
       $ bridge link set dev sw1p5 learning off flood off