
[![Docker Pulls](https://img.shields.io/docker/pulls/mdsami/docker-phpfpm.svg)](https://hub.docker.com/r/mdsami/docker-phpfpm/)

PHP-FPM Docker container image 
Table of Contents
-------------------

 * [Installation](#installation)
 * [Quick Start](#quick-start)
 * [Adding PHP-extension](#adding-php-extension) 
 * [Logging](#logging)
 * [Out of the box](#out-of-the-box)

Installation
-------------------

 * [Install Docker](https://docs.docker.com/installation/) or [askubuntu](http://askubuntu.com/a/473720)
 * Pull the latest version of the image.
 
```bash
docker pull mdsami/docker-phpfpm
```

or other versions (7.2, 7.1, 7.0, 5.6, 5.5, 5.4 or 5.3):

```bash
docker pull mdsami/docker-phpfpm:5.5
```

Alternately you can build the image yourself.

```bash
git clone https://github.com/mdsami/docker-phpfpm.git
cd docker-phpfpm
docker build -t="$USER/docker-phpfpm" .
```

Quick Start
-------------------

Run the application container:

```bash
docker run --name app -d -p 9000:9000 mdsami/docker-phpfpm
```

The simplest way to login to the app container is to use the `docker exec` command to attach a new process to the running container.

```bash
docker exec -it app bash
```

Adding PHP-extension
-------------------

You can use one of two choices to install the required php-extensions:

1. `docker exec -it app bash -c 'apt-get update && apt-get install php-mongo && rm -rf /var/lib/apt/lists/*'`

2. Create your container on based the current. Сontents Dockerfile:

```
FROM mdsami/docker-phpfpm:5.6

RUN apt-get update \
    && apt-get install -y php-mongo \
    && rm -rf /var/lib/apt/lists/* 

WORKDIR /var/www/app/

EXPOSE 9000

CMD ["/usr/sbin/php-fpm5.6"]
```

Next step,

```bash
docker build -t php-5.6 .
docker run --name app -d -p 9090:9000 php-5.6
```

>See installed php-extension: `docker exec -it app php -m`

>PHP-extension "Mcrypt" was REMOVED in PHP 7.2. Use [Sodium](http://php.net/manual/en/book.sodium.php) or [OpenSSL](http://php.net/manual/en/book.openssl.php)

Logging
-------------------

All the logs are forwarded to stdout and sterr. You have use the command `docker logs`.

```bash
docker logs app
```

####Split the logs

You can then simply split the stdout & stderr of the container by piping the separate streams and send them to files:

```bash
docker logs app > stdout.log 2>stderr.log
cat stdout.log
cat stderr.log
```

or split stdout and error to host stdout:

```bash
docker logs app > -
docker logs app 2> -
```

####Rotate logs

Create the file `/etc/logrotate.d/docker-containers` with the following text inside:

```
/var/lib/docker/containers/*/*.log {
    rotate 31
    daily
    nocompress
    missingok
    notifempty
    copytruncate
}
```
> Optionally, you can replace `nocompress` to `compress` and change the number of days.

Out of the box
-------------------
 * Ubuntu 12.04, 14.04 or 16.04 LTS
 * PHP 5.3, 5.4, 5.5, 5.6, 7.0, 7.1 or 7.2
 * Composer (package manager)

>Environment depends on the version of PHP.

License
-------------------

PHP-FPM docker image is open-sourced software licensed under the [MIT license](http://opensource.org/licenses/MIT)
