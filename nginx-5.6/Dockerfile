# The container includes:
#
# - Git
# - NGINX
# - PHP Version 5.6
# - Composer
# - MySQL Client

# Use Alpine Linux
FROM alpine:3.3
MAINTAINER Luis Baroni <lcsbaroni@yahoo.com.br>

# Timezone
ENV TIMEZONE "America/Sao_Paulo"
ENV PHP_MEMORY_LIMIT 512M
ENV MAX_UPLOAD 50M
ENV PHP_MAX_FILE_UPLOAD 200
ENV PHP_MAX_POST 100M

ADD ./conf/nginx.runit /etc/service/nginx/run
ADD ./conf/php.runit /etc/service/php/run

RUN echo "@community http://nl.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories && \
    echo "@testing http://nl.alpinelinux.org/alpine/edge/testing" >> /etc/apk/repositories && \
    apk update && \
    apk upgrade && \
    apk add --update tzdata && \
    cp /usr/share/zoneinfo/${TIMEZONE} /etc/localtime && \
    echo "${TIMEZONE}" > /etc/timezone && \
    apk add --update \
        nginx \
        libxml2 \
        pwgen \
        runit@community \
        curl \
        git \
        openssh-client \
        php@community \
        php-fpm@community  \
        php-dev@community \
        php-dom@community \
        php-iconv@community \
        php-ctype@community \
        php-cli@community \
        php-phar@community \
        php-mcrypt@community \
        php-mysql@community \
        php-mysqli@community \
        php-curl@community \
        php-json@community \
        php-gd@community \
        php-openssl@community \
        php-pdo@community \
        php-pdo_mysql@community \
        php-zlib \
        php-xml@community

RUN sed -i "s|short_open_tag = .*|short_open_tag = On|" /etc/php/php.ini && \
    sed -i "s|;date.timezone =.*|date.timezone = ${TIMEZONE}|" /etc/php/php.ini && \
    sed -i "s|memory_limit =.*|memory_limit = ${PHP_MEMORY_LIMIT}|" /etc/php/php.ini && \
    sed -i "s|upload_max_filesize =.*|upload_max_filesize = ${MAX_UPLOAD}|" /etc/php/php.ini && \
    sed -i "s|max_file_uploads =.*|max_file_uploads = ${PHP_MAX_FILE_UPLOAD}|" /etc/php/php.ini && \
    sed -i "s|post_max_size =.*|max_file_uploads = ${PHP_MAX_POST}|" /etc/php/php.ini && \
    sed -i "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/" /etc/php/php.ini && \
    apk del tzdata && \
    rm -fr /tmp/*.apk && \
    rm -rf /var/cache/apk/* && \
    mkdir /etc/nginx/sites-enabled && \
    adduser -u 1000 docker -D -s /bin/ash && \
    chown -R docker:docker /home/docker && \
    mkdir -p /etc/service && \
    chmod a+x /etc/service/nginx/run && \
    chmod a+x /etc/service/php/run && \
    mkdir /www && \
    chown -R docker:docker /www && \
    echo "Success"

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

ADD ./conf/nginx.conf /etc/nginx/nginx.conf
ADD ./conf/default.conf /etc/nginx/sites-enabled/default
ADD ./conf/php-fpm.conf /etc/php/php-fpm.conf
ADD ./conf/www.conf /etc/php/php-fpm.d/www.conf

# Set Workdir
WORKDIR /www

# Expose ports
EXPOSE 80 443

CMD ["sh", "-c", "exec /sbin/runsvdir -P /etc/service/"]