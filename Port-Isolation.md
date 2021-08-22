Port isolation (also called ‘protected ports’ or ‘pvlan edge’) enables you to control how traffic is forwarded between ports that are connected at the same bridge domain. Traffic between isolated ports is prevented regardless of their VLAN membership. However, isolated ports can still communicate with non-isolated ports such as uplink to access WAN. 
# Linux Support
This capability is supported by Linux kernels since 4.18. The support is provides on the port level (that is, not per port and vlan). Isolation is always applied on netdev switch ports.

Linux Command:
`bridge link set dev DEV [isolated {on|off}]`
This setting is available through (rt) netlink, and it has no equivalent in ioctl/brctl



