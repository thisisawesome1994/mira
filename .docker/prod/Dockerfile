FROM php:8.1-cli-alpine

RUN apk update && \
    apk add --no-cache coreutils icu icu-dev libzip libzip-dev memcached unzip yarn zip && \
    docker-php-ext-install bcmath intl sockets zip

COPY .docker/prod/php.ini /usr/local/etc/php/conf.d/php.ini
COPY --from=spiralscout/roadrunner:2.6.3 /usr/bin/rr /usr/bin/rr
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

ENV APP_ENV prod

WORKDIR /app
COPY ./ /app
RUN composer install --no-dev --classmap-authoritative && \
    composer dump-env prod && \
    bin/console cache:clear ; bin/console cache:warmup && \
    yarn install --pure-lockfile --non-interactive ; yarn build && \
    rm -rf assets/ node_modules/ .docker/ .env .dockerignore

EXPOSE 44356
EXPOSE 44357

ENTRYPOINT ["rr", "--WorkDir", "/app", "--config", "/app/.rr.yaml", "serve"]