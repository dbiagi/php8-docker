FROM php:8.0.1-fpm-alpine3.13

# Install php compile dependencies
RUN apk --no-cache add \
    autoconf=2.69-r3 \
    dpkg-dev=1.20.6-r0 \
    dpkg=1.20.6-r0 \
    file=5.39-r0 \
    g++=10.2.1_pre1-r3 \
    gcc=10.2.1_pre1-r3 \
    libc-dev=0.7.2-r3 \
    make=4.3-r0 \
    pkgconf=1.7.3-r0 \
    re2c=1.3-r1 \
    icu-dev \
    rabbitmq-c rabbitmq-c-dev \
    postgresql-dev \
    libsodium-dev

# Install services
RUN apk --no-cache add \
    git \
    vim \
    gettext

RUN rm -rf /var/cache/apk/*

RUN docker-php-ext-configure intl && docker-php-ext-install intl

RUN docker-php-ext-configure opcache && docker-php-ext-install opcache

RUN docker-php-ext-install pgsql pdo_pgsql

RUN docker-php-ext-configure sodium && docker-php-ext-install sodium

RUN pecl install amqp-1.11.0beta && docker-php-ext-enable amqp

# Enable php8-xdebug if $PHP_XDEBUG_MODE is not empty
ARG PHP_XDEBUG_MODE=off
ARG PHP_XDEBUG_CLIENT_PORT=9000
ARG PHP_XDEBUG_CLIENT_HOST=172.16.0.1
ARG USER=1000

RUN mkdir -p /tmp/templates

COPY xdebug.ini  /tmp/templates/xdebug.ini.template

RUN if [[ "$PHP_XDEBUG_MODE" != "" ]]; then \
        pecl install xdebug; \
        docker-php-ext-enable xdebug; \
        export PHP_XDEBUG_MODE=$PHP_XDEBUG_MODE; \
        export PHP_XDEBUG_CLIENT_PORT=$PHP_XDEBUG_CLIENT_PORT; \
        export PHP_XDEBUG_CLIENT_HOST=$PHP_XDEBUG_CLIENT_HOST; \
        envsubst < /tmp/templates/xdebug.ini.template > ${PHP_INI_DIR}/conf.d/xdebug.ini; \
    fi

RUN rm -rf /tmp/templates

RUN curl -sS https://getcomposer.org/installer | tee composer-setup.php \
    && php composer-setup.php && rm composer-setup.php* \
    && chmod +x composer.phar && mv composer.phar /usr/bin/composer

COPY app.ini ${PHP_INI_DIR}/conf.d/
COPY app.ini ${PHP_INI_DIR}/cli/conf.d/

COPY application.poll.conf /usr/local/etc/php-fpm.d/

RUN mkdir -p /.composer

RUN chown -R $USER:$USER /.composer/

RUN mkdir -p /var/www/api

RUN echo ${PHP_INI_DIR}

WORKDIR /var/www/api

CMD ["php-fpm", "-F"]

EXPOSE 9001