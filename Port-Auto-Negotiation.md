
Port auto-negotiation can be enabled/disabled using following command:  
`ethtool -s sw1p1 autoneg [on | off]`  
Linux defines following interface modes which user can advertise to the remote side:
| Mask | Mode |
|---- |---- |
|0x001| 10baseT Half |
0x001	|	10baseT Half	|
0x002	|	10baseT Full	|
0x004	|	100baseT Half	|
0x008	|	100baseT Full	|
0x80000000000000000	|	100baseT1 Full	|
0x010	|	1000baseT Half	|
0x020	|	1000baseT Full	|
0x100000000000000000	|	1000baseT1 Full	|
0x20000	|	1000baseKX Full	|
0x20000000000	|	1000baseX Full	|
0x800000000000	|	2500baseT Full	|
0x8000	|	2500baseX Full	|
0x1000000000000	|	5000baseT Full	|
0x1000	|	10000baseT Full	|
0x40000	|	10000baseKX4 Full	|
0x80000	|	10000baseKR Full	|
0x100000	|	10000baseR_FEC	|
0x40000000000	|	10000baseCR  Full	|
0x80000000000	|	10000baseSR  Full	|
0x100000000000	|	10000baseLR  Full	|
0x200000000000	|	10000baseLRM Full	|
0x400000000000	|	10000baseER  Full	|
0x200000	|	20000baseMLD2 Full	|
0x400000	|	20000baseKR2 Full	|
0x80000000	|	25000baseCR Full	|
0x100000000	|	25000baseKR Full	|
0x200000000	|	25000baseSR Full	|
0x800000	|	40000baseKR4 Full	|
0x1000000	|	40000baseCR4 Full	|
0x2000000	|	40000baseSR4 Full	|
0x4000000	|	40000baseLR4 Full	|
0x400000000	|	50000baseCR2 Full	|
0x800000000	|	50000baseKR2 Full	|
0x10000000000	|	50000baseSR2 Full	|
0x10000000000000	|	50000baseKR Full	|
0x20000000000000	|	50000baseSR Full	|
0x40000000000000	|	50000baseCR Full	|
0x80000000000000	|	50000baseLR_ER_FR Full	|
0x100000000000000	|	50000baseDR Full	|
0x8000000	|	56000baseKR4 Full	|
0x10000000	|	56000baseCR4 Full	|
0x20000000	|	56000baseSR4 Full	|
0x40000000	|	56000baseLR4 Full	|
0x1000000000	|	100000baseKR4 Full	|
0x2000000000	|	100000baseSR4 Full	|
0x4000000000	|	100000baseCR4 Full	|
0x8000000000	|	100000baseLR4_ER4 Full	|
0x200000000000000	|	100000baseKR2 Full	|
0x400000000000000	|	100000baseSR2 Full	|
0x800000000000000	|	100000baseCR2 Full	|
0x1000000000000000	|	100000baseLR2_ER2_FR2 Full	|
0x2000000000000000	|	100000baseDR2 Full	|
0x4000000000000000	|	200000baseKR4 Full	|
0x8000000000000000	|	200000baseSR4 Full	|
0x10000000000000000	|	200000baseLR4_ER4_FR4 Full	|
0x20000000000000000	|	200000baseDR4 Full	|
0x40000000000000000	|	200000baseCR4 Full	|

In order to advertise some modes, you need to prepare the mask using “OR” operation for target modes and pass it to the `ethtool` command. For example:  
`ethtool -s sw1p1 advertise 0x80000`  

If auto negotiation is enabled, the prestera driver should verify inbound advertising modes and print an error message whether some unsupported mode has occurred. If auto-negotiation is disabled, the driver just ignores the advertising mode setting.    

The configuration of auto-negotiation mechanism is different for low and high speed ports:   
* For low-speed ports, the auto-negotiation parameters should be taken (or applied) from (to) PHY directly.  The user has a full control of auto-negotiation configuration on the port.  
* For high-speed SFP ports, only 802.3ap auto-negotiation is supported. The user can enable/disable 802.3ap auto-negotiation on a port and configure the advertised modes. The 802.3ap mode is supported only for TP port type.  

**NOTE**: Auto-negotiation on Copper SFP with RJ-45 module using a Prestera driver is not supported.  

