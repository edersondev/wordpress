# Referencias:
# https://github.com/docker-library/wordpress/tree/master/php7.3/apache
FROM wordpress:php7.3

ENV TZ=America/Sao_Paulo
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt-get update && apt-get install -y \
  libtidy-dev \
  zlib1g-dev \
  libmemcached-dev

RUN pecl install apcu \
  pecl install msgpack \
  pecl install igbinary

RUN docker-php-ext-install pdo_mysql \
  && docker-php-ext-install tidy \
  && docker-php-ext-enable apcu \
  && docker-php-ext-enable msgpack \
  && docker-php-ext-enable igbinary

# Instalação da extensão memcached
RUN curl -L -o /tmp/memcached.tar.gz "https://github.com/php-memcached-dev/php-memcached/archive/v3.1.3.tar.gz" \
  && mkdir -p /usr/src/php/ext/memcached \
  && tar -C /usr/src/php/ext/memcached -zxvf /tmp/memcached.tar.gz --strip 1 \
  && docker-php-ext-configure memcached --enable-memcached-igbinary --enable-memcached-json --enable-memcached-msgpack

# Instalação da extensão redis
RUN mkdir -p /usr/src/php/ext/redis \
  && curl -L https://github.com/phpredis/phpredis/archive/4.3.0.tar.gz | tar xvz -C /usr/src/php/ext/redis --strip 1 \
  && echo 'redis' >> /usr/src/php-available-exts

RUN docker-php-ext-install redis \
  && docker-php-ext-install memcached

RUN apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false; \
  rm -rf /var/lib/apt/lists/*

RUN a2enmod ext_filter
RUN a2enmod headers
RUN a2enmod expires

RUN { \
    echo 'memory_limit = 2048M'; \
    echo 'post_max_size = 50M'; \
    echo 'upload_max_filesize = 50M'; \
    echo 'max_execution_time = 300'; \
    echo 'date.timezone = "America/Sao_Paulo"'; \
    echo 'zlib.output_compression = On'; \
} > /usr/local/etc/php/conf.d/extra-conf.ini

RUN { \
    echo 'apc.shm_size = 256M'; \
    echo 'apc.ttl = 7200'; \
    echo 'apc.user_ttl = 7200'; \
    echo 'apc.gc_ttl = 3600'; \
} > /usr/local/etc/php/conf.d/apcu.ini

COPY wordpress /usr/src/wordpress
RUN chown -R www-data:www-data /usr/src/wordpress

ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["apache2-foreground"]
