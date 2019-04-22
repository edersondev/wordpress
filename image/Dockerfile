FROM ubuntu:18.04
LABEL maintainer="Ederson Ferreira <ederson.dev@gmail.com>"

ENV TZ=America/Sao_Paulo
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt-get update && apt-get install -y \
apache2 \
libapache2-mod-php \
libapache2-mod-fcgid \
php \
php-cli \
php-common \
php-curl \
php-gd \
php-mysql \
php-odbc \
php-bz2 \
php-imagick \
php-json \
php-mbstring \
php-pclzip \
php-xml \
php-zip \
php-apcu \
php-memcached \
php-memcache \
php-redis \
curl \
nano \
&& apt-get clean && apt-get autoclean && apt-get autoremove \
&& rm -rf /var/lib/apt/lists/*

RUN openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/ssl-cert-snakeoil.key -out /etc/ssl/certs/ssl-cert-snakeoil.pem -subj "/C=AT/ST=Vienna/L=Vienna/O=Security/OU=Development/CN=example.com"

RUN a2enmod ext_filter
RUN a2enmod rewrite
RUN a2enmod expires
RUN a2enmod headers
RUN a2enmod fcgid
RUN a2enmod proxy_fcgi
RUN a2enmod ssl
RUN a2enmod http2

RUN a2ensite default-ssl

ENV APACHE_LOCK_DIR="/var/lock"
ENV APACHE_PID_FILE="/var/run/apache2.pid"
ENV APACHE_RUN_USER="www-data"
ENV APACHE_RUN_GROUP="www-data"
ENV APACHE_LOG_DIR="/var/log/apache2"
ENV APACHE_RUN_DIR="/var/run/apache2"

RUN { \
    echo 'memory_limit = 2048M'; \
    echo 'post_max_size = 50M'; \
    echo 'upload_max_filesize = 50M'; \
    echo 'max_execution_time = 300'; \
    echo 'max_input_time = 300'; \
    echo 'date.timezone = "America/Sao_Paulo"'; \
    echo 'max_input_vars = 4000'; \
    echo 'realpath_cache_ttl = 300'; \
} > /etc/php/7.2/apache2/conf.d/extra-conf.ini

RUN { \
    echo 'apc.shm_size = 256M'; \
    echo 'apc.ttl = 7200'; \
    echo 'apc.user_ttl = 7200'; \
    echo 'apc.gc_ttl = 3600'; \
} > /etc/php/7.2/apache2/conf.d/apcu.ini

VOLUME /var/www/html

COPY 000-default.conf /etc/apache2/sites-available/000-default.conf
COPY default-ssl.conf /etc/apache2/sites-available/default-ssl.conf

ENV WORDPRESS_VERSION 5.1.1
ENV WORDPRESS_SHA1 f1bff89cc360bf5ef7086594e8a9b68b4cbf2192

RUN set -ex; \
	curl -o wordpress.tar.gz -fSL "https://wordpress.org/wordpress-${WORDPRESS_VERSION}.tar.gz"; \
	echo "$WORDPRESS_SHA1 *wordpress.tar.gz" | sha1sum -c -; \
# upstream tarballs include ./wordpress/ so this gives us /usr/src/wordpress
	tar -xzf wordpress.tar.gz -C /usr/src/; \
	rm wordpress.tar.gz; \
	chown -R www-data:www-data /usr/src/wordpress

WORKDIR /var/www/html

COPY wordpress /usr/src/wordpress
RUN chown -R www-data:www-data /usr/src/wordpress

COPY apache2-foreground /usr/local/bin/
COPY wp-entrypoint.sh /usr/local/bin/
RUN chmod +x /usr/local/bin/*

EXPOSE 80
EXPOSE 443

ENTRYPOINT ["wp-entrypoint.sh"]

CMD ["apache2-foreground"]
