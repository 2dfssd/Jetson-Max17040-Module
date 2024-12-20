# Jetson-Max17040-Module
This repository is a tutorial to install the Max17040 Battery Monitor module driver on a Nvidia Jetson system.

## Download Jetson Source Code
- Find your Jetson Linux version in the Nvidia Developer Website
- In your Jetson system, download the Driver Package (BSP) Sources
- Extract the folder
```bash
tar -xvf public_sources.tbz2
```
- Extract the Kernel Source folder
```bash
tar -xvf kernel_src.tbz2
```
- The max17040 driver source code is in:
```bash
Linux_for_Tegra/kernel/kernel-xx/drivers/power/supply/max17040_battery.c
```

## Build 
Go to the folder with the module.
```bash
cd Linux_for_Tegra/kernel/kernel-xx/drivers/power/supply/max17040_battery.c
```
If you wish to build only the max17040 module, change the Makefile simply to:
```bash
obj-m += max17040_battery.o
```
To build the driver:
```bash
make -C /lib/modules/$(uname -r)/build M=$PWD
```
It will generate a .ko file. Copy it to the Jetson modules folder
```bash
sudo cp max17040_battery.ko /lib/modules/$(uname -r)/kernel/drivers/power/supply/
```
Update dependancies so the system recognizes the new module
```bash
depmod -a
```

## Alter Device Tree
- Find your system's Device Tree (the device tree used by the system is usually in /boot/extlinux/exlinux.conf)
- Uncompille it to a dts file
```bash
dtc -I dtb -O dts device_tree.dtb -o device_tree.dts
```
- Find your prefered I2C node (usually I2C1) and add a max17040 node
```c
max17040@36 {
    compatible = "maxim,max17040";
    reg = <0x36>;
    status = "okay";
};
```
- Recompile the new Device Tree
```bash
dtc -I dts -O dtb device_tree.dts -o new_device_tree.dtb
```
- Change extlinux.conf to read the new dtb file
- Reboot the system
