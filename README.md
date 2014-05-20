The Alix Software Project
=========================

![Alix Board Banner](http://www.yeasoft.com/assets/images/articles/alix-board-banner.jpg)

[PC-Engines™ Alix-Boards](http://www.pcengines.ch/) are x86 compatible embedded
systems. Due to the very low power consumpion (about 4-8 Watt) they are very
suitable to be used as routers, NAS or for many other purposes. They are
available in [several flavours](http://www.pcengines.ch/alix.htm) and so they
have been adopted in many products
([see examples](http://www.pcengines.ch/alixcust.htm)).

Project Scope
-------------

The Alix-Systems produced by PC-Engines can be roughly categorized in two
groups: PC-like systems with one network adapter and VGA connector (Alix.1D,
Alix.3C3 and Alix.3D3) and embedded systems with up to three network adapters
and serial interface (Alix.2 Series and several models from the Alix.3 series).

This repository contains all software components (image templates, scripts,
Debian and Ubuntu package sources) referenced in the
[related article series](http://www.yeasoft.com/site/projects:alix).

Components of the Project
-------------------------

### Part 1: The Rescue and Preinstallation Environment ###

Every system needs a starting point, a simple way to make it work on the fly
without any complications. Let's say: a live-system usable as a rescue system
when the primary system is damaged or broken as well as a preinstallation
environment for initially setting up your device. It should be compact, simple
to deploy but it should also contain everything you need. It should be bootable
from a local storage device and from the network. Enjoy
[alix-rescue](http://www.yeasoft.com/site/projects:alix-rescue:doc), a
life-system created with [UIC](https://github.com/YeaSoft/uic) and available in
Debian and Ubuntu flavours.
