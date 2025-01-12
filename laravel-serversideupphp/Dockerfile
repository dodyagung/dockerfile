# Base image
FROM serversideup/php:fpm-nginx-alpine AS base
RUN install-php-extensions Ctype cURL DOM Fileinfo Filter Hash Mbstring OpenSSL PCRE PDO Session Tokenizer XML
WORKDIR /var/www/html

# Install dependencies only when needed
FROM base AS deps
COPY composer.json composer.lock ./
RUN composer install --no-dev --classmap-authoritative --optimize-autoloader
RUN composer clear-cache

# Rebuild the source code only when needed
FROM base AS builder
COPY --from=deps /var/www/html/vendor ./vendor
COPY . .

# Production image, copy all the files and run laravel
FROM base AS runner
COPY --from=builder --chown=www-data:www-data /var/www/html/ ./
ENV PHP_OPCACHE_ENABLE=1
ENV AUTORUN_ENABLED=true
RUN echo 'APP_KEY=' > .env && php artisan key:generate
