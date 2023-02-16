FROM php:8.1-fpm-bullseye AS base

WORKDIR /data

# timezone environment
ENV TZ=UTC \
  # locale
  LANG=en_US.UTF-8 \
  LANGUAGE=en_US:en \
  LC_ALL=en_US.UTF-8 \
  # composer environment
  COMPOSER_ALLOW_SUPERUSER=1 \
  COMPOSER_HOME=/composer \
  WORKDIR=/var/www/app.dev

COPY --from=composer:2.4 /usr/bin/composer /usr/bin/composer

RUN apt-get update \
  && apt-get -y install --no-install-recommends \
    locales \
    git \
    unzip \
    libzip-dev \
    libicu-dev \
    libonig-dev \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/* \
  && locale-gen en_US.UTF-8 \
  && localedef -f UTF-8 -i en_US en_US.UTF-8 \
  && docker-php-ext-install \
    intl \
    pdo_mysql \
    zip \
    bcmath \
  && composer config -g process-timeout 3600 \
  && composer config -g repos.packagist composer https://packagist.org
RUN docker-php-ext-install mysqli && docker-php-ext-enable mysqli
FROM base AS development

COPY ./docker/php/php.development.ini /usr/local/etc/php/php.ini

FROM development AS development-xdebug

RUN pecl install xdebug && \
  docker-php-ext-enable xdebug

COPY ./docker/php/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

FROM base AS deploy

COPY ./docker/php/php.deploy.ini /usr/local/etc/php/php.ini
COPY ./src /data

RUN composer install -q -n --no-ansi --no-dev --no-scripts --no-progress --prefer-dist

# Workdir
WORKDIR ${WORKDIR}

# Copy files to container
#COPY ./ ./

# Expose port 9000 and start php-fpm server
EXPOSE 9000

# Run entrypoint
COPY ./entrypoint.sh /usr/bin/docker-entrypoint
CMD [ "docker-entrypoint" ]
