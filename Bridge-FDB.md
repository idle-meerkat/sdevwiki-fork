Linux bridge is a way to connect two Ethernet segments together in a protocol independent way. Packets are forwarded based on Ethernet address, rather than IP address (like a router). Since forwarding is done at Layer 2, all protocols can go transparently through a bridge.  

The Linux bridge code implements a subset of the ANSI/IEEE 802.1d standard. [1]. The original Linux bridging was first done in Linux 2.2, then rewritten by Lennert Buytenhek. The code for bridging has been integrated into 2.4 and 2.6 kernel series.  
## Linux Commands
A bridge is created by running:  
`$ ip link add name br0 type bridge`  
or  
`$ ip link add name br0 type bridge`  

Linux bridge is forwarding packets based on FDB data. To display bridge FDB:  
`$ bridge fdb  
52:54:00:12:35:01 dev sw1p1 master br0 permanent  
00:02:00:00:02:00 dev sw1p1 master br0 offload  
00:02:00:00:02:00 dev sw1p1 self  
52:54:00:12:35:02 dev sw1p2 master br0 permanent  
00:02:00:00:03:00 dev sw1p2 master br0 offload  
00:02:00:00:03:00 dev sw1p2 self  
33:33:00:00:00:01 dev eth0 self permanent  
01:00:5e:00:00:01 dev eth0 self permanent  
33:33:ff:00:00:00 dev eth0 self permanent  
01:80:c2:00:00:0e dev eth0 self permanent  
33:33:00:00:00:01 dev br0 self permanent  
01:00:5e:00:00:01 dev br0 self permanent  
33:33:ff:12:35:01 dev br0 self permanent`

Entries with `offload` flag are externally learned entries (hardware FDB)

