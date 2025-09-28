---
title: Proxmox Email Notification Configuration
description: How to configure email notifications on Proxmox
date: 2025-09-28 05:00:00 -0500
categories: [DevOps, Proxmox]
tags: [devops, proxmox, email]
image:
  path: /assets/img/posts/2025-09-28/proxmox-email-notifications.jpg
  alt: Proxmox email notifications
---

Install the necessary packages by executing the command listed below:

```shell
apt install libsasl2-modules -y
```
Next, we need to edit the `posfix` configuration file:

```shell
sudo nano /etc/postfix/main.cf
```
Comment out `mydestination` and `relayhost` lines by adding the `#` at the beginning of each line.
It should look similar to this:

```shell
#mydestination = $myhostname, localhost.$mydomain, localhost
#relayhost =
```
Add the following to the end of the file:
```shell
# Email notification custom settings
relayhost = smtp.mailgun.org:587
smtp_use_tls = yes
smtp_sasl_auth_enable = yes
smtp_sasl_security_options =
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_tls_CAfile = /etc/ssl/certs/Entrust_Root_Certification_Authority.pem
```
Save the changes and close the file by pressing CTRL+X, Y and Enter.

Create the `sasl_passwd` file by executing the following command:
```shell
nano /etc/postfix/sasl_passwd
```
Add the following line to the file:

```text
smtp.mailgun.org:587 <%mail-username%>:<%mail-password%>
```
Save the changes and close the file by pressing CTRL+X, Y and Enter.

Now, we need to update the postfix lookup table by executing the following command:

```shell
postmap hash:/etc/postfix/sasl_passwd
```
Then limit the access to the `sasl_passwd` file to root user only by executing the following:

```shell
chmod 600 /etc/postfix/sasl_passwd
```
Then we need to restart `postfix` service:

```shell
systemctl restart postfix
```
To test all our changes we can execute the following command

```shell
echo "Test email from Proxmox: $(hostname)" | mail -s "Proxmox Testing" <%receving-email-address%>
```
