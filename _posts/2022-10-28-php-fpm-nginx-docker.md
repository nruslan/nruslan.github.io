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

Now we need to apply some changes to the files that we've extracted from the official PHP-FPM container.
In the `www.conf` we need to specify the address on which to accept FastCGI requests. It listens to port 9000 by default,
but we are going to change that to unix socket. See the example below:

```ini
listen = /var/run/php/php8.1-fpm.sock
```
Uncomment the following three lines to set the permissions for unix socket. Read/write permissions must be set in order to allow connections from a web server.
The owner and group can be specified either by name or by their numeric IDs:

```ini
listen.owner = www-data
listen.group = www-data
listen.mode = 0660
```

Next we are going to modify `zz-docker.conf` file and set the listen address to the same unix socket path that we've used in `www.conf` file:

```ini
listen = /var/run/php/php8.1-fpm.sock
```

Create the `entrypoint.sh` file for our docker container with the following content:

```shell
#!/usr/bin/env bash
service nginx start
php-fpm
```

### Dockerfile

Our PHP-FPM/NGINX container is going to be build from `php:8.1.11-fpm` image. See the sample Dockerfile below:

```dockerfile
FROM php:8.1.11-fpm
RUN apt-get update \
    && apt-get install -y nginx

# Add php files into container
COPY ./src /var/www/html

COPY nginx/default.conf /etc/nginx/sites-available/default
COPY php/www.conf /usr/local/etc/php-fpm.d/www.conf
COPY php/zz-docker.conf /usr/local/etc/php-fpm.d/zz-docker.conf
#COPY php.ini /usr/local/etc/php/php.ini

COPY entrypoint.sh /etc/entrypoint.sh
RUN chmod +x /etc/entrypoint.sh
WORKDIR /var/www/html
ENTRYPOINT ["/etc/entrypoint.sh"]

```

### Compose

Docker-compose file structure:

```yaml
# https://docs.docker.com/compose/compose-file/

# Containers
services:

  # Web App Container
  app:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: specify_container_name
    restart: unless-stopped
    ports:
      - "8080:80"
    volumes:
      - ./src:/var/www/html
      - ./php/socket:/var/run/php
    tty: true
```

Execute the following command to start the container:

```shell
docker compose up -d
```
{: .nolineno }
