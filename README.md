# 配置开发环境

### 创建项目挂载环境
```bash
docker create --name wwwdir \
	-v ~/workspace/:/wwwdir \
	alpine:latest /bin/sh
```

### 运行 `memcached` 容器
```bash
docker run --name memcached \
	-h memcached \
	-i -t -d my-memcached:1.4.34-alpine \
	memcached -u memcache -vv
```

### 运行 `redis` 容器
```bash
docker run --name redis -h redis \
	-v ~/docker/redis/redis.conf:/etc/redis.conf \
	-v ~/docker/data/redis:/var/lib/redis \
	-v ~/docker/log/redis:/var/log/redis \
	-i -t -d my-redis:3.0.7-alpine \
	/bin/sh -c "redis-server /etc/redis.conf && while true; do sleep 1000; done"
```

### 运行 `mysql` 容器
```bash
docker run --name mysql -h mysql \
	-v ~/docker/mysql/conf.d:/etc/mysql/conf.d \
	-v ~/docker/data/mysql:/var/lib/mysql \
	-v ~/docker/log/mysql:/var/log/mysql \
	-i -t -d my-mysql:5.7.17 \
	mysqld
```

### 运行 `php` 容器
```bash
docker run --name php -h phpfpm \
	-v ~/docker/php/php-fpm.conf:/usr/local/etc/php-fpm.d/www.conf \
	-v ~/docker/php/php.ini:/usr/local/etc/php/php.ini \
	-v ~/docker/log/php:/var/log/php \
	-v ~/proj:/proj \
	--volumes-from wwwdir \
	--link redis:redis \
	--link memcached:memcached \
	-i -t -d my-php:5.6.30-alpine \
	php-fpm -F
```

### 运行 `nginx` 容器
```bash
docker run --name nginx -h nginx \
	-v ~/docker/nginx:/etc/nginx \
	-v ~/docker/log/nginx:/var/log/nginx \
	-v ~/proj:/proj \
	--volumes-from wwwdir \
	--link php:phpfpm \
	-p 80:80 -p 443:443 \
	-i -t -d my-nginx:1.11.10 \
	/bin/bash -c "/usr/sbin/nginx -g 'daemon off;'"
```
