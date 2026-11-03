FROM php:8.5-fpm-alpine AS base
# opcache is built into PHP 8.5 core, so it is not installed as an extension
RUN apk add --no-cache postgresql-dev libzip-dev icu-dev \
 && docker-php-ext-install pdo_pgsql pgsql zip intl pcntl
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer
WORKDIR /var/www/html

FROM base AS dev
# bind-mounted code; no opcache tuning needed

FROM base AS prod
RUN { \
      echo 'opcache.enable=1'; \
      echo 'opcache.validate_timestamps=0'; \
      echo 'opcache.memory_consumption=192'; \
    } > /usr/local/etc/php/conf.d/opcache.ini
COPY backend/ .
RUN composer install --no-dev --optimize-autoloader --no-interaction \
 && chown -R www-data:www-data storage bootstrap/cache