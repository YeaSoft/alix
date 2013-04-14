==============================================================================
Alix Rescue and PE System - Version 12
==============================================================================

     alix-rescue is a minimal standalone debian system intended as rescue,
     fallback and preinstallation environment for PC-Engines ALIX boards with
     serial interfaces. It is delivered in a version for direct installation
     on local CF media and a version for PXE netboot. alix-rescue is available
     as Debian (squeeze) or Ubuntu (hardy and lucid) based image.

Motivation
==========

Since appliances based on PC-Engines Alix Boards are equipped only with a
serial interface and boot only from the internal disc or by PXE, it may be
both useful to have a live rescue operating system at hand that boots either
from a local volume or from PXE in order to have a robust and reliable
fallback solution in case of failure of the main operating system or at
installation time. alix-rescue satifies these requirements. It is simple to
deploy, both on PXE as on a local CF-Card and comes with all features needed
to fix the existing system or install a new system.


Features
==========

 * Connectivity and Usability
   + ssh
   + isc dhcp server
   + lsof
   + nano
   + rsync
   + screen

 * Additional File Systems
   + nfs-common

 * File System and Volume Tools
   + parted

 * Installation and deployment tools
   + uic

At runtime the user is able to install additional software in the live
system by using the standard means of ubuntu:

        apt-get update
        apt-get install <package>

Issuing apt-get update is mandatory, since the live filesystem does not
contain any package source listing in order to save as much as possible
memory.


Usage
=====

The following files are part of alix-rescue. All other files contained in the
distribution archive are only intended for sample or documentation.

axrescue.vmlinuz   This is the Linux kernel used for alix-rescue
axrescue.initrd    This is the standard (local) version of the initial ramdisk
axrescue.initrdn   This is the network enabled version of the initial ramdisk
axrescue.squashfs  This is the compressed root file system


Modes of Operation
------------------

alix-rescue beahves differently based on the startup mode. If it is started
via PXE (with axrescue.initrdn) from the first network interface (the Alix
board does not permit to PXE boot from the other interfaces), that interface
is configured via DHCP. No more network initialization will occur. The other
network interfaces, if present, will not be automatically started and
configured. Nevertheless it is possible to start them subsequently from the
commandline using the usual commands (like ifup eth1, etc.). Also
/etc/network/interface may be edited in order to configure network parameters
for the other interfaces.

Since the operation of the system is dependant upon the NFS connection to the
server where the live filesystem is stored, it will be fatal to stop,
reconfigure or somewhow mess with eth0. This will lead unavoidably to a system
crash or halt.

When booted from the internal CF-Card, all interfaces found on the system
will be automatically initialized in the following way:


Interface      Description
-------------+----------------------------------------------------------------
                The interface will be configured by default via DHCP and will
                additionally get also the APIPA 169.254.19.65/16.
eth0            Because of some particular configurable features, it will be
                referred to as the "primary interface". It can be configured
                to a static address and to offer DHCP addresses (see below).

                The interface will be configured by default with the static IP
eth1            address 192.168.2.1/24. DHCP servicing (Dynamic addresses
                starting from 192.168.2.128 to 192.168.2.254) is available and
                enabled for this interface.

eth2            The interface will be configured via DHCP

In both modes of operation the machine will be reachable via SSH from the
network for the user root with the default password password or the password
specified on the kernel command line (see below).


alix-rescue Command Line Parameters
-----------------------------------

The following options may or must be specified on the kernel command line.

Parameter                         Description
--------------------------------+----------------------------------------------
                                  This option sets the password for the user
password=<password>               root. It can be specified as clear text or
password=SHADOW:<hash value>      as a '/etc/shadow'-compatible hash value if
                                  prefixed with 'SHADOW:'

                                  This option sets the writable device for the
                                  filesystem writes during runtime. It can be
                                  a physical filesystem formatted with ext2,
                                  ext3 or any filesystem supported by the
                                  initial ramdisk or a ramdisk itself (use
                                  /dev/ram). When using the ramdisk, all
branch=<device>                   changes made during the session are lost
                                  after shutting down. It is permitted to
                                  reference the device by device name (e.g.
                                  /dev/sda1), by volume label (e.g.
                                  LABEL=boot-disk) and by unique volume id
                                  (e.g.
                                  UUID=f5a49b89-e8c7-448a-a920-ae0aaac53b7b).
                                  Depending upon the intial ramdisk image,
                                  this parameter may be optional or not. See
                                  below.


                                  This option sets the path and name to the
loop=<root filesystem image file> compressed root filesystem image. Depending
                                  upon the intial ramdisk image, this parameter
                                  may be optional or not. See below.

                                  This option sets the device for the
                                  filesystem where the compressed root
                                  filesystem image is located. It can be a
                                  physical filesystem formatted with ext2,
                                  ext3, any filesystem supported by the initial
                                  ramdisk or an NFS mount (use /dev/nfs to
root=<root fs device>             specify the NFS mount).
                                  It is permitted to reference the device by
                                  device name (e.g. /dev/sda1), by volume label
                                  (e.g. LABEL=boot-disk) and by unique volume
                                  id (e.g.
                                  UUID=f5a49b89-e8c7-448a-a920-ae0aaac53b7b)
                                  Depending upon the intial ramdisk image, this
                                  parameter may be optional or not. See below.

                                  This option tells the kernel to use the
                                  specified tty device for input and output.
                                  This option is mandatory in order to
                                  communicate with the Alix board over the
                                  serial interface. The standard configuration
                                  is console=ttyS0,38400n8 that means: in- and
                                  output over the serial port (ttyS0)
                                  configured to 38400 Baud, 8 Bits, No Parity.
console=<tty>[,<serial config>]   If the option is not specified, the kernel
                                  itself does not send any messages over the
                                  serial interface. If for any reason the boot
                                  process fails and a recovery console is
                                  started from within the initial ramdisk, it
                                  will not be possible neither to get anything
                                  from the serial interface nor to send
                                  anything to the board.

                                  If specified, the primary interface used for
dhcpdif-swap                      manual configuration and dhcp server will be
                                  eth2 and eth0 will be dhcp configured instead

                                  If specified, the DHCP service will be
dhcpdif-enable                    enabled also for the primary interface.
                                  This will work only if the primary interface
                                  is configured with a static ip address.

                                  If specified, the primary interface will be
                                  initialized with the specified IP address or
                                  192.168.1.1 if 'static' was specified. When
dhcpdif-ip=<ip address>|static    specified together with the enabled DHCP
                                  service, the offered scope will be adapted
                                  automatically to the specified address
                                  network.

                                  If specified, the primary interface will be
                                  initialized with the specified IP address
dhcpdif-mask=<ip address mask>    mask instead of 255.255.255.0. When
                                  specified together with the enabled DHCP
                                  service, the offered scope will be adapted
                                  automatically to the specified network mask.



Mandatory Command Line Parameters
---------------------------------

Based on the used initial ramdisk image, some parametrs may be mandatory or
not:

Initial Ramdisk  Required Parameters
----------------+-------------------------------------------------------------
axrescue.initrd  The local initial ramdisk image requires all the root, the
                 loop and the branch parameters.

                 The network initial ramdisk image requires all the 'root', the
axrescue.initrdn loop and the branch parameter. In addition it requires the
                 nfsroot parameter to point to the NFS share containing the
                 compressed root filesystem image and the ip parameter


Samples
=======

All following samples show boot scenarios based on syslinux configuration
files.


Boot from local volume
----------------------

KERNEL  /axrescue.vmlinuz
APPEND  initrd=/axrescue.initrd root=LABEL=bootbank loop=/axrescue.squashfs \
        branch=/dev/ram password="aBRAcadaBRA" console=ttyS0,38400n8

Boots alix-rescue from the device with the volume name bootbank where all
files of alix-rescue are located at the top of the filesystem. All writes
are done in the ramdisk, so there is no persistent storage. The password of
the user root is set to aBRAcadaBRA


Boot from Network with NFS
--------------------------

KERNEL   alix-rescue/axrescue.vmlinuz
APPEND   initrd=alix-rescue/axrescue.initrdn rootdelay=2 root=/dev/nfs \
         nfsroot=10.65.6.1:/mnt/data/tftproot,rw ip=dhcp \
         loop=/yeaboot/alix-rescue/axrescue.squashfs branch=/dev/ram \
         console=ttyS0,38400n8

PXE Boot of alix-rescue via TFTP with the kernel and the initial ramdisk
located in the directory alix-rescue of the TFTP server area. The compressed
root filesystem image is located on the NFS share /mnt/data/tftproot on the
server with the address 10.65.6.1 in the directory /yeaboot/alix-rescue/.
The IP address of the booting computer is assigned by DHCP. All writes are
done in the ramdisk, so there is no persistent storage.

More samples will be included in the distribution archive.


Quick deployment
================

This is a quick and dirty step-by-step instruction for creating a bootable
CF-Card containing alix-rescue. The following assumes that the CF-Card is
connected through a card reader to a computer running a Debian or Ubuntu
Linux with the package syslinux installed.

After connecting the card reader, the filesystem on the card may be mounted
if there is already a partition with a valid filesystem on it. In such a
case, the card must be unmounted by hand.

WARNING

The following commands may break your system if you are not knowing what you
are doing - so please be careful and try to understand what you are doing.
YOU are responsible for what is happening! If you break it, you own both
parts!

In this instruction we assume the CF-Card as /dev/sdX. Please substitute it
with the device name where the card is mapped on your system.

First of all the card must be partitioned:

    sudo fdisk /dev/sdX

Now delete all existing partitions and create a new small primary partition
nr. 1 (it should have at least 256 MB but try to make it bigger since you
will need it in future to store on it also the kernels of your appliance
operating system. Set the boot flag on the partition (command a). You can
verify that the parition is bootable by showing at the parition table (with
the command p):

  Disk /dev/sda: 16.0 GB, 16022200320 bytes
  255 heads, 63 sectors/track, 1947 cylinders
  Units = cylinders of 16065 * 512 = 8225280 bytes
  Sector size (logical/physical): 512 bytes / 512 bytes
  I/O size (minimum/optimal): 512 bytes / 512 bytes
  Disk identifier: 0x00000000

     Device Boot      Start         End      Blocks   Id  System
  /dev/sdX1   *           1          66      530113+  83  Linux

  Command (m for help): 

Save the partition table. Now format it and mount it into your filesystem:

    sudo mke2fs -L boot /dev/sdX1
    sudo mkdir -p /mnt/cfcard
    sudo mount /dev/sdX1 /mnt/cfcard

Now make it bootable by writing a bootable MBR (Master Boot Record) and by
saving a boot loader onto the card:

    sudo cat /usr/lib/syslinux/mbr.bin > /dev/sdX
    sudo mkdir -p /mnt/cfcard/syslinux
    sudo extlinux --install /mnt/cfcard/syslinux

Unpack the alix-rescue files to the boot partition and then unmount:

    sudo tar xvzf alix-rescue-10.04.4-05.tar.gz -C /mnt/cfcard
    sudo unmount /mnt/cfcard
    sudo rmdir /mnt/cfcard

Now you can plug the CF-Card in your Alix board, power it on and enjoy the
show!


Copyright and Legal Stuff
=========================

NetPoldo is a derivative work based on Debian and Ubuntu Linux created by Leo
Moll

Copyright: (C) 2012-2013 Leo Moll <leo.moll@yeasoft.com>

 This package is free software; you can redistribute it and/or modify
 it under the terms of the GNU General Public License as published by
 the Free Software Foundation; either version 2 of the License, or
 (at your option) any later version.

 This package is distributed in the hope that it will be useful,
 but WITHOUT ANY WARRANTY; without even the implied warranty of
 MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 GNU General Public License for more details.  

 You should have received a copy of the GNU General Public License
 along with this program. If not, see <http://www.gnu.org/licenses/>
