# Base image
FROM serversideup/php:fpm-nginx-alpine AS base
WORKDIR /var/www/html
ENV PHP_OPCACHE_ENABLE=1
ENV AUTORUN_ENABLED=true
RUN install-php-extensions Ctype cURL DOM Fileinfo Filter Hash Mbstring OpenSSL PCRE PDO Session Tokenizer XML

# Install dependencies only when needed
FROM base AS deps
COPY composer.json composer.lock ./
RUN composer install --no-scripts --no-dev --classmap-authoritative

# Rebuild the source code only when needed
FROM base AS builder
COPY --chown=www-data:www-data --from=deps /var/www/html/vendor ./vendor
COPY --chown=www-data:www-data . .
RUN composer dump-autoload

# Production image, copy all the files and run
FROM base AS runner
COPY --from=builder /var/www/html/ ./
RUN echo 'APP_KEY=' > .env && php artisan key:generate
