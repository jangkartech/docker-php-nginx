ARG ALPINE_VERSION=3.17
FROM alpine:${ALPINE_VERSION}

# Setup document root
WORKDIR /var/www/html

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
  php81 \
  php81-ctype \
  php81-curl \
  php81-dom \
  php81-fpm \
  php81-gd \
  php81-intl \
  php81-mbstring \
  php81-mysqli \
  php81-opcache \
  php81-openssl \
  php81-phar \
  php81-session \
  php81-xml \
  php81-xmlreader \
  supervisor

# make sure you can use HTTPS
RUN apk --update add ca-certificates

# Install additional PHP extensions
RUN apk add --no-cache \
  php81-pdo_mysql \
  php81-exif \
  php81-pcntl \
  php81-bcmath \
  php81-fileinfo \
  php81-json \
  php81-tokenizer \
  php81-xmlwriter \
  php81-simplexml \
  php81-zip

# Configure nginx - http
COPY config/nginx.conf /etc/nginx/nginx.conf

# Configure nginx - default server
COPY config/conf.d /etc/nginx/conf.d/

# Configure PHP-FPM
COPY config/fpm-pool.conf /etc/php81/php-fpm.d/www.conf
COPY config/php.ini /etc/php81/conf.d/custom.ini

# Configure supervisord
COPY config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Make sure files/folders needed by the processes are accessable when they run under the nobody user
RUN chown -R nobody.nobody /var/www /run /var/lib/nginx /var/log/nginx

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