---
title: How to configure a static IP address on Raspberry Pi
date: 2023-02-15 05:00:00 -0700
categories: [Raspberry Pi,DevOps]
tags: [devops,pi,network]
---

## How to configure a static IP address on Raspberry Pi

SSH into raspberry pi and type the following command to edit the IP configs:

```shell
sudo nano /etc/dhcpcd.conf
```
{: .nolineno }

Scroll down to the `Example static IP configuration:` section See the sample config below:

```text
# Example static IP configuration:
#interface eth0
#static ip_address=192.168.0.10/24
#static ip6_address=fd51:42f8:caae:d92e::ff/64
#static routers=192.168.0.1
#static domain_name_servers=192.168.0.1 8.8.8.8 fd51:42f8:caae:d92e::1
```

Uncomment and update the `interface` line and the following `static ip_address` lines:

```text
# Example Static IP configuration:
interface eth0
static ip_address=192.168.0.11/24
```
{: .nolineno }

Keep the `ip6_address` line commented and uncomment the following `static routers` line and set the proper IP address.

```text
#static ip6_address=fd51:42f8:caae:d92e::ff/64
static routers=192.168.0.1
```
{: .nolineno }

Uncomment the `static domain_name_servers` line and update the primary and the secondary DNS's IP addresses:

```text
static domain_name_servers=192.168.0.1 1.1.1.1
```
{: .nolineno }

Here is the completed and updated `Example static IP configuration` section:

```text
# Example static IP configuration:
interface eth0
static ip_address=192.168.0.11/24
#static ip6_address=fd51:42f8:caae:d92e::ff/64
static routers=192.168.0.1
static domain_name_servers=192.168.0.1 1.1.1.1
```

Save the changes.
