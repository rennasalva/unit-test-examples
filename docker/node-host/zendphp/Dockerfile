ARG OS=debian
ARG OS_VERSION=11
ARG ZENDPHP_VERSION=7.4
ARG BASE_IMAGE=fpm
FROM cr.zend.com/zendphp/${ZENDPHP_VERSION}:${OS}-${OS_VERSION}-${BASE_IMAGE}

# Customizations
ARG TIMEZONE=UTC
ARG INSTALL_COMPOSER=false
ARG SYSTEM_PACKAGES
ARG ZEND_EXTENSIONS_LIST
ARG PECL_EXTENSIONS_LIST
ARG POST_BUILD_BASH
ARG ZENDPHP_REPO_USERNAME
ARG ZENDPHP_REPO_PASSWORD

## Prepare tzdata
ENV TZ=$TIMEZONE

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone


RUN apt-get update && \
 apt-get install -y openssh-server telnet supervisor zip sudo && \
 mkdir -p  /var/run/sshd
COPY  ./.docker/supervisord/supervisord.conf /etc/supervisord.conf



USER root
RUN mkdir -p /entrypoint.d/jenkins/ssh  /entrypoint.d/jenkins/scripts 
COPY ./.docker/ssh/remote-key.pub /entrypoint.d/jenkins/ssh/remote-key.pub
COPY --chmod=0755  ./.docker/bin/*.sh /entrypoint.d/jenkins/scripts


COPY ./.docker/conf/fpm/zendhq.ini /entrypoint.d/zendhq.ini
COPY ./.docker/conf/fpm/www.conf /entrypoint.d/php-fpm.d/www.conf
COPY ./.docker/conf/fpm/worker.conf /entrypoint.d/php-fpm.d/worker.conf
COPY ./.docker/conf/fpm/99-site.ini /entrypoint.d/conf.d/99-site.ini

COPY ./.docker/post-build.sh /usr/local/sbin/post-build.sh

RUN ZendPHPCustomizeWithBuildArgs.sh


ENTRYPOINT ["/usr/bin/supervisord","--configuration=/etc/supervisord.conf"]