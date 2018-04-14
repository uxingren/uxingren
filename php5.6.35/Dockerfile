FROM php:5.6.35-fpm-alpine

LABEL maintainer "wsmdr1986@gmail.com"

ENV PHPIZE_DEPS \
  autoconf \
  dpkg-dev dpkg \
  file \
  g++ \
  gcc \
  libc-dev \
  make \
  pkgconf \
  re2c

ADD redis-3.1.1.tgz /usr/src
ADD yaf-2.3.5.tgz /usr/src

COPY ./repositories /etc/apk/repositories

RUN apk update \
    && apk add --no-cache --virtual .build-deps \
		$PHPIZE_DEPS \
    libmcrypt-dev libpng-dev \
    && docker-php-source extract \
    && mv /usr/src/redis-3.1.1 /usr/src/php/ext/redis \
    && mv /usr/src/yaf-2.3.5 /usr/src/php/ext/yaf \
    && docker-php-ext-install mcrypt pdo_mysql sockets gd opcache pcntl zip redis yaf \
    && runDeps="$( \
		scanelf --needed --nobanner --format '%n#p' --recursive /usr/local \
			| tr ',' '\n' \
			| sort -u \
			| awk 'system("[ -e /usr/local/lib/" $1 " ]") == 0 { next } { print "so:" $1 }' \
	  )" \
	  && apk add --no-cache --virtual .php-rundeps $runDeps \
    && docker-php-source delete && apk del .build-deps

COPY ./composer.phar /usr/local/bin/composer

RUN chmod o+x /usr/local/bin/composer

EXPOSE 9000
CMD ["php-fpm"]
