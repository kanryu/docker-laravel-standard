FROM php:8.1-fpm
COPY php.ini /usr/local/etc/php/

RUN apt-get -y update \
  && apt-get install -y zlib1g-dev zip mariadb-client vim curl gnupg \
  libzip-dev libfreetype6-dev libjpeg62-turbo-dev libpng-dev libmcrypt-dev \
  && docker-php-ext-install zip pdo_mysql gd iconv exif
RUN apt-get install libyaml-dev -y
RUN pecl install yaml && echo "extension=yaml.so" > /usr/local/etc/php/conf.d/ext-yaml.ini && docker-php-ext-enable yaml
RUN pecl install xdebug
#node.js install
RUN curl -sL https://deb.nodesource.com/setup_18.x | bash -
RUN apt-get install -y nodejs
RUN npm install npm@latest -g
#Composer install
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
RUN php composer-setup.php
RUN php -r "unlink('composer-setup.php');"
RUN mv composer.phar /usr/local/bin/composer
# COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

ENV COMPOSER_ALLOW_SUPERUSER 1

ENV COMPOSER_HOME /composer

ENV PATH $PATH:/var/www/laravel-app/vendor/bin


WORKDIR /var/www

RUN composer global require "laravel/installer"
EXPOSE 5173
