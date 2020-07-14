## STP (802.1D) Configuration
To enable STP on a bridge, run the following command:  
`$ ip link set dev br0 type bridge stp_state 1`  

By default, STP is disabled.  
To disable STP on a bridge, set `stp_state` to “0”.  
To show the current `stp_state` value, run the following command:    
`$ ip -d link show dev br0 | grep stp_state`    

In addition, to show extended STP configuration on the bridge (bridge stp state, port stp state, …), you can use `brctl` utility.  
`$ brctl showstp br0`  

You can use `brctl` to configure the following spanning tree protocol parameters. For more information on these parameters, see the IEEE 802.1d specification.  See [brctl man](https://linux.die.net/man/8/brctl) for details on bridge STP configurations:   
* Bridge priority
* Path priority and cost
* Forwarding delay
* Hello time
* Max age  
 
## RSTP (802.1w) Configuration  
RSTP support requires user-level daemon mstpd, running in RSTP mode. The mstpd daemon is an open source project (https://github.com/mstpd/mstpd).  
The `mstpctl` utility, provided by the mstpd service, configures STP.  

After the bridge is created and STP is enabled (described above), you can switch the bridge mode from STP to RSTP using the `mstpctl` application. See [mstpctl man](https://github.com/mstpd/mstpd/blob/master/utils/mstpctl.8) for more information. 

* To switch the bridge mode from STP to RSTP:  
`$ mstpctl setforcevers br0 rstp`  
* To show information about the bridge:  
`$ mstpctl showbridge`  
* To show port information:  
`$ mstpctl showport br0 sw1p5`  
  
