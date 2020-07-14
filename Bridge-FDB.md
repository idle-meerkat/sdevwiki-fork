Linux bridge is a way to connect two Ethernet segments together in a protocol independent way. Packets are forwarded based on the Ethernet address, rather than the IP address (like a router). Since forwarding is done at Layer 2, all protocols can go transparently through a bridge.  

The Linux bridge code implements a subset of the ANSI/IEEE 802.1d standard. [1]. The original Linux bridging was first done in Linux 2.2, then rewritten by Lennert Buytenhek. The code for bridging has been integrated into 2.4 and 2.6 kernel series.  
## Linux Commands
A bridge is created by running:  
`$ ip link add name br0 type bridge`  
Or:  
`$ ip link add name br0 type bridge`  

A Linux bridge forwards packets based on FDB data. To display bridge FDB data:  
`$ bridge fdb`  
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

## Static and Sticky FDB entries
Recently new entry type “sticky” was introduced in Linux bridge. In Linux, static FDB can be roamed to a different port via learning. Sticky FDB entries cannot be moved. 
Because of the current infrastructure of switchdev FDB notification chain there is no indication which type of entry was added. Thus, all entries are treated as static.  Once first upstream patch is published a request with changes to switchdev fdb notification the chain will add support for the entry type.

* To add a static FDB entry:
`3.1.1	Static and Sticky FDB entries

Recently new entry type “sticky” was introduced in Linux bridge. In Linux static FDB can be roamed to a different port via learning. Sticky FDB entries cannot be moved. 
Because of the current infrastructure of switchdev fdb notification chain there is no indication which type of entry was added. Thus, all entries are treated as static.  Once first upstream patch will be published a request with changes to switchdev fdb notification chin ill add support for entry type.

* To add a static FDB entry:  
`$ bridge fdb add ADDR dev DEV master static [vlan VID]`  
* To delete the static FDB entry:  
`$ bridge fdb add ADDR dev DEV master static [vlan VID]`  

## Bridge Port Configuration
The following bridge port attributes can be configured:
* learning
* flooding

To set learning and flooding attributes:
`$ bridge link set dev DEV learning {on/off} flood {on/off}`  
 
