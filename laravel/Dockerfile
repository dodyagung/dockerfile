# Base image

FROM serversideup/php:8.4-fpm-nginx-alpine AS base

USER root
RUN install-php-extensions Ctype cURL DOM Fileinfo Filter Hash Mbstring OpenSSL PCRE PDO Session Tokenizer XML
USER www-data

WORKDIR /app

############################################
# Development Image
############################################

FROM base AS development

ARG USER_ID
ARG GROUP_ID

USER root
RUN docker-php-serversideup-set-id www-data $USER_ID:$GROUP_ID && \
    docker-php-serversideup-set-file-permissions --owner $USER_ID:$GROUP_ID --service nginx
USER www-data

############################################
# CI image
############################################

FROM base AS ci

USER root
RUN echo "user = www-data" >> /usr/local/etc/php-fpm.d/docker-php-serversideup-pool.conf && \
    echo "group = www-data" >> /usr/local/etc/php-fpm.d/docker-php-serversideup-pool.conf

############################################
# Production Image
############################################

FROM base AS production

COPY --chown=www-data:www-data . /var/www/html
USER www-data