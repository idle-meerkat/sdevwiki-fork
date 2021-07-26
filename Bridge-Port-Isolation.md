Isolated ports cannot communicate between each other, but they can still communicate with unisolated ports.

To isolate a bridge port, enter the following command: 
```
$ bridge link set dev sw1p1 isolated on 
```
Isolating a single bridge port has no effect until at least one other port of the same bridge is also isolated: 
```
# bridge link set dev sw1p2 isolated on 
```
Now the bridge ports sw1p1 and sw1p2 are unable to send or receive any packet to or from each other. However, these interfaces can still communicate normally with any other unisolated port of the bridge. 

## Limitations: 
* If there are multiple VLANs on an isolated bridge port, all are affected.
* The bridge's self interface (bridge0 in this example) can not isolate itself (even with the correct syntax: appending the self keyword when the device is the bridge name itself).
* All newer advanced features are available through the (rt)netlink ??with the ip link and bridge commands have no equivalent through the older ioctl API, so brctl can't be used for this. 