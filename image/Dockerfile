# Referencia:
# https://github.com/docker-library/wordpress/tree/master/php7.3/apache
FROM wordpress:php7.3

ENV TZ=America/Sao_Paulo
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

COPY wordpress /usr/src/wordpress
RUN chown -R www-data:www-data /usr/src/wordpress

# Update the package lists and install everything
RUN docker-php-ext-install pdo_mysql

RUN { \
		echo 'memory_limit = 2048M'; \
		echo 'post_max_size = 50M'; \
		echo 'upload_max_filesize = 50M'; \
		echo 'max_execution_time = 300'; \
    echo 'date.timezone = "America/Sao_Paulo"'; \
	} > /usr/local/etc/php/conf.d/extra-conf.ini

ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["apache2-foreground"]
