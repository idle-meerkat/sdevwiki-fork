# Installation
## Update the Firmware Binary Image
The application should be in the HOST CPU FS /usr/bin directory and it enables updating the firmware Co-Processor FLASH NAND.  
Do the following to update the image:  
1. Make sure the fw_flash application is located in HOST CPU FS /usr/bin directory.  
1. Enable the execution rights for the application by calling `chmod +x` Linux command.  
1. Connect to the HOST A7K CPU, and fetch the required flash image into the HOST CPU FS:  
Use the `tftp` command to download the files.  
**DNI**:  
`tftp 10.5.116.4 - binary -c get switchdev/tn4810m-prestera-fw-flach-image-v1.0.img`  
or  
`tftp 10.5.116.4 - binary -c get switchdev/tn48m-prestera-fw-flach-image-v1.0.img`  
**ACCTON**:  
`tftp 10.5.116.4 - binary -c get switchdev/as5114_48x-prestera-fw-flach-image-v1.0.img`  
**Both**:  
`/user/bin/fw_flash -i <flash_image>`  
## Host ARMADA 7K CPU  
### Firmware Agent Application (mvsw_prestera_fw.img)  
This image is intended to be loaded by the HOST CPU Switchdev driver into the firmware CoProcessor.  
It includes the Firmware Agent application.  
This image must be uploaded to the standard firmware path on running DENT/ONL rootfs under `/lib/firmware/marvell/`   
**NOTE**: The DENT/ONL build is done at a later stage by a system integrator, therefore, this image must be placed manually in the DENT/ONL FS.  This means that you can only copy (scp) it to the target DUT after the DENT/ONL FS is run. 
This step is done only once. You can include this copy as part of the DENT/ONL build.  
## Linux Kernel Switchdev Drivers 
### Switchdev Driver Code Patch (onl-switchdev-v2.x.x.patch)   
DENT/ONL Switchdev patch can be applied via a patch or git apply tool.  
It applies the following code and must be patched into the DENT/ONL code package.  
* Switchdev driver  
* Kernel configs  

**Example**:  
`patch -d <ONL_GIT_SOURCES> -p1 < onl-switchdev-v2.3.0.patch`  
**NOTE**: When Switchdev driver is configured as a built-in driver into the kernel, the driver cannot be used to load a firmware image from the rootfs to the firmware CPU.  
The reason is that the Switchdev driver starts before the rootfs FS is mounted during the kernel startup. As the firmware is loaded from the rootfs, the Switchdev kernel driver has been changed to compile as **kernel modules**. Provided that the DENT/ONL build system does not include those modules in the output DENT/ONL image now, it must manually copy those KO (prestera_sw.ko, prestera_pci.ko) to the DENT/ONL image and manually start it using insmod.  
The Switchdev driver locations in the DENT/ONL build after compilation: 
```
(find . -name "prestera_*.ko" 2>/dev/null): 

<ONL_GIT_SOURCES>/packages/base/arm64/kernels/kernel-5.6-lts-arm64-all/builds/lib/modules/5.6/kernel/drivers/net/ethernet/marvell/prestera_sw/prestera_sw.ko 
<ONL_GIT_SOURCES>/packages/base/arm64/kernels/kernel-5.6-lts-arm64-all/builds/lib/modules/5.6/kernel/drivers/net/ethernet/marvell/prestera_sw/prestera_pci.ko 
```
Copy the Switchdev drivers from the ONL build to the ONL FS (create the folder if it does not exist):      
```
/lib/modules/5.6.7/extra/ 
```
Additional ONL platform configuration:  
```
Udev rules that need to be applied to ONL (e.g. /etc/udev/rules.d/10-switchdev.rules):SUBSYSTEM=="net", ACTION=="add", ATTR{phys_switch_id}=="[0-9][0-9]", ATTR{phys_port_name}!="", PROGRAM="/bin/sh -c 'printf %1d $attr{phys_switch_id}'", NAME="sw%cp$attr{phys_port_name}" 
```
**NOTE**: `udevadm control –-reload` must be reformed to reload the rules once modified.  
