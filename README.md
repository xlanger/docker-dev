# 开发环境适用的Dockerfile

`docker-compose.yml` 参考：
```ymlversion: '3'
services:
    php:
        hostname: php
        container_name: php
        image: xphp:7.2.7-fpm-alpine
        command: php-fpm -F
        volumes:
            - ~/Docker/php/php-fpm.conf:/usr/local/etc/php-fpm.conf
            - ~/Docker/php/php-fpm.d:/usr/local/etc/php-fpm.d
            - ~/Docker/php/php.ini:/usr/local/etc/php/php.ini
            - ~/Docker/log/php:/var/log/php
            - ~/Workspace:/wwwdir
        networks:
            devnet:
                ipv4_address: 172.28.0.2
                ipv6_address: 2001:3984:3989::02
        extra_hosts:
            - "mysql:172.28.0.5"
            - "memcached:172.28.0.6"
            - "redis:172.28.0.7"
            - "mongo:172.28.0.9"
    php5:
        hostname: php5
        container_name: php5
        image: xphp:5.6.36-fpm-alpine
        command: php-fpm -F
        volumes:
            - ~/Docker/php5/php-fpm.conf:/usr/local/etc/php-fpm.conf
            - ~/Docker/php5/php-fpm.d:/usr/local/etc/php-fpm.d
            - ~/Docker/php5/php.ini:/usr/local/etc/php/php.ini
            - ~/Docker/log/php5:/var/log/php
            - ~/Workspace:/wwwdir
        networks:
            devnet:
                ipv4_address: 172.28.0.3
                ipv6_address: 2001:3984:3989::03
        extra_hosts:
            - "mysql:172.28.0.5"
            - "memcached:172.28.0.6"
            - "redis:172.28.0.7"
            - "mongo:172.28.0.9"
            - "bukeadminapi.x.com:172.28.0.4"
            - "bukeadminapi.x.com:2001:3984:3989::04"
            - "gitlab.x.com:172.28.0.4"
            - "gitlab.x.com:2001:3984:3989::04"
    nginx:
        hostname: nginx
        container_name: nginx
        image: xnginx:1.14.0-alpine
        command: nginx -g 'daemon off;'
        volumes:
            - ~/Docker/nginx:/etc/nginx
            - ~/Docker/log/nginx:/var/log/nginx
            - ~/Workspace:/wwwdir
        ports:
            - "80:80"
            - "443:443"
        networks:
            devnet:
                ipv4_address: 172.28.0.4
                ipv6_address: 2001:3984:3989::04
        extra_hosts:
            - "fpm-php:172.28.0.2"
            - "fpm-php5:172.28.0.3"
            - "gitlab:172.28.0.10"
    mysql:
        hostname: mysql
        container_name: mysql
        image: xmysql:5.7.22
        command: mysqld --explicit_defaults_for_timestamp=true --lower_case_table_names=2 --innodb_use_native_aio=0
        #environment:
        #    - MYSQL_ROOT_PASSWORD=rootpwd
        ports:
            - "3306:3306"
        volumes:
            - ~/Docker/mysql/conf.d:/etc/mysql/conf.d
            - ~/Docker/data/mysql:/var/lib/mysql
            - ~/Docker/log/mysql:/var/log/mysql
        networks:
            devnet:
                ipv4_address: 172.28.0.5
                ipv6_address: 2001:3984:3989::05
    memcached:
        hostname: memcached
        container_name: memcached
        image: xmemcached:1.5.8-alpine
        command: memcached -u memcache -vv
        networks:
            devnet:
                ipv4_address: 172.28.0.6
                ipv6_address: 2001:3984:3989::06
    redis:
        hostname: redis
        container_name: redis
        image: xredis:4.0.10-alpine
        command: redis-server /etc/redis.conf
        volumes:
            - ~/Docker/redis/redis.conf:/etc/redis.conf
            - ~/Docker/data/redis:/var/lib/redis
            - ~/Docker/log/redis:/var/log/redis
        networks:
            devnet:
                ipv4_address: 172.28.0.7
                ipv6_address: 2001:3984:3989::07
    mongo:
        hostname: mongo
        container_name: mongo
        restart: always
        image: xmongo:4.0.0-xenial
        command: mongod #-f /etc/mongod.conf
        #environment:
        #    - MONGO_INITDB_ROOT_USERNAME=root
        #    - MONGO_INITDB_ROOT_PASSWORD=rootpwd
        ports:
            - "27017:27017"
        volumes:
            - mongo-data:/data/db
            - ~/Docker/bak/test:/root/mongo/test
            #- ~/Docker/mongo/mongod.conf:/etc/mongod.conf
        networks:
            devnet:
                ipv4_address: 172.28.0.9
                ipv6_address: 2001:3984:3989::09
    gitlab:
        hostname: gitlab
        container_name: gitlab
        image: gitlab/gitlab-ce:latest
        restart: always
        privileged: true
        environment:
            TZ: 'Asia/Shanghai'
            GITLAB_OMNIBUS_CONFIG: |
                external_url 'http://gitlab.x.com'
                gitlab_rails['time_zone'] = 'Asia/Shanghai'
                gitlab_rails['gitlab_shell_ssh_port'] = 20022
                gitlab_rails['initial_root_password'] = File.read('/run/secrets/gitlab_root_password')
        ports:
            - '20022:22'
        volumes:
            - ~/Docker/gitlab/config:/etc/gitlab
            - ~/Docker/gitlab/data:/var/opt/gitlab
            - ~/Docker/gitlab/logs:/var/log/gitlab
            - ~/Docker/gitlab/gitlab_root_password:/run/secrets/gitlab_root_password
        networks:
            devnet:
                ipv4_address: 172.28.0.10
                ipv6_address: 2001:3984:3989::10
networks:
    devnet:
        driver: bridge
        ipam:
            driver: default
            config:
                - subnet: 172.28.0.0/16
                - subnet: 2001:3984:3989::/64
volumes:
    mongo-data:
        driver: local
```
