FROM redis:5.0.3-alpine

RUN apk add --no-cache --virtual .build-deps tzdata \
    && ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo "Asia/Shanghai" >  /etc/timezone \
	&& apk del .build-deps

CMD [ "redis-server" ]
