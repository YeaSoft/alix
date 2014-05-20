==============================================================================
Release Notes - Alix Rescue and PE System - Version 13              2013-11-19
==============================================================================

* Release Scope
* What's New
* Included Software
* Memory Impact
* Known Problems

Release Scope
-------------
This release features updated binaries for all versions and includes some
undelete tools for ext filesystems. Since the support of Ubuntu hardy has been
discontinued, the release if avaible only for Debian 6 and 7 and Ubuntu 10.04


What's New
----------
 * Binaries updated as of 2013-11-19
 * Added ext undelete tools


Included Software
-----------------

In addition to the minimal system (ubuntu-minimal or equivalent for debian),
the following additional packages are included in this version of alix-rescue:

 * Connectivity and Usability
   + ssh
   + dhcp3-server
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


Memory Usage
------------

The following memory usage values have been determined approximatively one
minute after completion of the boot process based on the selected alix-rescue


Version                 | Local  | Net    
========================+========+========
Debian 7.2-x86          |  71.7M |  60.5M
------------------------+--------+--------
Debian 6.07-x86         |  54.0M |  52.8M
------------------------+--------+--------
Ubuntu 10.04-x86        |  65.6M |  65.2M
------------------------+--------+--------
Ubuntu 8.04-x86         |  57.2M |  46.7M 
------------------------+--------+--------

The following method was used:

  echo $(($(grep MemTotal /proc/meminfo | awk '{print $2}') - \
    $(grep MemFree /proc/meminfo | awk '{print $2}') ))


Known Problems
--------------

None


Release History
---------------

Version 12 released on 2013-04-07

 * Fixed missed modules iso8859-1 and cp437 for booting from vfat partitions

Version 11 released on 2013-04-07

 * Support for Debian Wheezy 7.0.0
 * UIC updated to version 0.15
 * Binaries updated as of 2013-04-07

Version 10 released on 2012-05-23

 * Support for Debian Wheezy 7.0.0
 * UIC updated to version 0.15

Version 09 released on 2012-05-21

 * The primary interface now starts in DHCP mode by default
 * Feature: Increased security by adding the possibility to configure the root
   password by specifying a 'shadow'-hash on the kernel command line.

Version 07 released on 2012-05-11

  * Feature: Debian squeeze based version
  * Feature: commandline switch dhcpdif-swap to swap functionality between
    eth0 and eth2
  * Fixed: dhcpdif-ip=dhcp is now working

Version 05 released on 2012-05-01

  * First public release