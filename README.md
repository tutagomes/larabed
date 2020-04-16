As I wandered over those vast and imaginable wilderness of internet, I've never found a complete image with Apache to host my Laravel applications. It's always NGinx. So, here it is, I think.

#### An example of usage

```yml
# Restoring Composer Dependencies
FROM composer:1.7 as vendor

COPY database/ database/

COPY composer.json composer.json
COPY composer.lock composer.lock

RUN composer install \
    --ignore-platform-reqs \
    --no-interaction \
    --no-plugins \
    --no-scripts \
    --prefer-dist \
    --no-dev \
    --no-interaction \
    --optimize-autoloader

# Now to the business
FROM tutagomes/larabed:7.2-mysql

WORKDIR /var/www/html

COPY . /var/www/html
COPY --from=vendor /app/vendor/ /var/www/html/vendor/

# Banco temporário para criar link de storage
ENV DB_DATABASE /var/www/html/database.sqlite
ENV DB_CONNECTION sqlite

# Remove banco se existir
RUN rm database.sqlite || true

# Adiciona banco
RUN touch database.sqlite

# Removendo pasta caso exista
RUN rm -rf /var/www/html/public/storage || true
# Recriando link
RUN php artisan storage:link
```

