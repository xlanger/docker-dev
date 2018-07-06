FROM mysql:5.7.22

RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo "Asia/Shanghai" >  /etc/timezone 

CMD ["mysqld"]
