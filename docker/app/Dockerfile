FROM php:7.1-apache

MAINTAINER dereck.zenda@gmail.com

# Enable URL rewriting in .htaccess files
RUN a2enmod rewrite

# install the PHP extensions we need
RUN apt-get update \
    && apt-get install -y libpng12-dev libjpeg-dev \
    && docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/usr \
    && docker-php-ext-install gd \
    && docker-php-ext-install mbstring \
    && docker-php-ext-install mysqli \
    && docker-php-ext-install opcache zip\
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Allow an existing WordPress install to be mapped into /var/www/html
VOLUME /var/www/html

# Install wp-cli
RUN curl -L https://github.com/wp-cli/wp-cli/releases/download/v0.24.1/wp-cli-0.24.1.phar -o /usr/local/bin/wp \
&& chmod +x /usr/local/bin/wp

# set recommended PHP.ini settings
# see https://secure.php.net/manual/en/opcache.installation.php
RUN { \
		echo 'opcache.memory_consumption=128'; \
		echo 'opcache.interned_strings_buffer=8'; \
		echo 'opcache.max_accelerated_files=4000'; \
		echo 'opcache.revalidate_freq=2'; \
		echo 'opcache.fast_shutdown=1'; \
		echo 'opcache.enable_cli=1'; \
	} > /usr/local/etc/php/conf.d/opcache-recommended.ini

RUN a2enmod rewrite expires
# Replace the default apache2-foreground script with one that relies on apache2ctl, so
# that /etc/apache2/envvars can be used to configure the environment of the www-data user
COPY bin/apache2-foreground /usr/local/bin/apache2-foreground
RUN chmod +x /usr/local/bin/apache2-foreground

# Set up entrypoint script
ENV SCRIPTS_DIR /scripts
RUN mkdir /scripts /scripts/pre-install.d /scripts/post-install.d
COPY docker-entrypoint.sh /scripts/entrypoint.sh
RUN chmod +x /scripts/entrypoint.sh
ENTRYPOINT ["/scripts/entrypoint.sh"]

CMD ["apache2-foreground"]