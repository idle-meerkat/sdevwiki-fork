Static NAT provides a permanent mapping between a private IP address and a single public address.

## <a id="static"></a>Static (Stateless) NAT Configuration

To configure stateless Network Address Translation (NAT), use the `tc` iproute2 tool, with the `tc-nat` action. The action is used in combination with the `flower` filter rule and ingress qdisc to do static NAT entry offloading.
This is the command format: 
```
tc … flower ... action nat { ingress | egress } <OLD> <NEW> 
```
where, 
* `<OLD>`   is the IP address which should be translated. 
* `<NEW>`         is the IP address which IP should be translated into. 
* `ingress`         translate destination addresses, i.e. perform DNAT. 
* `egress`         translate source addresses, i.e. perform SNAT. 

To implement NAT one-to-one mapping, you must configure one rule for private-to-public direction, and another rule for public-to-private direction. For more information on `tc-nat` action, see the official man page at [[https://man7.org/linux/man-pages/man8/tc-nat.8.html]]. 

### Basic Example 
Create a static one-to-one mapping from a private (sw1p2) to a public (sw1p1) subnet for all TCP traffic.  The IP 192.168.0.0/24 is the private subnet. 
The following figure shows static NAT setup: 

![Static NAT Setup](images/static_nat_setup.png)

First, you must define IP addresses on the public/private host interfaces connected to the switch: 
```
# private host IP / default gateway 
ip addr add dev eth0 192.168.0.2/24 
ip route add default via 192.168.0.1 

# public host IP address (WAN port) 
ip addr add dev eth0 10.1.1.2/24 
```
Next, configure IP addresses on the public/private switch ports: 

```
ip addr add dev sw1p2 192.168.0.1/24 
ip addr add dev sw1p1 91.245.77.1/24 
```
Add a default gateway on the switch, to route packets via a public interface. 

```
# add default gateway via public interface 
ip route add default via 91.245.77.1 
```
Configure ACL rules on the switch to do NAT offloading: 

```
# public ports  
tc qdisc add dev sw1p1 clsact 
tc filter add dev sw1p1 protocol ip egress \ 
   flower ip_proto tcp src_ip 192.168.0.2 \ 
   action nat egress 192.168.0.2 91.245.77.1 

tc filter add dev sw1p1 protocol ip ingress \ 
   flower ip_proto tcp dst_ip 91.245.77.1 \ 
   action nat ingress 91.245.77.1 192.168.0.2 

# private ports, create only HW rule 
tc qdisc add dev sw1p2 clsact 
tc filter add dev sw1p2 protocol ip ingress \ 
   flower skip_sw ip_proto tcp src_ip 192.168.0.2 \ 
   action nat egress 192.168.0.2 91.245.77.1 
```
**NOTE:** The configuration is slightly different from the usual Linux software configuration of stateless TC NAT, where NAT rules are applied on a public port only (such as, WAN port). For instance: 
```
# public network 
tc qdisc add dev eth0 ingress 
tc filter add dev eth0 ingress protocol ip matchall \ 
   action nat ingress 172.31.19.2/32 10.0.2.2/32 
 
# private network 
tc qdisc add dev eth0 root handle 1: prio 
tc filter add dev eth0 parent 1: protocol ip matchall \ 
   action nat egress 10.0.2.2/32 172.31.19.2/32 
```
**NOTE:** Egress `qdisc` (egress ACL) is not supported by the Switchdev driver. Therefore, the public/private Switchdev TC NAT configuration is done using ingress `qdisc` only. This requires adding hardware rules on the private switch port.

### Private to Private Flow
To skip NAT for packets that are destined for private subnet or hosts, you need to install additional rules on private ports. For example, a rule that matches a private subnet with a higher priority should be installed with the action “do nothing”. 

```
tc filter add dev sw1p2 protocol ip ingress \ 
   flower skip_sw ip_proto tcp dst_ip 192.168.0.1/24 action pass 
```
**NOTE:** The last rule added has the higher priority, so there is no need to define the priority in the rule (see [Supported Actions, Keys and Rules](acl#supported-actions-keys-and-rules) for more information).