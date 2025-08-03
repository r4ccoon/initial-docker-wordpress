FROM php:8.2-fpm-bullseye

# Install system dependencies
RUN apt-get update && apt-get install -y \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    libwebp-dev \
    libzip-dev \
    libicu-dev \
    libonig-dev \
    libpq-dev \
    unzip \
    git \
    default-mysql-client \
    && docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-install mysqli \
    && docker-php-ext-install zip \
    && docker-php-ext-install intl \
    && docker-php-ext-install opcache \
    && docker-php-ext-install exif \
    && docker-php-ext-install bcmath \
    && docker-php-ext-configure intl \
    && docker-php-ext-enable opcache

# Install Redis extension
RUN pecl install redis && docker-php-ext-enable redis

# Install Xdebug (for development, disable in production)
# RUN pecl install xdebug && docker-php-ext-enable xdebug

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/local/bin/composer

# Set working directory
WORKDIR /var/www/html

# Copy custom PHP configuration
COPY ./php.ini /usr/local/etc/php/conf.d/custom.ini

# Set proper permissions
RUN chown -R www-data:www-data /var/www/html \
    && find /var/www/html -type d -exec chmod 755 {} \; \
    && find /var/www/html -type f -exec chmod 644 {} \;

# Clean up
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Expose port 9000 for PHP-FPM
EXPOSE 9000

# Start PHP-FPM
CMD ["php-fpm"]
