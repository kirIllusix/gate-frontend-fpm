# gate-frontend-fpm
```
FROM php:7.0.33-fpm

# disable interactive functions
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update
RUN apt-get update \
	&& apt-get install -y --no-install-recommends \
	  libicu-dev \
    libmcrypt-dev \
    zlib1g-dev \
    gettext \
    zip \
    unzip \
	&& rm -rf /var/lib/apt/lists/*

# Install various PHP extensions
RUN docker-php-ext-configure pdo_mysql --with-pdo-mysql \
    && docker-php-ext-configure mbstring --enable-mbstring \
    && docker-php-ext-install -j5 \
		mysqli \
    pdo_mysql \
		mbstring \
		intl \
    gettext \
    json \
    opcache \
    mcrypt \
		zip \
	&& docker-php-ext-enable \
		mysqli \
    pdo_mysql \
		mbstring \
		intl \
    gettext \
    json \
    opcache \
    mcrypt \
		zip

# Copy timezone configration
COPY ./src/timezone.ini /usr/local/etc/php/conf.d/timezone.ini

# Set timezone
RUN rm /etc/localtime
RUN ln -s /usr/share/zoneinfo/Europe/Kiev /etc/localtime
RUN "date"

RUN apt autoremove -y \
	&& apt clean -y \
	&& rm -rf /tmp/* /var/tmp/* \
	&& find /var/cache/apt/archives /var/lib/apt/lists -not -name lock -type f -delete \
	&& find /var/cache -type f -delete

RUN mkdir -p /var/www && chown www-data:www-data /var/www && chmod 777 /var/www

VOLUME /var/www
WORKDIR /var/www
```
