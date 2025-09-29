---
title: Intel CPU microcode update
description: How to install/update intel-microcode on Proxmox
date: 2025-06-06 05:00:00 -0500
categories: [DevOps, Proxmox]
tags: [devops, proxmox]
image:
  path: /assets/img/posts/2025-06-06/intel-cpu-microcode.jpg
  alt: Intel CPU microcode
---

Microcode updates are intended to fix found security vulnerabilities and other serious CPU bugs.

To get the current running microcode revision for comparison or debugging purposes go to Proxmox console and
execute the following command:

```shell
grep microcode /proc/cpuinfo | uniq
```
In order to install/update `intel-microcode` we have to ensure that the `non-free-firmware`component is enabled 
in the apt sources.

To update the `sources.list` file execute the following command:

```shell
nano /etc/apt/sources.list
```
then append `non-free-firmware` to the end of each line. See the sample screenshot below:

![img-description](/assets/img/posts/2025-06-06/screenshot.jpg)
_sources.list_

Now you can save the changes and close the text editor.

The next step is to execute `apt update` command and then:

```shell
apt install intel-microcode
```
The last step is to reboot the Proxmox host and you are done.

Check the detailed article about [firmware updates](https://pve.proxmox.com/wiki/Firmware_Updates) on Proxmox.
