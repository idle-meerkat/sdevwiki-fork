
## ACL supported actions [3]
* drop
* trap
* pass
## ACL supported keys [3]
* Ingress interfaces (only switchdev interface)
* Protocol (ethertype)
* src_mac
* dst_mac
* src_ip (IPv4)
* dst_ip (IPv4)
* ip_proto (Tcp/Udp)
* src_port
* dst_port
* vlan_id
## Supported TC Flower Rules and Actions
The following list of ACL rules (TC flower matches) are supported:
* indev DEV-NAME (useful when using qdisc blocks, which is described in next major section)
* protocol PROTO (tc filter option, not flower filter type)
* dst_mac MASKED-LLADDR
* src_mac MASKED-LLADDR
* ip_proto [tcp | udp] (protocol ip)
* dst_ip PREFIX (protocol ip)
* src_ip PREFIX (protocol ip)
* dst_port {NUMBER | MIN_VALUE-MAX_VALUE} (ip_proto tcp|udp)
* src_port {NUMBER | MIN_VALUE-MAX_VALUE} (ip_proto tcp|udp)
* vlan_id
The following ACL actions (TC flower actions) are supported:
* drop (shot word also can be used to specify drop action)
* pass (ok word also can be used to specify pass action)
* trap
