FROM php:7.2-apache

ARG uid=1001
ARG gid=1001
RUN a2enmod rewrite

RUN a2enmod ssl

RUN apt-get update -yqq && \
    apt-get install -y apt-utils zip unzip && \
    apt-get install -y nano && \
    apt-get install -y libzip-dev libpq-dev libmcrypt-dev libbz2-dev libsqlite3-dev && \
    apt-get install -y libxml2-dev && \
    a2enmod rewrite && \
    docker-php-ext-install pdo_mysql && \
    docker-php-ext-install mysqli && \
    docker-php-ext-configure zip --with-libzip && \
    docker-php-ext-install zip && \
    docker-php-ext-install bcmath && \
    docker-php-ext-install calendar && \
    docker-php-ext-install exif && \
    docker-php-ext-install gettext && \
    docker-php-ext-install bz2 && \
    docker-php-ext-install pdo_sqlite && \
    docker-php-ext-install soap && \
    docker-php-ext-enable soap && \
    docker-php-ext-install sockets && \
    rm -rf /var/lib/apt/lists/*
    
RUN pecl install mcrypt-1.0.1
RUN docker-php-ext-enable mcrypt

RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
    && docker-php-ext-install -j$(nproc) iconv \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd

RUN php -r "readfile('http://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer

COPY default.conf /etc/apache2/sites-enabled/000-default.conf

RUN if ! grep -q ":${gid}:" /etc/group;then groupadd -g ${gid} ponte;fi
RUN useradd -u ${uid} -d /var/www/html -m -g ${gid} ponte \
	&& usermod -a -G ${gid} www-data
COPY ssl.conf /etc/apache2/sites-enabled/default-ssl.conf

RUN mkdir /opt/ssl
RUN chmod 700 /opt/ssl
RUN openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /opt/ssl/selfsigned.key -out /opt/ssl/selfsigned.crt -subj "/C=BR/ST=Sao_Paulo/L=Jundiai/O=Essencia_Digital/OU=Dev/CN=tst"
RUN openssl dhparam -out /opt/ssl/dhparam.pem 2048
RUN cat /opt/ssl/dhparam.pem | tee -a /opt/ssl/selfsigned.crt
WORKDIR /var/www

CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]

EXPOSE 80
