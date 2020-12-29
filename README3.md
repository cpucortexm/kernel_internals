## Device Drivers

## Linux Boot process (see the boot process image)
1. Firstly the bootROM of the ARM chip gets executed on POR(power on reset) as the hardware reset logic makes ARM to start execution at bootROM.The bootROM will then based on the bootmode scans the hardware (NAND, NOR, SD/eMMC) and reads the bootstrap code(or header code) from NAND flash into internal SRAM. This bootstrap code starts initialing the DDR (RAM), also initialises hw such as clock speed etc.The bootstrap code then loads the U-boot from NAND flash to external DDR and runs it.

2. The U-Boot loads both the kernel image and the compiled device tree binary into RAM and passes the memory address of the device tree binary into the kernel as part of the launch.

3. U-Boot jumps to the kernel code.

    ![uboot_load_kernel](images/u-boot.png)

4. Kernel does architecture specific low level kernel initialisation code like enabling MMU, setting up page tables, caches and finally calls non architecture code using start_kernel()

5. start_kernel() is at init/main.c, and it then initialises the following:
* initialise kernel core (memory, scheduler, interrupts)
* init built in drivers
* mount root file system based on the kernel args passed by u-boot
* initialise first user process init from /sbin/init which is the parent process of all the user processes. The init in embedded systems can be one of busy-box init, systemd or system V init etc
    ![kernel_boot_process](images/boot_process.png)


## Driver model:
Driver model is used to have some kind of abstraction so that you can have clean code or uniform code(remove code duplication).
Also with this abstraction model you can view the driver details from user space like status of the device, power states, which bus the device is attached to etc. through sysfs.

Device mode is with 3 structures:
* struct bus_type // e.g. USB, platform, PCI etc
* struct device_driver // driver capable of handling the devices on the bus
* struct device // represents a device connected to bus


..TBD
How driver init(), probe() and exit() gets called by the device model.



## _init and _exit macros
These are special sections in the linker file of kernel.
They are effective only on built-in kernel modules.

__init is effective on built in drivers as it will free the memory of the function once it is initialised. During kernel build,  the build system groups all the functions with __init in one place so that kernel can discard or free this memory once initialisation is done. Hence for loadable modules it is ineffctive as kernel has to do lot of work to delete the memory as they are not compiled during kernel build.

__exit parameter tells the kernel to omit this function when the module is built-in, since in built in modules this functions would never execute.

## Platform drivers (I2C, SPI)

## Char drivers and Network drivers

## Interrupts in drivers and Threaded IRQ

## container_of():


## Kernel sleeping mechanisms

## Kernel locking mechanisms

## Work deferring mechanisms

## Device Tree

## Delay and Timers

## DMA in drivers