---
title: Raspberry Pi Servers Monitoring
date: 2022-10-23 07:00:00 -0700
categories: [DevOps, Raspberry Pi]
tags: [devops,pi,grafana,prometheus,cadvisor,node_exporter]
---

> Work in progress
{: .prompt-info }

## Raspberry Pi Servers Monitoring

How to install and configure tools for monitoring Raspberry Pis with Ubuntu OS.

> These instructions are tested on Raspberry Pi 4B 4GB and 8GB. OS type: Ubuntu 20.04 and 22.04.
{: .prompt-info }

### Prerequisites

To make all panels work as intended we need to enable `c-groups` by modifying `cmdline.txt` configuration file.
Execute the following command to open the file in the `nano` text editor:

```shell
sudo nano /boot/firmware/cmdline.txt
```
{: .nolineno }

Next, add the following to the beginning of the file:

```text
cgroup_enable=memory cgroup_memory=1
```
{: .nolineno }

Now save the file and reboot the machine.

```sh
sudo reboot
```
{: .nolineno }

When system will boot, execute the following command to verify the result:

```shell
cat /proc/cgroups
```
{: .nolineno }

The command that is specified above, should yield to a screen the information similar on the following screenshot:

![img-description](/assets/img/posts/2022-10-23/cat-proc-cgroups.PNG)
_/proc/cgroups_


Now we need to create a couple of directories. Execute the following script to create the necessary folder structure:

```shell
#!/bin/bash

function error {
  echo -e "\\e[91m$1\\e[39m"
  exit 1
}

function check_internet() {
  printf "Checking if you are online..."
  wget -q --spider http://github.com
  if [ $? -eq 0 ]; then
    echo "Online. Continuing."
  else
    error "Offline. Go connect to the internet then run the script again."
  fi
}

check_internet

echo "Creating directories..."
sudo mkdir -p /portainer/Files/AppData/Config/prometheus/config || error "Failed to create config directory!"
sudo mkdir -p /portainer/Files/AppData/Config/prometheus/data || error "Failed to create data directory for Prometheus!"
sudo mkdir -p /portainer/Files/AppData/Config/grafana/data || error "Failed to create data directory for Grafana!"
echo "Download Prometheus config files if they don't exist"
if [ -d /portainer/Files/AppData/Config/prometheus/config/prometheus.yml ];
then
    echo "/portainer/Files/AppData/Config/prometheus/config/prometheus.yml is a directory removing"
    rm -rf '/portainer/Files/AppData/Config/prometheus/config/prometheus.yml/'
fi

if [ ! -f /portainer/Files/AppData/Config/prometheus/config/prometheus.yml ];
then
        sudo wget -O /portainer/Files/AppData/Config/prometheus/config/prometheus.yml https://raw.githubusercontent.com/oijkn/Docker-Raspberry-PI-Monitoring/main/prometheus/prometheus.yml || error "Failed to download prometheus.yml file!"
fi

if [ -d /portainer/Files/AppData/Config/grafana/grafana.ini ];
then
    echo "/portainer/Files/AppData/Config/grafana/grafana.ini is a directory removing"
    rm -rf '/portainer/Files/AppData/Config/grafana/grafana.ini'
fi
sudo touch /portainer/Files/AppData/Config/grafana/grafana.ini || error "Failed to touch grafana.ini file!"
echo "Setting up the permissions..."
sudo chown -R 472:472 /portainer/Files/AppData/Config/grafana/data || error "Failed to set permissions for Grafana data!"
echo "Done"

```
The bash script that is specified above will create the necessary directory structure for **_Prometheus_** and **_Grafana_**.

## Installation

> Work in progress
{: .prompt-info }
