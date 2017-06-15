FROM centos:latest

ENV NODE_VERSION 6.9.5

COPY node-v$NODE_VERSION.tar.gz . 

RUN yum install -y gcc gcc-c++ make \
	&& tar -zxf node-v$NODE_VERSION.tar.gz \
	&& cd node-v$NODE_VERSION \
	&& ./configure \
	&& make -j$(getconf _NPROCESSORS_ONLN) \
	&& make install \
	&& npm install -g pm2 \
	&& cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
	&& echo "Asia/Shanghai" >  /etc/timezone \
	&& cd .. \
    && rm -rf "node-v$NODE_VERSION" \
    && rm "node-v$NODE_VERSION.tar.gz" \
	&& rm -rf /var/cache/yum

CMD ["node"]
