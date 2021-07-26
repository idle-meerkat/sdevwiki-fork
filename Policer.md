This section describes how to police static and dynamic trapped packets to to the CPU port. This feature is an extension of ALC rules which are already covered in separate ACL document [5]. User should refer to this document on how to configure ALC rules on device before continue reading this section.

# Policing of static traps

Static packet traps are configured upon switchdev driver initialization. The list of packet traps, their rate limit and TC is defined in “Static trap” section above.

You can disable the entire static profile, or select the desired profile during switchdev driver initialization. To select or disable a static trap profile, use the switchdev kernel module parameter, provided for  the `prestera_sw` driver.
```
modprobe prestera_sw trap_policer_profile=<PROFILE>
```
where `<PROFILE>` is the number value:
* 0 – disable
* 1 – enable first profile. In this case, enable profile specified in the table above. Currently, only one profile is supported.

**NOTE**: Applying the changes requires as system restart.

If an incorrect number is provided, the switchdev driver will fail to start, and print an error message in the kernel log.

On System V systems, the configuration is typically done via initialization scripts located in /etc/init.d folder.

It is possible to get the current profile number which was loaded by the switched driver (e.g. read the switchdev driver parameter value). Run the following command:
```
cat /sys/module/prestera_sw/parameters/trap_policer_profile
```
