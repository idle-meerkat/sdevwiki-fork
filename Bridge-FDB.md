Linux bridge is a way to connect two Ethernet segments together in a protocol independent way. Packets are forwarded based on the Ethernet address, rather than the IP address (like a router). Since forwarding is done at Layer 2, all protocols can go transparently through a bridge.  

The Linux bridge code implements a subset of the ANSI/IEEE 802.1d standard. [1]. The original Linux bridging was first done in Linux 2.2, then rewritten by Lennert Buytenhek. The code for bridging has been integrated into 2.4 and 2.6 kernel series.  
## Linux Commands
To create a bridge run:  
`$ ip link add name br0 type bridge`  
Or:  
`$ ip link add name br0 type bridge`  


## Static and Sticky FDB Entries
Recently, a new entry type “sticky” was introduced in Linux bridge. In Linux, a static FDB can be roamed to a different port via learning. Sticky FDB entries cannot be moved. 
Because of the current infrastructure of the switchdev FDB notification chain, there is no indication which type of entry was added. Thus, all entries are treated as static.  Once the first upstream patch is published, a request with changes to switchdev fdb notification the chain will add support for the entry type.

* To add a static FDB entry:
`3.1.1	Static and Sticky FDB entries

Recently new entry type “sticky” was introduced in Linux bridge. In Linux static FDB can be roamed to a different port via learning. Sticky FDB entries cannot be moved. 
Because of the current infrastructure of switchdev fdb notification chain there is no indication which type of entry was added. Thus, all entries are treated as static.  
Once first upstream patch is published, a request with changes to switchdev fdb notification chain will add support for the entry type. 

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
 
