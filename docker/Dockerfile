FROM public.ecr.aws/unocha/php-k8s:8.2-stable as builder

ENV COMPOSER_ALLOW_SUPERUSER=1

COPY . /srv/www
WORKDIR /srv/www
RUN apk add -U --no-cache patch && \
    rm -rf ./vendor && \
    composer self-update && \
    composer install --no-dev && \
    mkdir -p -m 0755 html/sites/default && \
    cp -a docker/settings.php docker/services.yml html/sites/default

FROM public.ecr.aws/unocha/php-k8s:8.2-stable

RUN apk add -U --no-cache php82-pecl-uploadprogress

ARG VCS_REF
ARG VCS_URL
ARG BUILD_DATE
ARG GITHUB_ACTOR
ARG GITHUB_REPOSITORY
ARG GITHUB_SHA
ARG GITHUB_REF

ENV  NGINX_SERVERNAME=www.kattekrab.net
ENV  PHP_ENVIRONMENT=production
ENV  PHP_MEMORY_LIMIT=256M
ENV  PHP_MAX_CHILDREN=16
ENV  GIT_BLAME=$GITHUB_ACTOR
ENV  GIT_REPO=$GITHUB_REPOSITORY
ENV  GIT_SHA=$GITHUB_SHA
ENV  GIT_REF=$GITHUB_REF
LABEL net.kattekrab.build.date=$BUILD_DATE \
      net.kattekrab.build.vcs-url=$VCS_URL \
      net.kattekrab.build.vcs-ref=$VCS_REF

COPY --from=builder /srv/www/html /srv/www/html/
COPY --from=builder /srv/www/vendor /srv/www/vendor/
COPY --from=builder /srv/www/composer.json /srv/www/composer.json
COPY --from=builder /srv/www/composer.patches.json /srv/www/composer.patches.json
COPY --from=builder /srv/www/composer.lock /srv/www/composer.lock
