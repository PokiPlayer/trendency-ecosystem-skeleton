ARG php_version=7.4

FROM php:${php_version}-fpm as base-php

ARG php_version=7.4
ARG project_name=''
ARG current_service=''
ARG project_root=../../ecosystem/${current_service}/backend
ARG app_env=dev
ARG env=local
ARG xdebug_enable=0
ARG uid=1000
ARG gid=1000
ARG uname=appuser
ARG db_user='dbuser'
ARG db_password='qwertz'
ARG db_name='trendency'
ARG db_host='database'
ARG db_dsn='mysql://trendency:jegcsap21@database:3306/trendencyserverVersion=5.7'
ARG redis_chema='http'
ARG redis_host='redis'
ARG redis_port='6379'
ARG redis_passwrod='qwertz'
ARG redis_database='1'
ARG redis_dsn='redis://redis'
ARG rabbitmq_host='rabbitmq'
ARG rabbitmq_port='5672'
ARG rabbitmq_password='quest'
ARG rabbitmq_user='quest'

ENV PROJECT_NAME ${project_name}
ENV CURRENT_SERVICE ${current_service}
ENV APP_HOME /app
ENV APP_ENV ${app_env}
ENV ENV ${env}
ENV XDEBUG_ENABLE ${xdebug_enable}
ENV COMPOSER_MEMORY_LIMIT -1
ENV CONTAINER_USERNAME ${uname}
ENV DB_USER ${db_user}
ENV DB_PASSWORD ${db_password}
ENV DB_NAME ${db_name}
ENV DB_HOST ${db_host}
ENV DATABASE_URL ${db_dsn}
ENV EXT_AMQP_VERSION master
ENV REDIS_SCHEMA ${redis_chema}
ENV REDIS_HOST ${redis_host}
ENV REDIS_PORT ${redis_port}
ENV REDIS_PASSWORD ${redis_passwrod}
ENV REDIS_DATABASE ${redis_database}
ENV REDIS_DSN ${redis_dsn}
ENV RABBITMQ_HOST ${rabbitmq_host}
ENV RABBITMQ_PORT ${rabbitmq_port}
ENV RABBITMQ_PASSWORD ${rabbitmq_password}
ENV RABBITMQ_USER ${rabbitmq_user}

RUN apt-get update && apt-get install -y \
    tini \
    apt-utils \
    sudo \
    procps \
    openssl \
    curl \
    htop \
    netcat \
    net-tools \
    zip \
    unzip \
    wget \
    mc \
    mcedit \
    nano \
    vim \
    gnupg \
    cron \
    librabbitmq-dev \
    build-essential \
    imagemagick \
    libmagickwand-dev \
    libmagickcore-dev \
    libmemcached-dev \
    libssh-dev \
    libssl-dev \
    zlib1g-dev \
    libicu-dev \
    libpng-dev \
    libjpeg-dev \
    g++ \
    libnss3-dev \
    libxslt-dev \
    libpng-dev \
    libjpeg-dev \
    libjpeg62-turbo-dev \
    acl

# Install spec php extensions
RUN pecl install redis && docker-php-ext-enable redis && \
    pecl install apcu && docker-php-ext-enable apcu && \
    pecl install igbinary && docker-php-ext-enable igbinary && \
    pecl install memcached && docker-php-ext-enable memcached && \
    pecl install imagick && docker-php-ext-enable imagick

# Install php8 compatibile amqp php extension
RUN if [ "${php_version}" = '8.0' ]; then \
    docker-php-source extract && \
    mkdir /usr/src/php/ext/amqp && \
    curl -L https://github.com/php-amqp/php-amqp/archive/master.tar.gz | tar -xzC /usr/src/php/ext/amqp --strip-components=1 && \
    docker-php-ext-install amqp ; \
else \
     pecl install amqp && docker-php-ext-enable amqp ; \
fi

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN composer --version

COPY ./conf.d/* /usr/local/etc/mods-available/

# Xdebug install
RUN if [ "${xdebug_enable}" = 1 ]; then \
    pecl install xdebug-3.0.4 && docker-php-ext-enable xdebug && \
    cp /usr/local/etc/mods-available/xdebug.ini /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini ; \
fi

WORKDIR ${APP_HOME}

# Container user creation
RUN echo 'alias sf="php /app/bin/console"' >> /root/.bashrc
RUN chmod g+wx /var/log/ && \
    chmod g+wx /opt/
RUN groupadd -g $gid -o $uname
RUN useradd -m -u $uid -g $gid -o -s /bin/bash $uname | chpasswd && adduser $uname sudo

RUN if [ "${env}" != 'prod' ]; then \
  echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers; \
fi

USER $uname

RUN echo 'alias sf="php /app/bin/console"' >> /home/${uname}/.bashrc

EXPOSE 9000


FROM base-php as php-worker

RUN sudo apt-get install -y \
    supervisor

COPY ./supervisor/supervisord.conf /etc/supervisor/supervisord.conf

CMD ["sudo", "/usr/bin/supervisord", "-c", "/etc/supervisor/supervisord.conf"]
