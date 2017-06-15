# Build a php image base on afficial image

从PHP官方镜像安装扩展非常简单，它提供了几个工具命令，只是安装扩展前需要手动安装相应的依赖。

在安装内核提供的扩展时，用工具 ` docker-php-ext-install` 安装需要的扩展，如：`docker-php-ext-install -j$(nproc) mcrypt`；用工具命令 `docker-php-ext-configure` 自定义配置安装扩展时的配置参数，如：
<!--more-->
```
docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/
```

下面是`install`可用扩展名的选项：
```
usage: /usr/local/bin/docker-php-ext-install [-jN] ext-name [ext-name ...]
   ie: /usr/local/bin/docker-php-ext-install gd mysqli
       /usr/local/bin/docker-php-ext-install pdo pdo_mysql
       /usr/local/bin/docker-php-ext-install -j5 gd mbstring mysqli pdo pdo_mysql shmop

if custom ./configure arguments are necessary, see docker-php-ext-configure

Possible values for ext-name:
bcmath bz2 calendar ctype curl dba dom enchant exif fileinfo filter ftp gd gettext gmp hash iconv imap interbase intl json ldap mbstring mcrypt mssql mysql mysqli oci8 odbc opcache pcntl pdo pdo_dblib pdo_firebird pdo_mysql pdo_oci pdo_odbc pdo_pgsql pdo_sqlite pgsql phar posix pspell readline recode reflection session shmop simplexml snmp soap sockets spl standard sybase_ct sysvmsg sysvsem sysvshm tidy tokenizer wddx xml xmlreader xmlrpc xmlwriter xsl zip
```

在安装PECL扩展时,  先使用`pecl install memcached-2.2.0`下载编译并安装，然后用工具 `docker-php-ext-enable memcached` 便可以将 `extension=memcached.so`加入php配置文件中，如：
```bash
$ docker run --rm -it php:5.6.30 /bin/bash
$ ls /usr/local/etc/php/conf.d
docker-php-ext-bcmath.ini     docker-php-ext-memcached.ini  docker-php-ext-redis.ini
docker-php-ext-gd.ini         docker-php-ext-mysqli.ini     docker-php-ext-soap.ini
docker-php-ext-gettext.ini    docker-php-ext-opcache.ini    docker-php-ext-sockets.ini
docker-php-ext-mcrypt.ini     docker-php-ext-pdo_mysql.ini  docker-php-ext-xdebug.ini
$ cat /usr/local/etc/php/conf.d/docker-php-ext-memcached.ini
extension=memcached.so
```

Dockerfile（debian jessie）文件：
```
FROM php:5.6.30-fpm

RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo "Asia/Shanghai" > /etc/timezone
    
RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng12-dev \
        libmemcached-dev \
        zlib1g-dev \
        libcurl4-openssl-dev \
        libxml2-dev \
        --no-install-recommends && rm -rf /var/lib/apt/lists/* \
    && docker-php-ext-install -j$(nproc) iconv mcrypt gettext curl mysqli pdo pdo_mysql \
        mbstring bcmath opcache xml simplexml sockets hash soap \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd
    
RUN pecl install redis-3.1.0 \
    && pecl install memcached-2.2.0 \
    && pecl install xdebug-2.5.0 \
    && docker-php-ext-enable redis memcached xdebug
    
CMD ["php-fpm", "-F"]
```

Dockerfile（alpine linux）文件：
```
FROM php:5.6.30-fpm-alpine

RUN apk add --no-cache --virtual .build-deps \
        autoconf \
        file \
        gcc \
        g++ \
        libc-dev \
        make \
        pkgconf \
        re2c \ 
        tzdata \
    && apk add --no-cache --virtual .run-deps \
        coreutils \
        libltdl \
        freetype-dev \
        gettext-dev \
        libjpeg-turbo-dev \
        libpng-dev \
        curl-dev \
        libmcrypt-dev \
        libxml2-dev \
        cyrus-sasl-dev \
        libmemcached-dev \
	&& rm -rf /var/lib/apt/lists/* \
    && docker-php-ext-install -j$(nproc) \
        iconv mcrypt gettext curl mysqli pdo pdo_mysql zip \
        mbstring bcmath opcache xml simplexml sockets hash soap \
    && docker-php-ext-configure gd \
        --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \
    && pecl install redis-3.1.0 \
    && pecl install memcached-2.2.0 \
    && pecl install xdebug-2.5.0 \
    && docker-php-ext-enable redis memcached xdebug \
    && cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo "Asia/Shanghai" >  /etc/timezone \
    && apk del .build-deps

CMD ["php-fpm", "-F"]
```

Blog地址：https://xlange.com/post/dockerfile-baseon-official-php-image.html
