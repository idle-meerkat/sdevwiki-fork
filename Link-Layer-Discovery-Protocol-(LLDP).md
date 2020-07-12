This section describes how to configure Link Layer Discovery Protocol (LLDP) on the device.  
# Open-LLDP
This section describes how to configure LLDP protocol using an Open-LLDP agent.  
## Default Behavior
By default, the LLDP agent does not enable LLDP protocol on switchdev interfaces on the system. This configuration should be done manually (e.g. enable protocol, configure TLVs etc). After restarting the agent, the configuration is preserved, the LLDP agent configuration is stored automatically in /var/lib/lldpad/lldpad.conf file.  

## Agent Configuration
* To enable LLDP (receive & transmit TLV information) on a specific port, use the following command:  
`ip link set dev <SW-PORT> up`  
`lldptool -L -i <SW-PORT> adminStatus=rxtx`  

* To get the status of a TLV (enabled/disable):  
`lldptool -t -i <SW-PORT> -V <TLV-NAME> -c enableTx`  

* To enable transmitting a specific TLV value:  
`lldptool -T -I <SW-PORT> -V <TLV-NAME> enableTx=yes`  

* To get local the value of all enabled TLVs:  
`lldptool -t -i <SW-PORT>`  

* To query LLDP statistics on port:  
`lldptool -S -i <SW-PORT>`  

* To query TLV information from the switch about a connected neighbor:
`lldptool -t -n -i <SW-PORT>`  

* To get a specific TLV value:  
`lldptool -t -n -V portID -i <SW-PORT>`   

## Configure Mandatory TLV  
Once the LLDP protocol is enabled on a switchdev port, the mandatory TLV information is enabled automatically. The mandatory TLV cannot be disabled by user.  

## Configure Optional TLV  
The following table below describes the TLV names (including mandatory) that can be used by the agent to configure required TLV values:
TLV Name	|	TLV Descripton
--- | ---
chassisID	|	Chassis ID TLV
portID	|	Port ID TLV
TTL	|	Time to Live TLV
portDesc	|	Port Description TLV
sysName	|	System Name TLV 
sysDesc	|	System Description TLV
sysCap	|	System Capabilities TLV
mngAddr	|	Management Address TLV
macPhyCfg	|	MAC/PHY Configuration Status TLV
linkAgg	|	Link Aggregation TLV
MTU	|	Maximum Frame Size TLV
