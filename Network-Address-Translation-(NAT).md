To configure stateless Network Address Translation (NAT), use the `tc` iproute2 tool, the `tc-nat` action. The action is used in combination with the `flower` filter rule and ingress qdisc to do static NAT entry offloading.
The command format looks like the following: 
tc … flower ... action nat { ingress | egress } <OLD> <NEW> 
where, 
<OLD>   is the IP address which should be translated. 
<NEW>         is the IP address which IP should be translated into. 
ingress         translate destination addresses, i.e. perform DNAT. 
egress         translate source addresses, i.e. perform SNAT. 

To make NAT one-to-one mapping, you must configure one rule for private-to-public direction, and another rule for public-to-private direction. For more information on `tc-nat` action, see the official man page at [[https://man7.org/linux/man-pages/man8/tc-nat.8.html]]. 



 


Basic example 



 


Create static one-to-one mapping from private (sw1p2) to public (sw1p1) subnet for all TCP traffic.  The IP 192.168.0.0/24 is the private subnet. The setup is described below: 


Text Box 
