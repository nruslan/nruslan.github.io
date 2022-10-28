---
title: PHP-FPM with NGINX in Docker container
date: 2022-10-28 05:00:00 -0700
categories: [DevOps, Docker]
tags: [devops,docker,php,nginx]
---

## PHP-FPM with NGINX in Docker container

How to set up the Docker container with PHP, PHP-FPM and connect PHP-FPM to NGINX via unix socket.

The official PHP-FPM image is configured to communicate with NGINX via `tcp` port by default. To change that we will need to modify a couple of configuration files.

### Prerequisites

First we need to spin up the official php container, so we can get all the necessary php configuration files. Execute the following command to start the container:

```shell
docker run --name phpfpm -d php:8.1.11-fpm
```
{: .nolineno }

Execute the following command to copy `zz-docker.conf` files from the container to your machine:

```shell
docker cp phpfpm:/usr/local/etc/php-fpm.d/zz-docker.conf .
```
{: .nolineno }

The following command will copy the `www.conf` file to your machine:

```shell
docker cp phpfpm:/usr/local/etc/php-fpm.d/www.conf .
```
{: .nolineno }

Most likely you will need to make some changes in the `php.ini` file. There are two `php.ini` files in the container.

+ `php.ini-development`
+ `php.ini-production`

Just copy one of them using the command specified below. Rename the file to `php.ini` and make the changes in according to your application needs.
```shell
docker cp phpfpm:/usr/local/etc/php/php.ini-production .
```
{: .nolineno }

### Configs

Our `Dockerfile` is going to be build from `php:8.1.11-fpm` version:

```dockerfile
FROM php:8.1.11-fpm
RUN apt-get update \
    && apt-get install -y nginx

# Add files into container
COPY ./src /var/www/html

COPY default.conf /etc/nginx/sites-available/default
COPY php/www.conf /usr/local/etc/php-fpm.d/www.conf
COPY php/zz-docker.conf /usr/local/etc/php-fpm.d/zz-docker.conf
#COPY php.ini /usr/local/etc/php/php.ini

COPY entrypoint.sh /etc/entrypoint.sh
RUN chmod +x /etc/entrypoint.sh
WORKDIR /var/www/html
ENTRYPOINT ["/etc/entrypoint.sh"]
```

> Work in progress
{: .prompt-info }
