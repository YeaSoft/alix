Alix Rescue and PE System
=========================

>   __alix-rescue__ is a minimal standalone debian system intended as rescue,
    fallback and preinstallation environment for PC-Engines ALIX boards with
    serial interfaces. It is delivered in a version for direct installation
    on local CF media and a version for PXE netboot. __alix-rescue__ is
    available as Debian (Jessie, Wheezy and Squeeze) or Ubuntu (Precise and
    Lucid) based image.


Motivation
----------

Since appliances based on [PC-Engines Alix Boards](http://pcengines.ch) are
only equipped with a serial interface and boot only from the internal disc or
via PXE, it may be useful to have a live rescue operating system at hand that
boots either from a local volume or from PXE in order to have a robust and
reliable fallback solution in case of failure of the main operating system as
well as at installation time.

__alix-rescue__ satisfies these requirements. It is simple to deploy both on
PXE and on a local CF-Card and comes with all needed features for installing
a new system or troubleshooting the existing system.

!!! WORK IN PROGRESS !!!
========================

Features
--------

The __alix-rescue__ system comes with the following preinstalled software
packages:

 * __Connectivity and Usability__
   - ssh client and server
   - isc dhcp server
   - dnsutils
   - host
   - ftp
   - rsync
   - screen
   - nano
   - lsof

 * __Additional File System__
   - nfs-common
   - ntfs-3g

 * __File System and Volume Tools__
   - syslinux including extlinux binary
   - squashfs-tools
   - dosfstools
   - parted
   - mtools
   - extundelete
   - recover (not included in alix-rescue-deb8)
   - e2undel (not included in alix-rescue-deb8)

 * __Installation and deployment tools__
   - partimage
   - uic

A complete package list is included as `package-list.txt` in each distribution
archive. When running, the user can install additional software into the live
system by using the standard commands:

````
apt-get update
apt-get install <package>
````

Issuing `apt-get update` is mandatory, since the live filesystem does not
contain any cached package source listing in order to save as much as possible
memory.


Usage
-----

The following files are part of __alix-rescue__. All other files contained in
the distribution archive are only intended for sample or documentation.

  * `axrescue.vmlinuz` - The Linux kernel used for alix-rescue
  * `axrescue.initrd` - The standard (local) version of the initial ramdisk
  * `axrescue.initrdn` - The network enabled version of the initial ramdisk
  * `axrescue.squashfs` - The compressed root file system


Modes of Operation
------------------

__alix-rescue__ behaves differently based on the startup mode. If it is started
via PXE (using `axrescue.initrdn`) from the first network interface (the Alix
board does not permit to PXE boot from the other interfaces), that interface
is configured via DHCP. No more network initialization will occur. The other
network interfaces, if present, will not be automatically started and
configured. Nevertheless it is possible to start them subsequently from the
command line with the usual commands (like `ifup eth1`, etc.). Also
`/etc/network/interfaces` may be edited in order to configure network
parameters for the other interfaces.

Since the operation of the system is dependant upon the NFS connection to the
server where the live filesystem is stored, __it will be fatal to stop,
reconfigure or somehow mess with eth0__. This will unavoidably lead to a system
crash or halt.

When booted from the internal CF-Card, all interfaces found on the system
will be automatically initialized in the following way:

 * __Interface eth0 (near the USB ports)__:
     The interface will be configured by default via DHCP and will additionally
     get also the link local address `169.254.19.65/16`. Because of some
     particular configurable features, it will be referred to as the _primary
     interface_. It can be configured to a static address and to offer DHCP
     addresses (see below).

 * __Interface eth1__:
     The interface will be configured by default with the static IP address
     `192.168.2.1/24`. DHCP servicing (Dynamic addresses starting from
     `192.168.2.128` to `192.168.2.254`) is available and enabled for this
     interface.

 * __Interface eth2 (near the serial port)__:
     The interface will be configured via DHCP (if available).

In both modes of operation the machine will be reachable via SSH from the
network for the user `root` with the default password `password` or the
password specified on the kernel command line (see below).


![Front Panel of Alix 2D13](http://www.yeasoft.com/assets/images/articles/alix-front-4-e3.jpg)

alix-rescue Command Line Parameters
-----------------------------------

The following options __may__ or __must__ be specified on the kernel command
line.

  * `password=<password>` or `password=SHADOW:<hash value>`:
    This option sets the password for the user `root`. It can be specified as
    clear text or as a `/etc/shadow`-compatible hash value if prefixed with
    the word `SHADOW:`.
    On the console there is no need to know this password, since the
    consoles go directly into the shell. The password is useful when logging in
    via SSH. The default password is _"password"_.

  * `timezone=<tzname>`:
    This option sets the timezone of the system. The timezone must be specified
    in the format `AREA/ZONE`. If not specified, the timezone is set to
    `Etc/UTC`.

  * `branch=<device>`:
    This option sets the writable device for the filesystem writes during the
    NetPoldo runtime. It can be a physical filesystem formatted with `ext2`,
    `ext3` or any filesystem supported by the initial ramdisk or a ramdisk
    itself (use `/dev/ram` to specify the ramdisk). When using the ramdisk, all
    changes made during the session are lost after shutting down. It is
    permitted to reference the device by device name (e.g. `/dev/sda1`), by
    volume label (e.g. `LABEL=boot-disk`) and by unique volume id (e.g.
    `UUID=f5a49b89-e8c7-448a-a920-ae0aaac53b7b`). Depending upon the initial
    ramdisk image, this parameter may be optional or not. See below.

  * `loop=<root filesystem image file>`:
    This option sets the path and name to the compressed root filesystem image.
    Depending upon the intial ramdisk image, this parameter may be optional or
    not. See below.

  * `root=<root fs device>`:
    This option sets the device for the filesystem where the compressed root
    filesystem image is located. It can be a physical filesystem formatted with
    `ext2`, `ext3`, any filesystem supported by the initial ramdisk, an NFS
    mount (use `/dev/nfs` to specify the NFS mount) or autodetection of a local
    or removable device (use `/dev/detect`). It is permitted to reference the
    device by device name (e.g. `/dev/sda1`), by volume label (e.g.
    `LABEL=boot-disk`) and by unique volume id (e.g.
    `UUID=f5a49b89-e8c7-448a-a920-ae0aaac53b7b`). Depending upon the initial
    ramdisk image, this parameter may be optional or not. See below.

  * `console=<tty>[,<serial config>]`:
    This option tells the kernel to use the specified tty device for input and
    output. This option is mandatory in order to communicate with the Alix
    board over the serial interface. The standard configuration is
    `console=ttyS0,38400n8` that means: in- and output over the serial port
    (`ttyS0`) configured to 38400 Baud, 8 Bits, No Parity. If the option is not
    specified, the kernel itself does not send any messages over the serial
    interface. If for any reason the boot process fails and a recovery console
    is started from within the initial ramdisk, it will not be possible to get
    anything from the serial interface or to send anything to the board.

  * `dhcpdif-swap`:
    If specified, the primary interface used for manual configuration and dhcp
    server will be `eth2` and `eth0` will be configured by dhcp instead.

  * `dhcpdif-enable`:
    If specified, the DHCP service well be enabled also for the primary
    interface. This will work only if the primary interface is configured with
    a static ip address.

  * `dhcpdif-ip=<ip address>|static`:
    If specified, the primary interface will be initialized with the specified
    IP address or `192.168.1.1` if `static` was specified. When specified
    together with the enabled DHCP service, the offered scope will be adapted
    automatically to the specified address network.

  * `dhcpdif-mask=<ip address mask>`:
    If specified, the primary interface will be initialized with the specified
    IP address mask instead of `255.255.255.0`. When specified together with
    the enabled DHCP service, the offered scope will be adapted automatically
    to the specified network mask.

Based on the used initial ramdisk image, some parametrs may be mandatory or
not:

  * `axrescue.initrd`:
    The _local initial ramdisk image_ requires at least the `loop` and the
    `branch` parameters. If the `root` parameter is not specified, it defaults
    to `/dev/detect` and __alix_rescue__ tries to find a volume containing the
    compressed root filesystem image as specified in `loop`.

  * `axrescue.initrdn`:
    The _network initial ramdisk image_ requires at least the `loop` and the
    `branch` parameter. In addition it requires the `nfsroot` parameter to point
    to the NFS share containing the compressed root filesystem image and the
    `ip` parameter.



Samples
-------

All following samples show boot scenarios based on syslinux configuration
files.


### Boot from local volume ###

````
KERNEL  /axrescue.vmlinuz
APPEND  initrd=/axrescue.initrd root=LABEL=bootbank loop=/axrescue.squashfs \
        branch=/dev/ram password="aBRAcadaBRA" console=ttyS0,38400n8
````

Boots __alix-rescue__ from the device with the volume name bootbank where all
files of __alix-rescue__ are located at the top of the filesystem. All writes
are done in the ramdisk, so there is no persistent storage. The password of
the user `root` is set to _"aBRAcadaBRA"_.


### Boot from Network with NFS ###

````
KERNEL   alix-rescue/axrescue.vmlinuz
APPEND   initrd=alix-rescue/axrescue.initrdn rootdelay=2 root=/dev/nfs \
         nfsroot=10.65.6.1:/mnt/data/tftproot,rw ip=dhcp \
         loop=/yeaboot/alix-rescue/axrescue.squashfs branch=/dev/ram \
         console=ttyS0,38400n8
````

PXE Boot of __alix-rescue__ via TFTP with the kernel and the initial ramdisk
located in the directory `alix-rescue`of the TFTP server area. The compressed
root filesystem image is located on the NFS share `/mnt/data/tftproot` on the
server with the address `10.65.6.1` in the directory `/yeaboot/alix-rescue/`.
The IP address of the booting computer is assigned by DHCP. All writes are
done in the ramdisk, so there is no persistent storage.

More samples will be included in the distribution archive.


Quick deployment
----------------

This is a quick and dirty step-by-step instruction for creating a bootable
CF-Card containing __alix-rescue__. The following assumes that the CF-Card
is connected through a card reader to a computer running a Debian or Ubuntu
Linux with the package syslinux installed.

After connecting the card reader, the filesystem on the card may be mounted
if there is already a partition with a valid filesystem on it. In such a
case, the card must __be unmounted__ by manually.

> __WARNING__

> The following commands may break your system if you are not knowing what you
  are doing - so please be careful and try to understand what you are doing.
  __YOU__ are responsible for what is happening! __If you break it, you own
  both parts!__

In this instruction we assume the CF-Card as `/dev/sdX`. Please substitute it
with the device name assigned by your system to the card.

First of all the card must be partitioned:

````
    sudo fdisk /dev/sdX
````

Now delete all existing partitions and create a new small primary partition
nr. 1 (it should have at least 256 MB but try to make it bigger since you
will need it in future to store on it also the kernels of your appliance
operating system. Set the boot flag on the partition (command __a__). You can
verify that the parition is bootable by showing at the parition table (with
the command __p__):

````
  Disk /dev/sda: 16.0 GB, 16022200320 bytes
  255 heads, 63 sectors/track, 1947 cylinders
  Units = cylinders of 16065 * 512 = 8225280 bytes
  Sector size (logical/physical): 512 bytes / 512 bytes
  I/O size (minimum/optimal): 512 bytes / 512 bytes
  Disk identifier: 0x00000000

     Device Boot      Start         End      Blocks   Id  System
  /dev/sdX1   *           1          66      530113+  83  Linux

  Command (m for help): 
````

Save the partition table. Now format it and mount it into your filesystem:

````
    sudo mke2fs -L boot /dev/sdX1
    sudo mkdir -p /mnt/cfcard
    sudo mount /dev/sdX1 /mnt/cfcard
````

Now make it bootable by writing a bootable MBR (Master Boot Record) and by
saving a boot loader onto the card:

````
    sudo cat /usr/lib/syslinux/mbr.bin > /dev/sdX
    sudo mkdir -p /mnt/cfcard/syslinux
    sudo extlinux --install /mnt/cfcard/syslinux
````

Unpack the ___alix-rescue___ files to the boot partition and then unmount:

````
    sudo tar xvzf alix-rescue-10.04.4-05.tar.gz -C /mnt/cfcard
    sudo unmount /mnt/cfcard
    sudo rmdir /mnt/cfcard
````

Now you can plug the CF-Card in your Alix board, power it on and enjoy the
show!


Related Links
-------------

   * Download location for prebuilt binaries:
     http://www.yeasoft.com/downloads/alix/alix-rescue/
   * Project home page:
     http://www.yeasoft.com/site/projects:alix:alix-rescue
   * GitHub Repository:
     https://github.com/YeaSoft/alix

__alix-rescue__ can be built from its templates using __UIC__, the Unified
Installation Creator. See the [project page][1] for further information.
 __UIC__ is also [hosted on GitHub][2].

[1]: http://www.yeasoft.com/site/projects:uic
[2]: https://github.com/YeaSoft/uic


Copyright and Legal Stuff
-------------------------

NetPoldo is a derivative work based on Debian and Ubuntu Linux created by Leo
Moll <leo.moll@yeasoft.com>

Copyright: 2012-2014 Leo Moll <leo.moll@yeasoft.com>

> This package is free software; you can redistribute it and/or modify
  it under the terms of the GNU General Public License as published by
  the Free Software Foundation; either version 2 of the License, or
  (at your option) any later version.

> This package is distributed in the hope that it will be useful,
  but WITHOUT ANY WARRANTY; without even the implied warranty of
  MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
  GNU General Public License for more details.

> You should have received a copy of the GNU General Public License
  along with this program. If not, see <http://www.gnu.org/licenses/>
