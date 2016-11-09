# The container includes:
#
# - Git
# - Apache2
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

ADD ./conf/apache.runit /etc/service/apache/run

RUN echo "@community http://nl.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories && \
    apk update && \
    apk upgrade && \
    apk add --update tzdata && \
    cp /usr/share/zoneinfo/${TIMEZONE} /etc/localtime && \
    echo "${TIMEZONE}" > /etc/timezone && \
    apk add --update \
        php-apache2 \
        libxml2 \
        pwgen \
        runit@community \
        curl \
        git \
        openssh-client \
        bash \
        php@community \
        php-dev@community \
        php-iconv@community \
        php-ctype@community \
        php-cli@community \
        php-gettext@community \
        php-dom@community \
        php-phar@community \
        php-mcrypt@community \
        php-mysql@community \
        php-mysqli@community \
        php-curl@community \
        php-json@community \
        php-gd@community \
        php-pear@community \
        php-openssl@community \
        php-soap@community \
        php-xml@community

RUN sed -i "s|short_open_tag = .*|short_open_tag = On|" /etc/php/php.ini && \
    sed -i "s|;date.timezone =.*|date.timezone = ${TIMEZONE}|" /etc/php/php.ini && \
    sed -i "s|memory_limit =.*|memory_limit = ${PHP_MEMORY_LIMIT}|" /etc/php/php.ini && \
    sed -i "s|upload_max_filesize =.*|upload_max_filesize = ${MAX_UPLOAD}|" /etc/php/php.ini && \
    sed -i "s|max_file_uploads =.*|max_file_uploads = ${PHP_MAX_FILE_UPLOAD}|" /etc/php/php.ini && \
    sed -i "s|post_max_size =.*|max_file_uploads = ${PHP_MAX_POST}|" /etc/php/php.ini && \
    sed -i "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/" /etc/php/php.ini && \
    sed -i "s|#LoadModule rewrite_module modules/mod_rewrite.so|LoadModule rewrite_module modules/mod_rewrite.so|" /etc/apache2/httpd.conf && \
    sed -i "s/AllowOverride None/AllowOverride All/g" /etc/apache2/httpd.conf && \
    apk del tzdata && \
    rm -fr /tmp/*.apk && \
    rm -rf /var/cache/apk/* && \

    mkdir -p /etc/service && \
    chmod a+x /etc/service/apache/run && \
    echo "Success"

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

RUN mkdir -p /run/apache2

# Set Workdir
WORKDIR /var/www/localhost/htdocs

# Expose ports
EXPOSE 80 443

CMD ["sh", "-c", "exec /sbin/runsvdir -P /etc/service/"]