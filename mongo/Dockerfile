FROM mongo:4.0.0-xenial

RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo "Asia/Shanghai" >  /etc/timezone 

CMD ["mongod"]
