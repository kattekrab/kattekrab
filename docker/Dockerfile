FROM unocha/php7-k8s:7.3.14-r0-202003-02 as builder

COPY . /srv/www
WORKDIR /srv/www
RUN rm -rf ./vendor && \
    composer install --no-dev && \
    cp -a docker/settings.php docker/services.yml html/sites/default

FROM unocha/php7-k8s:7.3.14-r0-202003-02

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
COPY --from=builder /srv/www/docker/fastcgi_drupal.conf /etc/nginx/apps/drupal/fastcgi_drupal.conf
