FROM php:8.1.0-fpm

WORKDIR /var/www

#Install dependencies
RUN apt update && apt install -y \
      apt-utils \
      libpq-dev \
      libpng-dev \
      libzip-dev \
      zip unzip \
      libgd3 \
      libgd-dev \
      git && \
      docker-php-ext-install pdo_pgsql && \
      docker-php-ext-install bcmath && \
      docker-php-ext-install gd && \
      docker-php-ext-install zip && \
      apt clean && \
      rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#Configure gd
RUN docker-php-ext-configure gd --with-freetype --with-jpeg
RUN docker-php-ext-install -j$(nproc) gd

#Install npm
RUN curl -sL https://deb.nodesource.com/setup_18.x | apt-get update && \
    apt-get install -y nodejs &&\
    apt-get install -y npm &&\
    npm install laravel-mix@6.0.0 &&\
    npm clean-install

#Copy custom php.ini
COPY ./_docker/app/php.ini /usr/local/etc/php/conf.d/php.ini

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Install amqplib support
RUN composer require php-amqplib/php-amqplib

# Install symfony cli
RUN curl -1sLf 'https://dl.cloudsmith.io/public/symfony/stable/setup.deb.sh' | bash
RUN apt install symfony-cli

# Add user for symfony application
RUN groupadd -g 1000 www
RUN useradd -u 1000 -ms /bin/bash -g www www

# Copy existing application directory contents
COPY . /var/www

# Copy existing application directory permissions
COPY --chown=www:www . /var/www

# Change current user to www
USER www

# Expose port 9000 and start php-fpm server
EXPOSE 9000

CMD bash -c "composer install && php-fpm"
