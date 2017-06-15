FROM centos:latest

MAINTAINER xlang007er@gmail.com

ENV NGINX_VERSION 1.11.10

WORKDIR /tmp/tmpdir

COPY nginx-$NGINX_VERSION.tar.gz .

COPY openssl-1.1.0d.tar.gz .

COPY pcre-8.40.tar.gz .

COPY zlib-1.2.11.tar.gz .

COPY gperftools-2.5.tar.gz .

COPY nginx-ct.tar.gz . 

RUN CONFIG="\
	--prefix=/usr/local/nginx \
	--sbin-path=/usr/sbin/nginx \
	--conf-path=/etc/nginx/nginx.conf \
	--error-log-path=/var/log/nginx/error.log \
	--http-log-path=/var/log/nginx/access.log \
	--user=nginx \
	--group=nginx \
	--with-file-aio \
	--with-http_ssl_module \
	--with-http_v2_module \
	--with-http_realip_module \
	--with-http_addition_module \
	--with-http_sub_module \
	--with-http_dav_module \
	--with-http_flv_module \
	--with-http_mp4_module \
	--with-http_gunzip_module \
	--with-http_slice_module \
	--with-http_gzip_static_module \
	--with-http_random_index_module \
	--with-http_secure_link_module \
	--with-http_degradation_module \
	--with-http_stub_status_module \
	--with-http_perl_module=dynamic \
	--with-http_xslt_module=dynamic \
	--with-http_geoip_module=dynamic \
	--with-http_image_filter_module=dynamic \
	--with-pcre \
	--with-pcre-jit \
	--with-mail=dynamic \
	--with-mail_ssl_module \
	--with-stream=dynamic \
	--with-stream_ssl_module \
	--with-stream_ssl_preread_module \
	--with-stream_realip_module \
	--with-stream_geoip_module=dynamic \
	--with-google_perftools_module \
	--with-openssl=../openssl-1.1.0d \
	--with-pcre=../pcre-8.40 \
	--with-zlib=../zlib-1.2.11 \
	--add-module=../nginx-ct \
	"\ 
	&& yum update -y \
	&& yum install --nogpgcheck -y gcc gcc-c++ make gd-devel libunwind-devel libxslt-devel libxml2-devel perl-devel perl-ExtUtils-Embed GeoIP GeoIP-devel \
	&& tar zxf nginx-ct.tar.gz \
	&& tar zxf nginx-$NGINX_VERSION.tar.gz \
	&& tar zxf openssl-1.1.0d.tar.gz \
	&& tar zxf pcre-8.40.tar.gz \
	&& tar zxf zlib-1.2.11.tar.gz \
	&& tar zxf gperftools-2.5.tar.gz \
	&& cd ./gperftools-2.5 \
	&& ./configure --enable-shared \
	&& make -j$(getconf _NPROCESSORS_ONLN) \
	&& make install \
	&& cd ../nginx-$NGINX_VERSION \
	&& ./configure $CONFIG --with-debug \
	&& make -j$(getconf _NPROCESSORS_ONLN) \
	&& make install \
	&& groupadd -r nginx \
	&& useradd -s /sbin/nologin -g nginx nginx \
    && ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && if [ -f /etc/timezone ]; then echo "Asia/Shanghai" >  /etc/timezone; fi \
    && echo "/usr/local/lib" > /etc/ld.so.conf.d/libprofiler.so.0.conf \
    && ldconfig \
    && rm -rf /var/cache/yum /tmp/tmpdir

WORKDIR /var/www

EXPOSE 80 443

CMD ["/usr/sbin/nginx", "-g", "daemon off;"]
