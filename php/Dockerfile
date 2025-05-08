FROM php:7.4-fpm-buster

ARG GIT_USER_NAME
ARG GIT_USER_EMAIL
ARG GIT_CORE_EDITOR
ARG UID=1000
ARG GID=1000

# Установка зависимостей и расширений
COPY --from=mlocati/php-extension-installer /usr/bin/install-php-extensions /usr/bin/
RUN install-php-extensions intl gettext gd pdo_mysql zip bcmath imagick

COPY --from=composer /usr/bin/composer /usr/bin/composer

RUN mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"

ENV DEBIAN_FRONTEND=noninteractive

# Базовые пакеты и wkhtmltopdf
RUN echo "deb http://http.debian.net/debian/ buster main contrib non-free" > /etc/apt/sources.list && \
    echo "deb http://http.debian.net/debian/ buster-updates main contrib non-free" >> /etc/apt/sources.list && \
    echo "deb http://security.debian.org/ buster/updates main contrib non-free" >> /etc/apt/sources.list && \
    apt-get update && \
    apt-get install --no-install-recommends -y \
    openssl git openssh-client nano build-essential curl xz-utils ca-certificates \
    xfonts-100dpi xfonts-75dpi xfonts-base fonts-roboto fonts-inconsolata \
    ttf-mscorefonts-installer fontconfig xorg libjpeg62-turbo && \
    curl -L https://github.com/wkhtmltopdf/packaging/releases/download/0.12.6-1/wkhtmltox_0.12.6-1.buster_amd64.deb -o wkhtmltox.deb && \
    dpkg -i wkhtmltox.deb && \
    fc-cache -f -v && \
    apt-get purge -y curl && \
    apt-get autoremove -y && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /var/tmp/* /tmp/* wkhtmltox.deb

# Создание пользователя amr
RUN groupadd -g $GID amr && useradd -m -u $UID -g $GID -s /bin/bash amr

# Настройка git
USER amr
ENV HOME=/home/amr
RUN git config --global user.name "$GIT_USER_NAME" && \
    git config --global user.email "$GIT_USER_EMAIL" && \
    git config --global core.editor "$GIT_CORE_EDITOR" && \
    git config --global init.defaultBranch master && \
    git config --global --add safe.directory /var/www/html
