In most cases, the configuration of a port’s parameters can be done using `ip`, `ifconfig`, `ifup2`, `ifdown2`, `ifquery` or `ethtool` linux commands. The detailed command description and usage examples are provided in this section.  
## Port Identification   
In order to specify the corresponding switch port name in Linux, use the following udev rule. It sets the interface name in the kernel driver, based on kernel port properties, such as switch ID and port ID.  
`SUBSYSTEM=="net", ACTION=="add", ATTR{phys_switch_id}=="00", ATTR{phys_port_name}!="", NAME="sw0p$attr{phys_port_name}"`  

## Port State  
The port administrative status can be set using one of the following commands:

`ip link set sw1p1 [up | down]`  
`ifconfig sw1p1 [up | down]`  
`ifup2 sw1p1`  
`ifdown2 sw1p1`  
## Port Maximum Transmission Unit (MTU)  
The port Maximum Transmission Unit (MTU) can be set using the following command:  
`ip link set dev sw1p1 mtu 1500`  

**NOTE** The driver supports up to three different MTU settings.

## Port MAC Address  
The port MAC address can be set using the following command:  
`ip link set dev sw1p1 address 00:00:00:00:00:02`  

## Port Speed
The port speed can be set using the following command (the speed value is in Mb/s.):  
`ethtool -s sw1p1 speed 1000`   

**NOTE:** The speed can only be set if auto-negotiation is disabled. If you pass a speed parameter when auto-neogtiation is enabled, it is ignored.  

## Port Duplex Mode
The port duplex mode can be set using the following command:
`ethtool -s sw1p1 duplex [half|full]`  

**NOTE**: duplex can only be set if auto-neogtiation is disabled. If you pass duplex parameter when auto-neogtiation is enabled, it is ignored.  

## NOTES
* Only odd numbers are supported for Maximum Receive Unit (MRU) configuration on the port. This is in keeping with the requirement that MTU is changed together with MTU.
* Before setting the speed, make sure to configure the port media type.
* Only the least significant byte of a MAC address can be configured on the port. The rest of the MAC address is a common base for all ports. 
* The ethtool `-r` command to restart the auto negotiation is supported only for 1G low speed copper ports.
* FEC is supported only on SFP ports of 10G speed and higher.

The following ethtool commands are not supported:
* `–m` command to read SFP module information.
* `–p` command to test LEDs on port.



