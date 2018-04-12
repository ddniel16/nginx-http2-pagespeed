FROM debian:stretch-slim

RUN \
    apt-get update && \
    apt-get -y upgrade && \
    apt-get clean

# Install required dependencies
RUN \
    apt install --no-install-suggests -y \
    curl unzip wget vim \
    openssl libssl-dev sudo \
    build-essential zlib1g-dev libpcre3-dev unzip uuid-dev

# Install ngxpagespeed
RUN /bin/bash -c "$(curl -f -L -sS https://ngxpagespeed.com/install) -y --nginx-version latest --additional-nginx-configure-arguments '--with-http_ssl_module --with-http_v2_module --with-http_gzip_static_module --user=nginx --group=nginx --sbin-path=/usr/sbin/nginx --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log'"

RUN useradd --system --no-create-home --user-group nginx

RUN ln -sf /dev/stdout /var/log/nginx/access.log
RUN ln -sf /dev/stderr /var/log/nginx/error.log

RUN rm -rf $HOME
RUN apt-get purge build-essential -y && apt-get autoremove -y

ENV NGINX_PATH "/usr/local/nginx"
RUN openssl genrsa -out $NGINX_PATH/conf/server.key 1024
RUN openssl req -new -key $NGINX_PATH/conf/server.key -out $NGINX_PATH/conf/server.csr -subj "/C=ES/ST=Bilbao/L=Bizkaia/O=Example Bilbao/CN=examplebilbao.com"
RUN openssl x509 -req -days 365 -in $NGINX_PATH/conf/server.csr -signkey $NGINX_PATH/conf/server.key -out $NGINX_PATH/conf/server.crt

# Home
ENV HOME /root
WORKDIR /root

EXPOSE 443

ENTRYPOINT ["nginx", "-g", "daemon off;"]