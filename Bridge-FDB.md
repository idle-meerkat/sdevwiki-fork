Linux bridge is a way to connect two Ethernet segments together in a protocol independent way. Packets are forwarded based on the Ethernet address, rather than the IP address (like a router). Since forwarding is done at Layer 2, all protocols can go transparently through a bridge.  

The Linux bridge code implements a subset of the ANSI/IEEE 802.1d standard. [1]. The original Linux bridging was first done in Linux 2.2, then rewritten by Lennert Buytenhek. The code for bridging has been integrated into 2.4 and 2.6 kernel series.  
## Linux Commands
To create a bridge run:  
`$ ip link add name br0 type bridge`  
Or:  
`$ ip link add name br0 type bridge`  



## Bridge Port Configuration

 
