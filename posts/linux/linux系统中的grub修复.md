### Environment

* ubuntu 
* grub

### Intro

The *rescue* mode is a major GRUB 2 enhancement. If GRUB 2 fails to find a useable *grub.cfg* and is unable to transfer control to a kernel it will drop to a *grub-rescue>* prompt. From this prompt the user can investigate problems, make changes, and retry the boot. 

### Steps

##### ls

This will display the known devices and partitions. From this information, the user must determine the device and partition on which the system is installed.

##### set prefix=(hdX,Y)/boot/grub

If incorrect, "no such disk" or "not found" errors will occur later.

##### set root=(hdX,Y)********

In this command, *X* is the device/drive, starting with 0. *Y* is the partition, starting with 1. Example: (hd0,1) is sda1. (hd2,5) is sdc5. 

##### set

Inspect the "prefix=" listing. It should match the root designation in Step 3, in the following format: *prefix=(hdX,Y)/boot/grub*. 

##### ls /boot

Inspect the contents. The user should see varioius kernels, initrd images and the *grub* folder. If not, use the **ls** command to inspect the device and attempt to find these files and folders. If necessary, set another device as root.

##### insmod /boot/grub/linux.mod

Load (*insert module*) the *linux* module. Without this module loaded, the user will receive an "*Unknown command linux*" message when trying to load the kernel.

##### linux /vmlinuz root=/dev/sdXY ro

Load the *linux* kernel, substituting the correct designations for "X" and "Y" (example: sda1). The user will see a message showing the kernel has been loaded.

##### initrd /initrd.img

Load the *initrd* image. When pressing *ENTER* the user may or may not see a message in the terminal. 

##### boot

Attempt to boot using the information entered. 

These changes are not permanent. After successfully booting into the system the user should run `sudo update-grub` and inspect the GRUB 2 configuration file (*/boot/grub/grub.cfg*).The user may need to reinstall GRUB 2 using `sudo grub-install /dev/sdX`.