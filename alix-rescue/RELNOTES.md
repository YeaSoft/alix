Alix Rescue and PE System - Release Notes
=========================================

!!! WORK IN PROGRESS !!!
========================

Release Scope
-------------

This release features updated binaries for all versions and includes some
undelete tools for ext filesystems. Since the support of Ubuntu hardy has been
discontinued, the release if avaible only for Debian 6 and 7 and Ubuntu 10.04

As a premiere, this release includes the first edition of alix-rescue based on
for Debian 8 Beta (jessie) and Ubuntu 12.04 (precise). Since the repositories
of Ubuntu 8.04 (hardy) are now offline, the corresponding version of
__alix-rescue__ will not be updated any more.



What's New in Version 13 (2014-??-??)
----------
 * UIC updated to version 0.16.6
 * Binaries updated as of 2014-??-??
 * New software packages
   - rsync
   - partimage
   - dosfstools and mtools
   - syslinux (including extlinux binary)
   - extundelete (not included in alix-rescue-1004 and alix-rescue-deb6)
   - recover (not included in alix-rescue-deb8)
   - e2undel (not included in alix-rescue-deb8)
 * New flavour based on Debian Jessie 8 Beta
 * New flavour based on Ubuntu Precise 12.04
 * New command line switch `settimezone=<AREA>/<ZONE>` allows to configure the
   time zone of the system

Included Software
-----------------

In addition to the minimal system (`ubuntu-minimal` or equivalent for Debian),
the following additional packages are included in this version of
__alix-rescue__:

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
   - extundelete (not included in alix-rescue-1004 and alix-rescue-deb6)
   - recover (not included in alix-rescue-deb8)
   - e2undel (not included in alix-rescue-deb8)

 * __Installation and deployment tools__
   - partimage
   - uic


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


Memory Usage
------------

The following memory usage values have been determined approximatively one
minute after completion of the boot process based on the selected NetPoldo
image:


````
  Version              | Local  | Net
 ----------------------|--------|--------
  Debian 8             |  M | M
  Debian 7             |  M | M
  Debian 6             |  M | M
  Ubuntu 12.04         |  M | M
  Ubuntu 10.04         |  M | M
````

The following method was used:

````
  echo $(($(grep MemTotal /proc/meminfo | awk '{print $2}') - \
    $(grep MemFree /proc/meminfo | awk '{print $2}') ))
````

Known Problems
--------------

At the time of this release there are no known issues with the currently
shipped configurations.


Release History
---------------

### Version 12 released on 2013-04-07 ###

 * Fixed missed modules iso8859-1 and cp437 for booting from vfat partitions

### Version 11 released on 2013-04-07 ###

 * Support for Debian Wheezy 7.0.0
 * UIC updated to version 0.15
 * Binaries updated as of 2013-04-07

### Version 10 released on 2012-05-23 ###

 * Support for Debian Wheezy 7.0.0
 * UIC updated to version 0.15

### Version 09 released on 2012-05-21 ###

 * The primary interface now starts in DHCP mode by default
 * Feature: Increased security by adding the possibility to configure the root
   password by specifying a 'shadow'-hash on the kernel command line.

### Version 07 released on 2012-05-11 ###

  * Feature: Debian squeeze based version
  * Feature: commandline switch dhcpdif-swap to swap functionality between
    eth0 and eth2
  * Fixed: dhcpdif-ip=dhcp is now working

### Version 05 released on 2012-05-01 ###

  * First public release
