# Base image
FROM dunglas/frankenphp:alpine AS base
WORKDIR /app
ENV SERVER_NAME=:80 
RUN install-php-extensions Ctype cURL DOM Fileinfo Filter Hash Mbstring OpenSSL PCNTL PCRE PDO Session Tokenizer XML
RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"

# Install dependencies only when needed
FROM composer:latest AS deps
WORKDIR /app
COPY composer.json composer.lock ./
RUN composer install --no-scripts --no-dev --classmap-authoritative 

# Rebuild the source code only when needed
FROM deps AS builder
COPY . .
RUN composer dump-autoload

# Production image, copy all the files and run laravel
FROM base AS runner
COPY --from=builder /app .
RUN echo 'APP_KEY=' > .env && php artisan key:generate
RUN php artisan optimize

ENTRYPOINT ["php", "artisan", "octane:frankenphp"]
