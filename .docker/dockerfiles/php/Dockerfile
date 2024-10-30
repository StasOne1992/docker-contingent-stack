FROM php:8.2.21-fpm
RUN apt-get update
RUN apt-get install --reinstall ca-certificates
RUN apt-get install -y --no-install-recommends apt-utils supervisor systemctl  libssl-dev zlib1g-dev  \
    curl git unzip libxml2-dev libpq-dev libzip-dev libfreetype6-dev libjpeg62-turbo-dev libpng-dev
RUN pecl install apcu \
    && docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql \
    && docker-php-ext-install -j$(nproc) zip opcache intl pdo_pgsql pgsql gd \
    && docker-php-ext-configure gd --with-freetype=/usr/include/ --with-jpeg=/usr/include/ \
    && docker-php-ext-enable apcu pdo_pgsql sodium \
    && docker-php-ext-configure zip \
    && docker-php-source delete

RUN git config --global http.postBuffer 524288000

RUN git config  --global http.version HTTP/1.1

RUN git clone -b dev https://github.com/StasOne1992/contingent-app.git /var/www/project
WORKDIR /var/www/project

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN curl -1sLf 'https://dl.cloudsmith.io/public/symfony/stable/setup.deb.sh' | bash
RUN apt install symfony-cli
RUN composer install
## start SuperVisor service
RUN /bin/sh -c 'systemctl enable supervisor.service'

RUN php bin/console make:migration
RUN php bin/console doctrine:migrations:migrate


