This section describes how to police static and dynamic trapped packets to to the CPU port. This feature is an extension of ACL rules. See [ACL Configuration](acl-configuration) for more information on how to configure ALC rules.

# Policing of Static Traps

Static packet traps are configured upon switchdev driver initialization. The list of packet traps, their rate limit and TC is defined in “Static trap”?? section above.

You can disable the entire static profile, or select the desired profile during switchdev driver initialization. To select or disable a static trap profile, use the switchdev kernel module parameter, provided for  the `prestera_sw` driver.
```
modprobe prestera_sw trap_policer_profile=<PROFILE>
```
where `<PROFILE>` is the number value:
* 0 – disable
* 1 – enable first profile. In this case, enable profile specified in the table above. Currently, only one profile is supported.

**NOTE**: Applying the changes requires a system restart.

If an incorrect number is provided, the switchdev driver fails to start, and prints an error message in the kernel log.

On System V systems, the configuration is typically done via initialization scripts located in the `/etc/init.d` folder.

To get the current profile number, which was loaded by the switched driver (e.g. read the switchdev driver parameter value). Run the following command:
```
cat /sys/module/prestera_sw/parameters/trap_policer_profile
```
# Policing of Dynamic Traps
You can configure the device to trap certain packets to the CPU port (switchdev port). This is done by the `tc` tool using the `trap` flower action. The default rate limit and TC of the trap is determined by the static profile which equals 4000 pps per TC. By default, the TC assigned to the trap is 3.

This feature enables you to set the rate limit and TC of a trapped packet by the ACL according to user-pecific values.

## Rate Limit of a Trapped Packet
To configure the rate limit and burst size of a trapped packet by the ACL, call the `tc`” policer action as part of creating the ACL rule. See tc-police.8 for more details on the command format and options used.

To trap a packet to the CPU, set the rate limit (bytes/bits per second unit) and burst size of the trap, use the following command:
```
tc ... action trap action police rate RATE burst BYTES conform-exceed drop
```
where,
* BYTES indicate the maximum allowed burst in bytes.
* RATE is the rate limit. See RATE parameters of the `tc` option for more information. Possible values are:
  * **bit or a bare number** - Bits per second
  * **kbit** - Kilobits per second
  * **mbit** - Megabits per second
  * **gbit** - Gigabits per second
  * **tbit** - Terabits per second
  * **bps** - Bytes per second
  * **kbps** - Kilobytes per second
  * **mbps** - Megabytes per second
  * **gbps** - Gigabytes per second
  * **tbps** - Terabytes per second

To specify in IEC units, replace the SI prefix (k-, m-, g-, t-) with the IEC prefix (ki-, mi-, gi- and ti-) respectively.

**NOTE**: The `police` action, requires the `rate` and `burst` parameters.

The `conform-exceed` option supports only the `drop` action, ??so it is required to be specified by the user, since the default conform exceed option is to reclassify. This option value is not validated by the switchdev driver, so any value may be specified, however, the driver performs the `drop` action even if a different value is provided.

For example, to trap the packet that matches SMAC 00:B5:4D:B1:32:22 of the packet, set the rate limit to 1024K bytes-per-second and burst to 2096 bytes, enter the following commands:
```
sudo tc qd add dev sw1p26 clsact
sudo tc filter add dev sw1p26 ingress flower skip_sw src_mac 00:B5:4D:B1:32:22 action trap \
action police rate 1mibps burst 2096 conform-exceed drop
```
**NOTE** Rule and police?? of the packet are bound to one port only (sw1p26 in this example). To bind the rule and the policer to multiple ports, use the shared block feature of the `tc` tool. See ACL documentation [5] on more details on configuring a shared block.

## Set the TC of a Trapped Packet

To configure the TC of a user-defined trap, use the `hw_tc` option of tc flower filter. You can use this option together with the `police` action. If this option is not specified, TC = 3 is used as a default value for all ALC traps.
```
tc ... action trap hw_tc TCID …
```
where,

`TCID` is the TC queue number (0-7).

For example, to trap the packet that matches SMAC 00:B5:4D:B1:32:22 of the packet??, and set the TC of that trap to the highest one??, enter the following commands:
```
sudo tc qd add dev sw1p26 clsact
sudo tc filter add dev sw1p26 ingress flower skip_sw src_mac 00:B5:4D:B1:32:22 hw_tc 7 action trap
```
## Assign a Policer to Multiple Ports

A policer ACL rule can be assigned to multiple ports in the same way as a regular?? ACL rule (using shared blocks). For example, to assign a trap ACL rule to two ports and set rate limit to the flow, enter the following:
```
sudo tc qdisc add dev sw1p1 ingress_block 1 clsact
sudo tc qdisc add dev sw1p2 ingress_block 1 clsact
sudo tc filter add block 1 ingress flower skip_sw src_mac 00:B5:4D:B1:32:24 action trap action police rate 1mibps burst 6000 conform-exceed drop
```
**NOTE** You can also use `hw_tc` for the example above.

## Precedence over Static Trap Limits

You cam add a dynamic (user) ACL rule with the flow which matches the static profile flow. In this case, the user-defined rule will have precedence over the static rule, and all limitations on dynamic traps are applied for this trap (4000 pps per TC, default TC = 3 etc.). 
For example, to create an ACL trap rule with precedence over the LLDP static rate limiter, enter the following command:
```
sudo tc qd add dev sw1p26 clsact
sudo tc filter add dev sw1p26 ingress flower skip_sw src_mac 01:80:c2:00:00:0e action trap \
action police rate 1mibps burst 2096 conform-exceed drop
```
It is possible also to change default TC in the example above.

## Limitations
These are the limitations on Policing of Dynamic Traps
* The following parameters the `policer` action are supported: `rate` and `burst` and `conform-exceed`.
* Specifying `rate` as a percentage is not supported.
* Specifying cell size for the `burst` option is not supported.  
* Specifying the `burst` values as less than the packet size sent to port does not drop all packets. The packets are be rate-limited according to the user defined rate.

# Policing Data Path Traffic
Configuring the policing of data path traffic is similar to the configuration of dynamic traps, except that trap action must not be specified.

## Rate Limit Data Path Traffic

To set the rate limit (bytes/bits per second) and burst size for a specific packet type, enter the following command:
```
tc ... action police rate RATE burst BYTES [action pass] conform-exceed drop
```
**NOTE:** `action pass` is the default action and can be omitted.

For example, to limit the rate of traffic that matches SMAC 00:B5:4D:B1:32:22 to 100M bytes per second and burst 64000 bytes, enter the following commands:
```
sudo tc qd add dev sw1p26 clsact
sudo tc filter add dev sw1p26 ingress flower skip_sw src_mac 00:B5:4D:B1:32:22 \
action police rate 100mbps burst 64000 conform-exceed drop
```

You can set the maximum policing rate to the same rate as for dynamic rules, but the actual rate is limited by the capabilities of the physical port. Please note, that SCT limitation for data path policing is not get applied.

## Assigning A Policer to Multiple Ports

As with dynamic traps, a shared block can be used to police data path traffic for multiple ports. In this case the expected rate will be divided among all ports.
```
sudo tc qdisc add dev sw1p1 ingress_block 1 clsact
sudo tc qdisc add dev sw1p2 ingress_block 1 clsact
sudo tc filter add block 1 ingress flower skip_sw src_mac 00:B5:4D:B1:32:24 action police rate 1mibps burst 6000 conform-exceed drop
```
## Limitations
* When ports in an ingress_block belong to different Processor Port?? (PP), the expected rate is doubled (applicable for AlleyCatC3x PPs).
* TC configuration is not applied for data path policing.

# Debugging CPU Code Counters

For development, or debugging purposes, it is important to be able to see the number of incoming packets coming per CPU packet code. This also allows us to track and refine the static profile setting. This feature should not be released to production.

## Enabling Debug CPU Code Counter

This feature is enabled as part of the switchdev driver initialization. To start working with CPU code debug counters, initialize the `debug fs` in the Linux kernel by mounting it to some user point. To do this, enter the following commands:
```
mount -t debugfs none <MOUNTPOINT>
```
where, 
`<MOUNTPOINT>` is typically located under `/sys/kernel/debug`.

Once the `debug fs` is mounted, cpu code debug counters are located under the `<MOUNTPOINT>/prestera/traps` folder.


## Retrieving CPU Code Counters

Each `cpu_code_<CPU CODE>_stats` file or entry in the `traps` folder represents a specific CPU code counter (the number of packets received per CPU code). For example, to read the number of packets received for CPU code 188, enter the following command:
```
cat <MOUNTPOINT>/prestera/traps/cpu_code_188_stats
```
For each CPU code in a prestera debug fs folder, there are 256 entries.
```
<MOUNTPOINT>/prestera/traps/cpu_code_0_stats
<MOUNTPOINT>/prestera/traps/cpu_code_1_stats
…
<MOUNTPOINT>/prestera/traps/cpu_code_255_stats
```
To read all counters at once (for all CPU codes), there is separate entry or file for this purpose. For example:
```
cat <MOUNTPOINT>/prestera/traps/cpu_code_stats
```
the output of the command above is as follows:
```
5:200000
26:200000
27:200000
28:200000
188:200000
```
where, the first number in the line defines the CPU code, and the next number, preceded by a colon “:”, is the number of packets received for the specified CPU code. Each line represents a counter for one CPU code. This entry does not show CPU counters which are zero.

**NOTE:** Counters are not reset upon read. Counters are reset only after system restart.

