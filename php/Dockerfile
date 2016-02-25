FROM php:5.6-apache

RUN a2enmod rewrite

# install the PHP extensions we need
RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libmysqlclient-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng12-dev \
    && docker-php-ext-install iconv mcrypt \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd \
    && docker-php-ext-install zip \
    && docker-php-ext-install mysqli \
    && docker-php-ext-install opcache \
    && docker-php-ext-install mbstring

RUN apt-get update && apt-get install -y wget
RUN wget http://xdebug.org/files/xdebug-2.3.3.tgz
RUN tar -xzf xdebug-2.3.3.tgz
RUN cd xdebug-2.3.3 && phpize && ./configure --enable-xdebug && make && make install
COPY xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini
RUN echo "date.timezone = 'Europe/Berlin'" >> /usr/local/etc/php/php.ini

VOLUME /var/www/html

ENV WORDPRESS_VERSION 4.2.1
ENV WORDPRESS_UPSTREAM_VERSION 4.2.1
ENV WORDPRESS_SHA1 c93a39be9911591b19a94743014be3585df0512f

# upstream tarballs include ./wordpress/ so this gives us /usr/src/wordpress
RUN curl -o wordpress.tar.gz -SL https://wordpress.org/wordpress-${WORDPRESS_UPSTREAM_VERSION}.tar.gz \
	&& echo "$WORDPRESS_SHA1 *wordpress.tar.gz" | sha1sum -c - \
	&& tar -xzf wordpress.tar.gz -C /usr/src/ \
	&& rm wordpress.tar.gz \
	&& chown -R www-data:www-data /usr/src/wordpress

COPY docker-entrypoint.sh /entrypoint.sh

# grr, ENTRYPOINT resets CMD now
ENTRYPOINT ["/entrypoint.sh"]
CMD ["apache2-foreground"]