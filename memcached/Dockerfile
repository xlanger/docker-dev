FROM memcached:1.4.34-alpine

USER root

RUN apk add --no-cache --virtual .build-deps \
		tzdata \
	&& cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo "Asia/Shanghai" >  /etc/timezone \
	&& apk del .build-deps

USER memcache

CMD [ "memcached" ]
