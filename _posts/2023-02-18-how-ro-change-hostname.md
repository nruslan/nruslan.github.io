---
title: How to Change Hostname on Debian/Ubuntu Linux OS
date: 2023-02-18 05:00:00 -0700
categories: [DevOps, Linux]
tags: [devops,linux,ubuntu,debian]
---

## How to Change Hostname on Debian/Ubuntu Linux OS

Type the following command to edit the **hostname** (your PC's name) in the `hostname` file:

```shell
sudo nano /etc/hostname
```
{: .nolineno }

To save the changes press `ctrl` + `x` keys, then `y` key and then `enter` key.

Next, type the following command to edit the `hosts` file:

```shell
sudo nano /etc/hosts
```
{: .nolineno }

Replace any occurrence of the old computer name with the new one. Save the changes.

Execute the following command to reboot the system:

```shell
sudo reboot
```
{: .nolineno }
