FROM php:8.0-fpm
LABEL maintainer="540173107@qq.com"


# set timezome
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

COPY ./sources.list /etc/apt/sources.list

RUN cat /etc/apt/sources.list
RUN rm -Rf /var/lib/apt/lists/*

# Install package and PHP Core extensions
RUN apt-get update -y && apt-get install -y \
        libzip-dev \
        git \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
    && docker-php-ext-configure gd --with-freetype=/usr/include/ --with-jpeg=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \
        && docker-php-ext-install zip \
        && docker-php-ext-install pdo_mysql \
        && docker-php-ext-install opcache \
        && docker-php-ext-install mysqli \
        && docker-php-ext-install pcntl \ 
        && docker-php-ext-install bcmath \ 
        && rm -r /var/lib/apt/lists/*

# Copy extensions had downloaded
COPY ./pkg/redis.tgz /home/redis.tgz

# Install PECL extensions (Redis)
RUN pecl install /home/redis.tgz && echo "extension=redis.so" > /usr/local/etc/php/conf.d/redis.ini

# Install PECL extensions (swoole)
RUN pecl install swoole && echo "extension=swoole.so" > /usr/local/etc/php/conf.d/swoole.ini


# Install Composer
ENV COMPOSER_HOME /root/composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
ENV PATH $COMPOSER_HOME/vendor/bin:$PATH

RUN rm -f /home/redis.tgz

WORKDIR /data

# Write Permission
RUN usermod -u 1000 www-data
