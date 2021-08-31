Dynamic NAT maps private IP addresses to public addresses.

## <a id="dynamic"></a>Dynamic (Stateful) NAT Configuration
The configuration of stateful NAT is done by using `tc` iproute2 tool and the `tc-ct` action. The action is used in combination with `flower` filter rule and ingress qdisc to do the dynamic NAT entry offloading. The user guide for stateful NAT is described in tc-flower and tc-ct man pages. 

**NOTE:** The stateful NAT feature requires Linux kernel 5.8.9 and above. 

### Basic Configuration 
This example covers the basic use case (many-to-one, with one private Switchdev port) for stateful NAT configuration, using a TC connection tracking subsystem. 

The following figure shows dynamic NAT setup: 

![Dynamic NAT Setup](images/dynamic_nat_setup.png)

**NOTE:** This scenario also covers the case of only one private host. 
Configuration of private host connected to the switch device (in this case on one private): 
```
# private host: IP/default gateway 
ip addr add dev eth0 192.168.0.2/24 
ip route add default via 192.168.0.1 
```
Configuration of public host connected to the switch device: 

```
# public host: IP 
ip addr add dev eth0 10.1.1.2/24 
```
IP configuration on the switch: 

```
# switch: private/public interface IP 
ip addr add dev sw1p2 192.168.0.1/24 
ip addr add dev sw1p1 91.245.77.1/24 
```
Add default gateway on the switch to route packets via public interface. 
```
# add default gateway via public interface 
ip route add default via 91.245.77.1 
```
Configure connection tracking on a private port: 
```
tc qdisc add dev sw1p2 clsact 
tc filter add dev sw1p2 ingress proto ip pref 2 flower ct_state -trk \ 
  action ct 
```
Configure NAT connection tracking on public port: 
```
tc qdisc add dev sw1p1 clsact 
tc filter add dev sw1p1 egress prio 10 proto ip flower \ 
  action ct commit nat src addr 91.245.77.1 pipe action pass 
tc filter add dev sw1p1 ingress prio 5 proto ip flower \ 
  action ct nat pipe action pass 
```
**NOTES:** 
* Since the NAT configuration is done using regular ACL rules, it takes resources from regular ACL memory. 
* Egress CT flower filter on a public port should be the same as CT flower filter on a private port. If a CT rule exists on multiple private ports, those flower matches should be reflected on the egress public port. 

### Private to Private Flow 

To skip NAT for packets that are destined for private subnet or hosts, you need to install additional hardware offloaded rules (`skip_sw` flower option) on private ports. For example, a rule that matches a private subnet with higher priority should be installed with the action “do nothing” on each of the private ports. 
```
tc filter add dev sw1p2 protocol ip ingress \ 
   flower skip_sw ip_proto tcp dst_ip 192.168.0.1/24 action pass 
```
**NOTE:** The last rule added has the higher priority, so there is no need to define the priority in the rule (see the ACL document for more information).  
