# Building the AGL Demo Platform for QEMU

To build the QEMU version of the AGL demo platform use machine **qemux86-64** and feature **agl-demo**:

```bash
source meta-agl/scripts/aglsetup.sh -m qemux86-64 agl-demo
bitbake agl-demo-platform
```

TODO: configure and build SDK as well?

By default, the build will produce a compressed *vmdk* image as *agl-demo-platform-qemux86-64.vmdk.xz*

# Deploying the AGL Demo Platform for QEMU

TODO: Windows tools

In order to deploy the resulting *vmdk.xz* image, first decompress it:

```bash
xz -d agl-demo-platform-qemux86-64.vmdk.xz
```

## QEMU

* TODO: where to get qemu-system-x86_64 on N Linux hosts
* TODO: document broken qemu that cannot be used in the SDK
* TODO: document runqemu from the build host?

Boot the *agl-demo-platform-qemux86-64.vmdk* image in qemu with kvm support.

```bash
qemu-system-x86_64 -enable-kvm -m 2048 \
	-hda agl-demo-platform-qemux86-64.vmdk \
	-cpu kvm64 -cpu qemu64,+ssse3,+sse4.1,+sse4.2,+popcnt \
	-vga std -show-cursor \
	-device virtio-rng-pci \
	-serial mon:stdio -serial null \
	-soundhw hda \
	-net nic,vlan=0 \
	-net user,hostfwd=tcp::$SSH_PORT-:22
```

## VirtualBox

TODO: Document where to get VirtualBox

Boot the *agl-demo-platform-qemux86-64.vmdk* image in VirtualBox.

  * Start VirtualBox
  * Click *New* to create a new machine
    * Enter *AGL QEMU* as the Name
    * Select *Linux* as the Type
    * Select *Other Linux (64-bit)* as the Version
    * Set *Memory size* to *2 GB*
    * Click *Use an existing virtual hard disk file* under *Hard disk*
       * Navigate to and select the *agl-demo-platform-qemux86-64.vmdk* image
  * Ensure that the newly created *AGL QEMU* machine is highlighted and click *Start*

## VMWare Player

TODO: Document where to get VMWare Player

Boot the *agl-demo-platform-qemux86-64.vmdk* image in VMWare Player.

  * Start VMWare Player
  * Select *File* and *Create a New Virtual Machine*
    * Select *I will install the operating system later* and click *Next*
    * Select *Linux* as the Guest Operating System, *Other Linux 3.x kernel 64-bit* as the Version, and click *Next*
    * Enter "AGL QEMU" as the Name and click *Next*
    * Leave disk capacity settings unchanged and click *Next*
    * Click *Finish*
  * Select/highlight *AGL QEMU* and click *Edit virtual machine settings*
    * Select/highlight *Memory* and click *2 GB*
    * Select/highlight *Hard Disk (SCSI)* and click *Remove*
    * Click *Add*
      * Select *Hard Disk* and click *Next*
      * Select *SCSI (Recommended) and click *Next*
         * Select *Use an existing virtual disk* and click *Next*
         * Browse and select the agl-demo-platform-qemux86-64.vmdk* image
         * Click *Finish*
         * Click *Keep Existing Format*
    * Click *Save*
  * Ensure that the newly create *AGL QEMU* machine is highlighted and click *Power On*
