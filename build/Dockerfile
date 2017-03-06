# The base image that provides the PHP runtime and composer packages
# which are requried by the application. You should use docker-compose
# to build/update the image

FROM php:7.0.16-apache

# Set up PHP environment
RUN apt-get update \
    && apt-get -y install \
            git \
            g++ \
            libicu-dev \
            libmcrypt-dev \
            zlib1g-dev \
        --no-install-recommends \

    # Enable mod_rewrite
    && a2enmod rewrite \

    # Extensions required by yii2
    && docker-php-ext-install intl \
    && docker-php-ext-install mbstring \
    && docker-php-ext-install mcrypt \
    && docker-php-ext-install zip \

    # Optional extensions (modify as needed)
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-install opcache \
    && pecl install apcu-5.1.8 && echo extension=apcu.so > /usr/local/etc/php/conf.d/apcu.ini \

    # Cleanup to keep the images size small
    && apt-get purge -y g++ \
    && apt-get autoremove -y \
    && rm -r /var/lib/apt/lists/*

# Install composer and composer packages
COPY install-composer /tmp/install-composer
COPY composer.json /var/www/html/
COPY composer.lock /var/www/html/
RUN /tmp/install-composer \
    && composer install --no-progress

WORKDIR /var/www/html
