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

**NOTE**: The agent does not on a physical port if it belongs to a LAG.  

# LLDPd
This section describes how to configure the LLDP protocol using an LLDPd agent.

## Default Behavior
By default, the LLDP agent enables LLDP protocol on all available physical interfaces. To limit the agent to listen only on switchdev interfaces, you need to provide a configuration file.

## Agent Configuration

* To limit the LLDP agent on specific port(s):  
`lldpcli configure system interface pattern sw*`  

* To enable/disable LLDP on a specific interface:  
`lldpcli configure ports sw1p1 lldp status rx-and-tx`  

*To query LLDP statistics on port:  
`lldpcli show statistics ports sw1p1`  

* To query TLV information from switch about connected neighbor:  
`lldpcli show neighbors`   
**NOTE**: Detailed instructions how to configure the agent can be found on lldpd manual page at https://vincentbernat.github.io/lldpd/usage.html.  

## Configure Mandatory/Optional TLV  
The mandatory and optional TLV information is enabled automatically by the agent and cannot be disabled or enabled separately.  

## IEEE 802.1/ IEEE 802.3 Organizationally Specific TLV
This information is not supported by the agent. However, the this TLV information can be provided statically via custom TLV configuration manually. For example:  

* IEEE 802.1   
`lldpcli configure lldp custom-tlv oui 00,80,c2 subtype 1 oui-info 56,78,9,0,90,78,54`  
* IEEE 802.3  
`lldpcli configure lldp custom-tlv oui 00,12,0f subtype 1 oui-info 56,78,9,0,90,78,54

## TLV Information Values  
The following table describes TLV values that LLDPd will advertise in case of default configuration.  

|TLV name | Value description |
|---- | ---|
|Mandatory TLV information|
|End of LLDPDU | End of TLV information|
Chassis ID TLV | Mac address of management interface
Port ID TLV | Switchdev port MAC address
Time to Live TLV | TTL
Optional TLVs information
Port Description TLV | Switchdev port name
System Name TLV	| Hostname (hostname tool)
System Description TLV | OS system description in format: <OS release name> <kernel name> <kernel release> <kernel version> <machine hardware name>. Can be obtained via Linux tools like: </br> sed -ne 's/PRETTY_NAME="\(.*\)"/\1/p' /etc/os-release; uname -s; uname -r; uname -v; uname -m
System Capabilities TLV	 | System capabilities, like bridge, router
Management Address TLV | IP of management interface
IEEE 802.3 Organizationally Specific TLV information (not supported by LLDPd automatically, TLV value can be configured via custom TLV configuration)

NOTE: Advertising IEEE 802.1/ IEEE 802.3 Organizationally Specific TLVs is not supported by the agent. This type of advertising can only be done via static custom TLVs.

