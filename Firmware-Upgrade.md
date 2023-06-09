Upgrading the firmware to enable using Switchdev includes the following stages:  
* [Fetch the Latest HOST Image](#Fetch-the-Latest-HOST-Image)
* [Boot from PCIe](#Boot-from-PCIe)
* [Upgrade the Flash Image](#Upgrade-the-Flash-Image)

These stages are described below.

## Fetch the Latest HOST Image  
1.	Connect to the HOST CPU, in our example this is an ARMADA-7K device.  
2.	Fetch the required flash image from the serverm as shown in the following example:  
**DNI:**  
`tftp 10.5.116.4 -m binary -c get /tftpboot/switchdev/shared/mrvl_val/tn4810m-prestera-fw-flash-image-v1.0.img`  
`tftp 10.5.116.4 -m binary -c get /tftpboot/switchdev/shared/mrvl_val/tn48m-prestera-fw-flash-image-v1.0.img`   
**ACCTON:**  
`tftp 10.5.116.4 -m binary -c get switchdev/shared/mrvl_val/as5114-loader/as5114_48x-prestera-fw-flash-image-v1.0.img`  
`tftp 10.5.116.4 -m binary -c get switchdev/shared/mrvl_val/as4224-loader/as4224_52p-prestera-fw-flash-image-v1.0.img`  
3.	`/usr/bin/fw_flash -i <flash_image>`  

At this point the HOST image is updated and you can proceed to the next stage.  

## Boot from PCIe  
From the HOST CPU Linux CLI:
1. Modify the boot vector - boot from PCIe:  
The platform includes a CPLD that controls the BOOT vector of the coprocessor (FW_CPU).  
If the coprocessor NAND FLASH is empty, use the following procedure to upload the NAND Flash, and repeat the process for all boards.  
**DNI:**  
`i2cset -y 2 0x41 0x11 0x2c`  
**ACCTON:**  
`i2cset -y 0 0x40 0xe0 0x1`  
`i2cset -y 0 0x40 0x51 0x6c`   
2. Soft reboot on the HOST CPU:   
`amzgo-host# reboot`  
3. From the firmware console you should see this output, indicating boot from PCIe:  
`BootROM - 1.73`  
`Booting from PEX0 (X1)`  
4. Stop at HOST u-boot and modify the bar window size allocation:  
`Marvell>>`  
**DNI:**  
`/* window size */`  
`mw 0xc0081804 0x007f0001`  
`mw 0xc0081808 0x003f0001`  
**ACCTON:**  
`mw 0xf6081804 0x007f0001`  
`mw 0xf6081808 0x003f0001`  
5. Boot to the HOST Linux system:  
`Marvell>> boot`  
6. At this point the PCIe is ready to use:  
`amzgo-host# lspci`   
`00:00.0 PCI bridge: Marvell Technology Group Ltd. Device 0110`  
`01:00.0 Memory controller: Marvell Technology Group Ltd. Device 6820 (rev 0a)`   

## Upgrade the Flash Image  
1. Download the image from TFTP:  
**DNI:**  
`tftp 10.5.116.4 -m binary -c get switchdev/shared/mrvl_val/tn4810m-loader/tn4810m-prestera-fw-flash-image-v1.0.img`  
`tftp 10.5.116.4 -m binary -c get switchdev/shared/mrvl_val/tn48m-loader/tn48m-prestera-fw-flash-image-v1.0.img`  
**ACCTON:**   
`tftp 10.5.116.4 -m binary -c get switchdev/shared/mrvl_val/as5114-loader/as5114_48x-prestera-fw-flash-image-v1.0.img`  
`tftp 10.5.116.4 -m binary -c get switchdev/shared/mrvl_val/as4224-loader/as4224_52p-prestera-fw-flash-image-v1.0.img`  
2. Upgrade the firmware flash image:  
`/usr/bin/fw_flash -f -i <image>`  
3. Modify the boot vector again - boot from NAND:  
`amzgo-host# `  
**DNI:**  
`i2cset -y 2 0x41 0x11 0x4a`  
**ACCTON:**  
`i2cset -y 0 0x40 0xe0 0x1`  
`i2cset -y 0 0x40 0x51 0x4a`  
4. Reboot.  
5. The firmware console should show this output:  
`BootROM - 1.73`  
`Booting from NAND flash`  

You have completed the firmware upgrade. 