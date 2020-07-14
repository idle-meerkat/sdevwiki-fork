In the most cases, the configuration of a port’s parameters can be done using `ip`, `ifconfig`, `ifup2`, `ifdown2`, `ifquery` or `ethtool` linux commands. The detailed command description and usage examples are provided in this section.  
# Port Identification   
In order to specify the corresponding switch port name in Linux, use the following udev rule. It sets the interface name in the kernel driver, based on kernel port properties, such as switch ID and port ID.  
`SUBSYSTEM=="net", ACTION=="add", ATTR{phys_switch_id}=="00", ATTR{phys_port_name}!="", NAME="sw0p$attr{phys_port_name}"`  

# Port State  
The port administrative status can be set using one of the following commands:
```
ip link set sw1p1 [up | down]
ifconfig sw1p1 [up | down]
ifup2 sw1p1
ifdown2 sw1p1
```
# Port MTU  
The port MTU can be set using one of the following commands:  
`ip link set dev sw1p1 mtu 1500`  
**NOTE** The driver supports maximum 3 different MTU settings.

# Port MAC Address  
The port MAC address can be set using one of the following commands:  
ip link set dev sw1p1 address 00:00:00:00:00:02  

# Port Speed
The port speed can be set using following commands (The value of speed should be in Mb/s.):  
`ethtool -s sw1p1 speed 1000`   
**NOTE:** The speed can only be set if autoneg is disabled. If you pass speed parameter when autoneg is enabled, it will be ignored.  

# Port Duplex Mode
The port duplex mode can be set using following commands:
`ethtool -s sw1p1 duplex [half|full]`  
**NOTE**: duplex can only be set if autoneg is disabled. If you pass duplex parameter when autoneg is enabled, it is ignored.  

# Port Statistics
There are two types of port statistic:
* Software statistics account for packets trapped to the CPU or packets sent from the CPU.  
* Hardware statistics account for all packets going through the port, including those not trapped to or originating from the CPU.  

## Software Statistics  
The `ifstat` tool can be used to query the port's software statistics.  

## Hardware Statistics  
The following command can be used to review port hardware statistics:
* ip –s link show sw1pX
* Ifconfig sw1pX
* Ethtool –S sw1pX  

The `ip –s link show` and `ifconfig` commands display basic hardware statistics in rtnl_link_stats64 linux format.  
```
7: sw1p1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT group default qlen 1000
    link/ether 00:00:00:00:00:01 brd ff:ff:ff:ff:ff:ff
    RX: bytes  packets  errors  dropped overrun mcast   
    3150       41       0       0       0       41      
    TX: bytes  packets  errors  dropped carrier collsns 
    3056       40       0       0       0       0
```
The “ethtool” command displays detailed port hardware counters. 
```
amzgo-host# ethtool -S sw1p1
NIC statistics:
     good_octets_received: 3150
     bad_octets_received: 0
     mac_trans_error: 0
     broadcast_frames_received: 0
     multicast_frames_received: 41
     frames_64_octets: 0
     frames_65_to_127_octets: 81
     frames_128_to_255_octets: 0
     frames_256_to_511_octets: 0
     frames_512_to_1023_octets: 0
     frames_1024_to_max_octets: 0
     excessive_collision: 0
     multicast_frames_sent: 40
     broadcast_frames_sent: 0
     fc_sent: 0
     fc_received: 0
     buffer_overrun: 0
     undersize: 0
     fragments: 0
     oversize: 0
     jabber: 0
     rx_error_frame_received: 0
     bad_crc: 0
     collisions: 0
     late_collision: 0
     unicast_frames_received: 0
     unicast_frames_sent: 0
     sent_multiple: 0
     sent_deferred: 0
     frames_1024_to_1518_octets: 0
     frames_1519_to_max_octets: 0
     good_octets_sent: 3056
```

