# Use Debian-based PHP-FPM image
FROM php:8.2-fpm

# Set timezone to Vietnam (Asia/Ho_Chi_Minh)
ENV TZ=Asia/Ho_Chi_Minh
RUN apt-get update && apt-get install -y tzdata \
    && ln -fs /usr/share/zoneinfo/Asia/Ho_Chi_Minh /etc/localtime \
    && dpkg-reconfigure -f noninteractive tzdata

# Install system dependencies required for PHP extensions
RUN apt-get update && apt-get install -y \
    libcurl4-openssl-dev \
    libzip-dev \
    libxml2-dev \
    libpq-dev \
    libonig-dev \
    librdkafka-dev \
    libssl-dev \
    libjpeg-dev \
    libpng-dev \
    libfreetype6-dev \
    libonig-dev \
    libicu-dev \
    zlib1g-dev \
    wget \
    git \
    vim \
    unzip \
    gnupg \
    build-essential \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Install PHP extensions
RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install \
        curl \
        pdo \
        pdo_mysql \
        pdo_pgsql \
        zip \
        xml \
        opcache \
        session \
        gd \
        mbstring \
        intl \
    && docker-php-ext-enable opcache

# Install PECL extensions
RUN pecl install redis xdebug rdkafka \
    && docker-php-ext-enable redis rdkafka

# Add php.ini
COPY php.ini /usr/local/etc/php/php.ini

# Copy custom Xdebug configuration
COPY ext-xdebug.ini /usr/local/etc/php/conf.d/ext-xdebug.ini

# Install Composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php composer-setup.php --install-dir=/usr/local/bin --filename=composer \
    && php -r "unlink('composer-setup.php');"

# Install Node.js and npm
RUN curl -sL https://deb.nodesource.com/setup_16.x | bash - && \
    apt-get install -y nodejs

# Check installation of Node.js and npm
RUN node -v && npm -v

# Set working directory
WORKDIR /var/www/html

EXPOSE 9000

# Expose ports for Xdebug
EXPOSE 9003

# Clean up apt cache to keep the image size small
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

CMD ["php-fpm"]
