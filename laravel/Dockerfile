# Base image
FROM serversideup/php:fpm-nginx-alpine AS base
USER root
RUN install-php-extensions Ctype cURL DOM Fileinfo Filter Hash Mbstring OpenSSL PCRE PDO Session Tokenizer XML
USER www-data
WORKDIR /var/www/html

# Install dependencies only when needed
FROM base AS deps
COPY composer.json composer.lock ./
RUN composer install --no-dev --optimize-autoloader

# Rebuild the source code only when needed
FROM base AS builder
COPY --from=deps /var/www/html/vendor ./vendor
COPY . .

# Production image, copy all the files and run laravel
FROM base AS runner
# ENV ENV=production
ENV PHP_OPCACHE_ENABLE=1
ENV AUTORUN_ENABLED=true
COPY --from=builder --chown=www-data:www-data /var/www/html/vendor ./vendor
COPY --from=builder --chown=www-data:www-data /var/www/html/ ./
