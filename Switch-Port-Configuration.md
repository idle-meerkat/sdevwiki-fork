In the most cases, the configuration of a port’s parameters can be done using `ip`, `ifconfig`, `ifup2`, `ifdown2`, `ifquery` or `ethtool` linux commands. The detailed command description and usage examples are provided in this section.  
# Port Identification   
In order to specify the corresponding switch port name in Linux, use the following udev rule. It sets the interface name in the kernel driver, based on kernel port properties, such as switch ID and port ID.  
`SUBSYSTEM=="net", ACTION=="add", ATTR{phys_switch_id}=="00", ATTR{phys_port_name}!="", NAME="sw0p$attr{phys_port_name}"`  

# Port Information
To view the port status and configuration, use the `ifconfig`, `ip link show` and `ethtool` commands.  

## ip link show sw1pX
The `ifconfig` and `ip link show` commands provide information about administrative and operational status, MTU and MAC address. `ifconfig` also provides some basic port counters.  

```
amzgo-host# ip link show sw1p1
7: sw1p1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT group default qlen 1000
    link/ether 00:00:00:00:00:01 brd ff:ff:ff:ff:ff:ff
```  

## ethtool sw1pX
The `ethtool` command provides information about current speed and duplex, autoneg status, supported and advertised link modes, connector type and statistic.  
The output is shown in the example below:
* **Low speed port**  
```
amzgo-host# ethtool sw1p1
Settings for sw1p1:
# Supported media type, link modes and FEC taken from PHY/MPD
        Supported ports: [ TP ] 
        Supported link modes:   10baseT/Half 10baseT/Full 
                                100baseT/Half 100baseT/Full 
                                1000baseT/Full 
        Supported pause frame use: No
        Supports auto-negotiation: Yes
        Supported FEC modes: None
# Advertised link modes and FEC, section should display actual modes if the port is administratively UP and autoneg is ON, ‘not reported’ otherwise
        Advertised link modes:  10baseT/Half 10baseT/Full 
                                100baseT/Half 100baseT/Full 
                                1000baseT/Full 
        Advertised pause frame use: No
# section always displays “Yes” as PHY is advertising at least one mode
        Advertised auto-negotiation: Yes
        Advertised FEC modes: None
# link partner capability, section should be displayed with actual capability if the link is established, hidden otherwise
        Link partner advertised link modes:  10baseT/Half 10baseT/Full 
                                             100baseT/Half 100baseT/Full 
                                             1000baseT/Full 
        Link partner advertised pause frame use: No
        Link partner advertised auto-negotiation: Yes
        Link partner advertised FEC modes: Not reported
# section should display actual speed and duplex if the link is established, “Unknown” otherwise
        Speed: 1000Mb/s
        Duplex: Full
# section should display the actual port media type
        Port: Twisted Pair
# PHYAD and Transceiver sections are not supported, default values from Linux are displayed
        PHYAD: 0
        Transceiver: internal
# section should display the actual status of auto negotiation 
        Auto-negotiation: on
# section should display the actual MDI/MDI-X mode
        MDI-X: off
# section should display the actual port operational status
        Link detected: yes
```
* **High speed port**  
```
# Supported media type
        Supported ports: [ TP FIBRE ]  
# Section is dynamically changing and displays the supported link modes for current port media type
        Supported link modes:   1000baseT/Full 
                                1000baseKX/Full 
                                10000baseKR/Full 
        Supported pause frame use: No
# Supported auto negotiation should be “Yes” only for TP port, “No” for Fiber 
        Supports auto-negotiation: Yes
# Section is dynamically changing and displays the supported FEC modes for current port media type
        Supported FEC modes: None BaseR
# Section should display actual advertising link modes for TP port type, “Not reported” for Fiber
        Advertised link modes:  Not reported
        Advertised pause frame use: No
        Advertised auto-negotiation: No
        Advertised FEC modes: Not reported
# link partner capability section should not be displayed for SFP ports
# Section should display actual speed and duplex if the link is established, “Unknown” otherwise
        Speed: 10000Mb/s
        Duplex: Full
# section should display the actual port media type
        Port: Twisted Pair
# PHYAD and Transceiver sections are not supported, default values from Linux are displayed
        PHYAD: 0
        Transceiver: internal
# section should display the actual status of auto negotiation for TP port type, always “off” for Fiber
        Auto-negotiation: off
# section is always displayed as Unknown, not supported for SFP ports
        MDI-X: Unknown
# section should display the actual port operational status
     Link detected: yes
```
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

# Port speed
The port speed can be set using following commands (The value of speed should be in Mb/s.):  
`ethtool -s sw1p1 speed 1000`   
**NOTE:** The speed can only be set if autoneg is disabled. If you pass speed parameter when autoneg is enabled, it will be ignored.  

# Port Auto-Negotiation
The port autoneg can be enabled/disabled using following command:  
`ethtool -s sw1p1 autoneg [on | off]`  
Linux defines following interface modes which user can advertise to the remote side:
| Mask | Mode |
|---- |---- |
|0x001| 10baseT Half |
