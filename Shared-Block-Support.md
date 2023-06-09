
TC shared blocks is a feature that enables binding several ports to the same list of filter rules.  

Consider a case when you have 2 netdevices, you create ingress qdisc on both. Now if you want to add an identical set of filter rules to both, you need to add them twice. One port for each qdisc. That is of course doable, but when the filters are offloaded to the hardware with a limited number of entries, the duplication may become a scale issue. Sharing of blocks aims to resolve that.  

To share blocks, you need to request the share from the kernel at the qdisc creation stage:  
`$ sudo tc qdisc add dev sw1p2 ingress_block 1 clsact`  
`$ sudo tc qdisc add dev sw1p3 ingress_block 1 clsact`  

These two commands add ingress qdiscs to both netdevices. Note the ingress_block option that indicates that both qdiscs should share the same block identified by index 1. Note that the `ingress_block` option identifies the block index that is shared between the corresponding qdiscs.
If you list the existing qdiscs, you see the block sharing information in the output:  
`$ tc qdisc`  

Example output:  
`qdisc clsact ffff: dev sw1p2 parent ffff:fff1 ingress_block 1`  
`qdisc clsact ffff: dev sw1p3 parent ffff:fff1 ingress_block 1`  

The number of qdiscs that can share the same block is not limited.  
Once the qdisc block is shared, you can no longer manipulate the filters using the qdisc handle. Instead, use the block index as a handle:  
`$ sudo tc filter add block 1 protocol ip pref 20 flower dst_ip 192.168.0.0/16 action drop`  

Or set rules for a specific interface in the block:  
`$ sudo tc filter add block 1 root flower skip_sw indev sw1p1 action pass`  

In addition to `clsact` qdisc, block sharing is also supported for `ingress` qdisc:  
`$ sudo tc qdisc add dev sw1p5 ingress_block 2 ingress`  
