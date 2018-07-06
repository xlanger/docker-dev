FROM node:10.6.0-alpine

RUN apk add --no-cache --virtual .build-deps \
        tzdata \
    && cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo "Asia/Shanghai" >  /etc/timezone \
	&& npm install -g pm2 \
    && apk del .build-deps

CMD ["node"]
