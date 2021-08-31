# Introduction
This site is part of our native support for hardware switches, through a Linux Kernel.  
The current Packet Processor (PP) SDK was created at the Linux user-space level. 
Note that interrupts, packets and configuration are offloaded to the hardware, as opposed to handling this traffic by the Linux kernel. While this approach has its advantages – its main drawback is that it does not support the use of standard Linux routines using an official (or partly official) Linux OS to provide PP functionality.  

The following figure shows the different layers involved in these two methods:
![Traffic Handling](images/traffic_handling.png)
To ease the integration of PP drivers into the Linux Kernel, an infrastructure was built into the Kernel, called Switchdev. This layer, along with Linux NetDev modules, acts a basis for interaction between the upper Linux modules and the underlying PP driver that implements the Switchdev / NetDev operations & notifies them of various hardware events.

The following image shows the Switchdev driver in the kernel:
![Switch Driver in the Kernel](images/linux_in-kernel_switchdev.JPG)
