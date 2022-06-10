ARG PHP_VERSION
FROM ${PHP_VERSION}

ARG TZ
ARG PHP_EXTENSIONS
ARG CONTAINER_PACKAGE_URL

# 复制源
COPY sources.list /etc/apt/

# 安装基础组件
RUN apt-get update  \
    && apt-get install -y vim telnet curl wget zip unzip procps iputils-ping cmake  \
    && apt-get install -y libssl-dev libbz2-dev libmcrypt4 libmcrypt-dev libxslt1.1 libxslt1-dev libpng-dev libjpeg-dev libfreetype6-dev

#安装libzip
COPY extensions/libzip-1.8.0.tar.gz /root/
RUN cd /root && tar -zxvf libzip-1.8.0.tar.gz && cd libzip-1.8.0 && mkdir build && cd build && cmake .. && make && make install

#安装rekafka的lib库
COPY ./extensions/librdkafka-1.8.2.tar.gz /root/
RUN cd /root && tar -zxvf librdkafka-1.8.2.tar.gz && cd librdkafka-1.8.2 && ./configure && make && make install

RUN echo "alias ll='ls -ahl'" > /root/.bashrc

# 创建php源目录
RUN docker-php-source extract

# 将扩展解压并拷贝到源目录下
ADD extensions/redis-5.3.5.tgz /usr/src/php/ext
ADD extensions/mongodb-1.12.0.tgz /usr/src/php/ext
ADD extensions/rdkafka-5.0.2.tgz /usr/src/php/ext
ADD extensions/xdebug-2.9.1.tgz /usr/src/php/ext

# docker-php-ext-install将安装并启动扩展
RUN docker-php-ext-install sockets pcntl pdo_mysql gd bz2 zip bcmath mbstring
RUN docker-php-ext-install redis-5.3.5
RUN docker-php-ext-install mongodb-1.12.0
RUN docker-php-ext-install rdkafka-5.0.2
RUN docker-php-ext-install xdebug-2.9.1

# Install composer and change it's cache home
RUN curl -o /usr/bin/composer https://mirrors.aliyun.com/composer/composer.phar \
    && chmod +x /usr/bin/composer
ENV COMPOSER_HOME=/tmp/composer

# php image's www-data user uid & gid are 82, change them to 1000 (primary user)
RUN usermod -u 1000 www-data && groupmod -g 1000 www-data

WORKDIR /www
