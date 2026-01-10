# 
# WordPress with wp-cli and Xdebug for standard structure
# Based on official WordPress Docker image

FROM wordpress:php8.2-apache
ARG SERVER_NAME

# Install sudo, mysql client, text editors
RUN apt-get update && apt-get install -y sudo less default-mysql-client nano vim unzip

# Install WP-CLI 
RUN curl -o /bin/wp-cli.phar https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar
COPY wp-su.sh /bin/wp
RUN chmod +x /bin/wp-cli.phar /bin/wp

# Cleanup
RUN apt-get clean
RUN rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Install Xdebug
RUN pecl install xdebug \
    && docker-php-ext-enable xdebug

# Copy modified php.ini for development
COPY php.ini-development /usr/local/etc/php

# Copy apache conf file
COPY ./conf/000-default.conf /etc/apache2/sites-available/000-default.conf

# Replace server name with our custom domain in apache2 conf
RUN sed -i -e "s/127.0.0.1/${SERVER_NAME}/g" /etc/apache2/sites-available/000-default.conf
