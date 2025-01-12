# Base image
FROM dunglas/frankenphp:alpine AS base
RUN install-php-extensions Ctype cURL DOM Fileinfo Filter Hash Mbstring OpenSSL PCRE PDO Session Tokenizer XML
WORKDIR /app

# Install dependencies only when needed
FROM base AS deps
COPY --from=composer/composer:latest-bin /composer /usr/bin/composer
COPY composer.json composer.lock ./
RUN composer install --no-dev --classmap-authoritative --optimize-autoloader
RUN composer clear-cache

# Rebuild the source code only when needed
FROM base AS builder
COPY --from=deps /app/vendor ./vendor
COPY . .

# Production image, copy all the files and run laravel
FROM base AS runner
COPY --from=builder /app ./
# RUN echo 'APP_KEY=' > .env && php artisan key:generate
ENV SERVER_NAME=:80
ENV FRANKENPHP_CONFIG="worker ./public/index.php"