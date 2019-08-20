FROM php:7.2.21-fpm

# Install PHP extensions
RUN \
    apt-get update && \
    apt-get install -y --no-install-recommends \
        supervisor \
        cron \
        sudo \
        libxml2-dev \
        libcurl4-openssl-dev \
        libbz2-dev \
        icu-devtools \
        libicu-dev \
        libmcrypt-dev \
        libfreetype6-dev \
        libssl-dev \
        libpq-dev \
        curl \
        cron && \
    apt-get clean && \
	rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN \
    docker-php-ext-install zlib || \
    ( mv /usr/src/php/ext/zlib/config0.m4 /usr/src/php/ext/zlib/config.m4 && \
        docker-php-ext-install zlib )

RUN \
    docker-php-ext-install openssl || \
    ( mv /usr/src/php/ext/openssl/config0.m4 /usr/src/php/ext/openssl/config.m4 && \
        docker-php-ext-install openssl )

RUN  docker-php-ext-configure pgsql --with-pgsql=/usr/local/pgsql \
    && docker-php-ext-install \
      gd \
      curl \
      pdo \
      intl \
      mbstring \
      bcmath \
      pcntl \
      ftp \
      curl \
      bz2 \
      pdo \
      pdo_pgsql \
      pgsql \
      zip \
      opcache && \
     CFLAGS="-I/usr/src/php" docker-php-ext-install xmlreader

RUN \
    pecl install -o -f redis && \
    rm -rf /tmp/pear && \
    docker-php-ext-enable redis

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin --filename=composer

# install phpunit
RUN curl https://phar.phpunit.de/phpunit.phar -L > phpunit.phar \
  && chmod +x phpunit.phar \
  && mv phpunit.phar /usr/local/bin/phpunit \
  && phpunit --version

# install cron
RUN apt-get update && apt-get -y install rsyslog

RUN mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"
RUN sed -i 's/memory_limit = .*/memory_limit = '1G'/' "$PHP_INI_DIR/php.ini"
RUN sed -i 's/max_execution_time = *./max_execution_time = '360'/' "$PHP_INI_DIR/php.ini"
RUN sed -i 's/zlib.output_compression = Off/zlib.output_compression = On/' "$PHP_INI_DIR/php.ini"
RUN sed -i 's/display_errors = Off/display_errors = On/' "$PHP_INI_DIR/php.ini"
RUN sed -i 's/display_startup_errors = Off/display_startup_errors = On/' "$PHP_INI_DIR/php.ini"
RUN sed -i 's/;error_log = syslog/error_Log = syslog/' "$PHP_INI_DIR/php.ini"

# Change uid and gid of apache to docker user uid/gid
RUN usermod -u 1000 www-data && groupmod -g 1000 www-data

WORKDIR /var/www/html
