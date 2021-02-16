FROM node:14-buster as node
FROM php:7.4-apache-buster
LABEL maintainer="Erioquest"
SHELL ["/bin/bash", "-oeux", "pipefail", "-c"]

# timezone environment
ENV TZ=UTC \
  # locale
  LANG=en_US.UTF-8 \
  LANGUAGE=en_US:en \
  LC_ALL=en_US.UTF-8 \
  # composer environment
  COMPOSER_ALLOW_SUPERUSER=1 \
  COMPOSER_HOME=/composer

# composer command
COPY --from=composer:1.8.5 /usr/bin/composer /usr/bin/composer
# node command
COPY --from=node /usr/local/bin /usr/local/bin
# npm command
COPY --from=node /usr/local/lib /usr/local/lib
# yarn command
COPY --from=node /opt /opt

RUN apt-get update && \
  apt-get -y install vim git redis-server curl wget \
                                libicu-dev libonig-dev libjpeg-dev libpng-dev libwebp-dev libfreetype6-dev libzip-dev libbz2-dev unzip locales \
                                xserver-xorg x11-apps \
                                gtk2-engines-pixbuf \
                                chromium  xorg xvfb gtk2-engines-pixbuf \
								fonts-liberation libappindicator3-1 libasound2 libatk-bridge2.0-0 libgtk-3-0 libnspr4 libnss3 libx11-xcb1 libxss1 libxtst6 xdg-utils \
                                dbus-x11 xfonts-base xfonts-100dpi xfonts-75dpi xfonts-cyrillic xfonts-scalable && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/* && \
  locale-gen en_US.UTF-8 && \
  localedef -f UTF-8 -i en_US en_US.UTF-8 && \
  a2enmod rewrite && \
  docker-php-ext-install intl pdo_mysql zip bcmath gd pcntl sockets mbstring bz2 && \
  composer config -g process-timeout 3600 && \
  composer config -g repos.packagist composer https://packagist.org

# install chrome
RUN wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
RUN dpkg -i google-chrome-stable_current_amd64.deb; apt-get -fy install

COPY ./infra/docker/php/php.ini /usr/local/etc/php/php.ini
COPY ./infra/docker/apache/httpd.conf /etc/apache2/sites-available/000-default.conf

WORKDIR /work/backend
