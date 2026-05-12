Software Overview
The CloudGate flash layout consists of these components:
Bootloader
Linux kernel 2.6.35
Router software: Modified OpenWRT stack
Firmware customization
Developer image
 

When the system is fully booted, you’ll see the following mounted filesystems:
1. Root-filesystem: read-only. Stored in /rom. This contains the basic option firmware. This is a heavily modified OpenWRT stack.
2. Overlay read-write flash filesystem of 20MB. Mounted at /. This filesystem can be used to store pieces of data. Since it is stored in flash, the data will persist across reboots. However it will be wiped by a factory reset.
3. Read-write tmpfs mounted at /tmp. This can be used to temporarily store small pieces of data. Since it is stored in RAM, it will be gone after a power cycle or reboot. RAM being a fairly limited resource, care should be taken that it is not used up by the filesystem.
 
The firmware customization filesystem and developer image will be mounted depending on whether they are present or not. In addition, the router software will verify if both filesystems are signed with the proper key (see next chapter) before they are mounted.
If the developer image passes verification, it will be mounted at /rom/mnt/cust. The maximum size allocated for the developer image is 30MB. The firmware customization is mounted at/rom/mnt/base_cfg.

Secure boot
The boot process in CloudGate tries to establish a chain of trust with the iMX.280 processor itself being the first link in the chain.
The iMX.280 has an authenticated boot feature, which protects our bootloader against modification. The bootloader in turn authenticates the kernel and the root filesystem. During the booting of the root filesystem, the configuration filesystem and the developer image are authenticated.
No image/filesystem will be mounted or used before it is properly authenticated by software which was already authenticated, hence establishing a chain of trust.

Currently the developer image is checked against a developer key, whose public key is included in the CloudGate software. The developer public & private key are also included in the SDK.

Therefore images generated with the CloudGate SDK will pass authentication against the developer key. Of course, since the public & private key are distributed along with the SDK, everybody can write software for the CloudGate.

In one of the next iterations of the option Firmware, we will allow the use of custom keys. These keys need to be signed by Option before they can be used on the CloudGate. Using a custom key will permanently disable the developer mode with a hardware fuse in the processor, so the developer key will not be able to be used on that unit.

After the developer image is properly mounted, the firmware will look for an executable called ‘isv’ and try to run it.
