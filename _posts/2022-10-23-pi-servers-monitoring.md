---
title: Raspberry Pi servers monitoring
date: 2022-10-23 07:00:00 -0700
categories: [DevOps, Raspberry Pi]
tags: [devops,pi,graphana,prometheus,cadvisor,node_exporter]
---

> Work in progress
{: .prompt-tip }
## Raspberry Pi servers monitoring


### prerequisites

First of all to make all panels work as intended We need to enable c-groups so the stack will work out of the box. To do this you need to modify the configuration file cmdline.txt

```shell
sudo nano /boot/firmware/cmdline.txt
```

Add the following to the beggining of the file:

```txt
cgroup_enable=memory cgroup_memory=1
```

Now save the file and reboot the machine.

```sh
sudo reboot
```

When system will boot, execute the following command to verify the result:

```sh
cat /proc/cgroups
```

the command that is specified above, should yield to a screen the information similar to the following image:
[past the imag]

Now we need to create a couple of directories. One fore graphana and for prometheus.
