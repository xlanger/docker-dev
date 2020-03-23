`docker-compose.yml` 参考：
```yml
version: '3'
services:
    php:
        hostname: php
        container_name: php
        image: xphp:7.4-fpm-alpine
        command: php-fpm -F
        volumes:
            - ~/docker/php/php-fpm.conf:/usr/local/etc/php-fpm.conf
            - ~/docker/php/php-fpm.d:/usr/local/etc/php-fpm.d
            - ~/docker/php/php.ini:/usr/local/etc/php/php.ini
            - ~/docker/log/php:/var/log/php
            - ~/workspace:/wwwdir
        networks:
            dev-net:
                ipv4_address: 172.16.10.10
        extra_hosts:
            - mysql:172.16.10.11
            - redis:172.16.10.12
            - memcached:172.16.10.13
    mysql:
        hostname: mysql
        container_name: mysql
        image: xmysql:8.0
        command: mysqld
        #environment:
        #    - MYSQL_ROOT_PASSWORD=rootpwd
        volumes:
            - ~/docker/mysql/conf.d:/etc/mysql/conf.d
            - ~/docker/data/mysql:/var/lib/mysql
            - ~/docker/log/mysql:/var/log/mysql
        networks:
            dev-net:
                ipv4_address: 172.16.10.11
    redis:
        hostname: redis
        container_name: redis
        image: xredis:5.0-alpine
        command: redis-server /etc/redis.conf
        volumes:
            - ~/docker/redis/redis.conf:/etc/redis.conf
            - ~/docker/data/redis:/var/lib/redis
            - ~/docker/log/redis:/var/log/redis
        networks:
            dev-net:
                ipv4_address: 172.16.10.12
    memcached:
        hostname: memcached
        container_name: memcached
        image: xmemcached:1.6-alpine
        command: memcached -u memcache -vv
        networks:
            dev-net:
                ipv4_address: 172.16.10.13
    nginx:
        hostname: nginx
        container_name: nginx
        image: xnginx:1.16-alpine
        command: nginx -g 'daemon off;'
        volumes:
            - ~/docker/nginx:/etc/nginx
            - ~/docker/log/nginx:/var/log/nginx
            - ~/workspace:/wwwdir
        ports:
            - 80:80
        networks:
            dev-net:
                ipv4_address: 172.16.10.20
        extra_hosts:
            - phpfpm:172.16.10.10
networks:
  dev-net:
    driver: bridge
    ipam:
      driver: default
      config:
          - subnet: 172.16.10.0/24
```