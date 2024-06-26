ARG ALPINE_VERSION=3.15
FROM alpine:${ALPINE_VERSION}

# Setup document root
WORKDIR /var/www/html

RUN echo "http://dl-cdn.alpinelinux.org/alpine/edge/testing" >> /etc/apk/repositories
RUN apk update && apk upgrade

# Install packages and remove default server definition
RUN apk add --no-cache \
  nginx \
  curl \
  zip \
  unzip \
  libcurl \
  libpq \
  git \
  dialog \
  autoconf \
  make \
  libzip-dev \
  bzip2-dev \
  icu-dev \
  tzdata \
  libpng-dev \
  libjpeg-turbo-dev \
  freetype-dev \
  libxslt-dev \
  curl-dev \
  openssl \
  openssl-dev \
  php7 \
  php7-ctype \
  php7-curl \
  php7-dom \
  php7-fpm \
  php7-gd \
  php7-intl \
  php7-mbstring \
  php7-mysqli \
  php7-opcache \
  php7-openssl \
  php7-phar \
  php7-session \
  php7-xml \
  php7-xmlreader \
  supervisor

# make sure you can use HTTPS
RUN apk --update add ca-certificates

# Install additional PHP extensions
RUN apk add --no-cache \
  php7-pdo_mysql \
  php7-exif \
  php7-pcntl \
  php7-bcmath \
  php7-fileinfo \
  php7-json \
  php7-tokenizer \
  php7-xmlwriter \
  php7-simplexml \
  php7-sodium \
  php7-iconv \
  php7-ftp \
  php7-zip \
  php7-pear \
  php7-dev \
  gcc \
  musl-dev \
  make


## Install Mongo From PECL
RUN pecl install mongodb

RUN pecl install redis

RUN pecl7 config-set php_ini /etc/php.ini

# Configure nginx - http
COPY config/nginx.conf /etc/nginx/nginx.conf

# Configure nginx - default server
COPY config/conf.d /etc/nginx/conf.d/

# Configure PHP-FPM
COPY config/fpm-pool.conf /etc/php7/php-fpm.d/www.conf
COPY config/php.ini /etc/php7/conf.d/custom.ini

# Configure supervisord
COPY config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Make sure files/folders needed by the processes are accessable when they run under the nobody user
RUN chown -R nobody.nobody /var/www/html /run /var/lib/nginx /var/log/nginx

# Switch to use a non-root user from here on
USER nobody

# Add application
COPY --chown=nobody src/ /var/www/html/

# Expose the port nginx is reachable on
EXPOSE 8080

# Let supervisord start nginx & php-fpm
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]

# Configure a healthcheck to validate that everything is up&running
HEALTHCHECK --timeout=10s CMD curl --silent --fail http://127.0.0.1:8080/fpm-ping

WORKDIR /var/www
