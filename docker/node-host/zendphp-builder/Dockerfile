ARG OS=debian
ARG OS_VERSION=11
ARG ZENDPHP_VERSION=7.4
ARG BASE_IMAGE=cli
FROM cr.zend.com/zendphp/${ZENDPHP_VERSION}:${OS}-${OS_VERSION}-${BASE_IMAGE}

# Customizations
ARG TIMEZONE=UTC
ARG INSTALL_COMPOSER=true
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
 apt-get install -y openssh-server telnet supervisor
COPY  ./.docker/supervisord/supervisord.conf /etc/supervisord.conf


USER root
RUN groupadd --system --gid 10003 remote_user && \
    useradd -rm -d /home/remote_user -s /bin/bash -g root -G sudo -u 10003 remote_user && \
    echo "remote_user:1234" | chpasswd && \
    mkdir /home/remote_user/.ssh -p && \
    chmod 700 /home/remote_user/.ssh && \
    mkdir -p  /var/run/sshd

COPY ./.docker/ssh/remote-key.pub /home/remote_user/.ssh/authorized_keys

RUN chown remote_user:remote_user   -R /home/remote_user && \
    chmod 400 /home/remote_user/.ssh/authorized_keys

RUN ssh-keygen -A && rm -rf /run/nologin


RUN set -e; \
echo "zend_extension=xdebug.so"  >> /etc/php/7.4-zend/cli/conf.d/20-xdebug.ini; \
echo "xdebug.mode=develop,debug,coverage" >> /etc/php/7.4-zend/cli/conf.d/20-xdebug.ini;
#     echo "xdebug.start_with_request = trigger" >> /etc/php/7.4-zend/cli/conf.d/20-xdebug.ini; \
#     echo "xdebug.trigger_value = PHPSTORM" >> /etc/php/7.4-zend/cli/conf.d/20-xdebug.ini; \
#     echo "xdebug.log = /tmp/xdebug.log" >> /etc/php/7.4-zend/cli/conf.d/20-xdebug.ini; \
#     echo "xdebug.log_level = 0" >> /etc/php/7.4-zend/cli/conf.d/20-xdebug.ini; \
#     echo "xdebug.client_host = host.docker.internal" >> /etc/php/7.4-zend/cli/conf.d/20-xdebug.ini; \
#     echo "xdebug.discover_client_host = 0" >> /etc/php/7.4-zend/cli/conf.d/20-xdebug.ini;\
#     echo "xdebug.client_port = 9003" >> /etc/php/7.4-zend/cli/conf.d/20-xdebug.ini; \
#     echo "xdebug.idekey =PHPSTORM" >> /etc/php/7.4-zend/cli/conf.d/20-xdebug.ini;

# RUN set -e; 
#     sed -i "s/xdebug.mode= .*/xdebug.mode=develop,debug,coverage/" /etc/php/7.4/apache2/php.ini 


RUN ZendPHPCustomizeWithBuildArgs.sh


CMD ["php" "-a"]