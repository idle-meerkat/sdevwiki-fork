This section describes how to police static and dynamic trapped packets to to the CPU port. This feature is an extension of ALC rules which are already covered in separate ACL document [5]. User should refer to this document on how to configure ALC rules on device before continue reading this section.

# Policing of Static Traps

Static packet traps are configured upon switchdev driver initialization. The list of packet traps, their rate limit and TC is defined in “Static trap” section above.

You can disable the entire static profile, or select the desired profile during switchdev driver initialization. To select or disable a static trap profile, use the switchdev kernel module parameter, provided for  the `prestera_sw` driver.
```
modprobe prestera_sw trap_policer_profile=<PROFILE>
```
where `<PROFILE>` is the number value:
* 0 – disable
* 1 – enable first profile. In this case, enable profile specified in the table above. Currently, only one profile is supported.

**NOTE**: Applying the changes requires a system restart.

If an incorrect number is provided, the switchdev driver will fail to start, and print an error message in the kernel log.

On System V systems, the configuration is typically done via initialization scripts located in /etc/init.d folder.

It is possible to get the current profile number which was loaded by the switched driver (e.g. read the switchdev driver parameter value). Run the following command:
```
cat /sys/module/prestera_sw/parameters/trap_policer_profile
```
# Policing of Dynamic Traps
You can configure the device to trap certain packets to the CPU port (switchdev port). This is done by the `tc` tool using the `trap` flower action. The default rate limit and TC of the trap is determined by the static profile which equals 4000 pps per TC. By default, the TC assigned to the trap is 3.

This feature enables you to set the rate limit and TC of a trapped packet by the ACL according to user-pecific values.

## Rate Limit of a Trapped Packet
To configure the rate limit and burst size of a trapped packet by the ACL, call the `tc`” policer action  during ACL rule creation. See tc-police.8 for more details on the command format and options used.

To trap the packet to the CPU, set the rate limit (bytes/bits per second unit) and burst size of this trap, use the following command:
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
gbps Gigabytes per second

tbps Terabytes per second

To specify in IEC units, replace the SI prefix (k-, m-, g-, t-) with IEC prefix (ki-, mi-, gi- and ti-) respectively.

NOTE: the rate and burts parameters are required options when using “police” action.

conform-exceed option supports only drop action, so it is required to be specified by the user, since the default conform exceed option is to reclassify. Please note, this option value is not validated by the switchdev driver, so any value may be specified by the user. In any case, the driver will use drop action even if different value is provided.

For example, to trap the packet that match SMAC 00:B5:4D:B1:32:22 of the packet, set rate limit to 1024K bytes per second and burst 2096 bytes, the following commands should be used:
sudo tc qd add dev sw1p26 clsact

sudo tc filter add dev sw1p26 ingress flower skip_sw src_mac 00:B5:4D:B1:32:22 action trap \

action police rate 1mibps burst 2096 conform-exceed drop

Please note, that rule and police of the packet are bound to one port only (sw1p26 in our case). To bind the rule and the policer to multiple ports, the shared block feature of “tc” tool should be used instead. See ACL documentation [5] on more details on configuring the shared block.