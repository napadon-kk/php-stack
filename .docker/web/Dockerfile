FROM php:8.4-fpm-alpine

# Install system dependencies
RUN apk add --no-cache \
    nginx \
    supervisor \
    curl \
    zip \
    unzip \
    git \
    curl \
    linux-headers \
    # Utilities
    bash \
    nano \
    # Frontend Tools
    nodejs \
    npm \
    # Database Clients
    mariadb-client \
    postgresql-client

# Install PHP extensions using mlocati/docker-php-extension-installer
# This utility handles all dependencies including MS SQL drivers (msodbcsql18) automatically
COPY --from=mlocati/php-extension-installer /usr/bin/install-php-extensions /usr/local/bin/
RUN install-php-extensions \
    gd \
    intl \
    pdo_mysql \
    pdo_pgsql \
    zip \
    bcmath \
    opcache \
    pcntl \
    exif \
    imagick \
    sqlsrv \
    pdo_sqlsrv

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Configure Nginx
COPY .docker/web/nginx.conf /etc/nginx/http.d/default.conf

# Configure PHP
COPY .docker/web/php.ini /usr/local/etc/php/conf.d/custom.ini

# Configure Supervisor
COPY .docker/web/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Setup working directory
WORKDIR /var/www/html

COPY src/draft .

# Create a non-root user if needed, but for simplicity we'll stick to root/www-data handling
# Adjust permissions
RUN chown -R www-data:www-data /var/www/html

# Expose ports
EXPOSE 80

# Start Supervisor
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]
