FROM memcached:1.5.12-alpine

USER root

RUN apk add --no-cache --virtual .build-deps tzdata \
    && ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo "Asia/Shanghai" >  /etc/timezone \
    && apk del .build-deps

USER memcache

CMD [ "memcached" ]
