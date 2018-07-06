FROM redis:4.0.10-alpine

RUN apk add --no-cache --virtual .build-deps \
		tzdata \
	&& cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo "Asia/Shanghai" >  /etc/timezone \
	&& apk del .build-deps

CMD [ "redis-server" ]
