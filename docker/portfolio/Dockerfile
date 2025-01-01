FROM php:8.3-fpm-bookworm

ARG RUNNING_GROUP
ARG RUNNING_USER
ENV RUNNING_GROUP=$RUNNING_GROUP
ENV RUNNING_USER=$RUNNING_USER

RUN groupadd -r web --gid=${RUNNING_GROUP}
RUN useradd -r -m -g web --uid=${RUNNING_USER} web

# Get repository and install wget and vim
RUN apt-get update && apt-get install --no-install-recommends -y \
        mailutils \
        wget \
        vim \
        git \
        unzip \
        gnupg \
        curl \
        apt-transport-https \
        acl

# Add PostgreSQL repository
RUN echo "deb http://apt.postgresql.org/pub/repos/apt/ bookworm-pgdg main" > /etc/apt/sources.list.d/pgdg.list
RUN wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | \
      apt-key add -

RUN curl -sL https://deb.nodesource.com/setup_22.x | bash -

RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - && \
    echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list

ENV ACCEPT_EULA=Y

# Install PHP extensions deps
RUN apt-get update \
    && apt-get install --no-install-recommends -y \
        procps \
        supervisor \
        nodejs \
        yarn  \
        postgresql-server-dev-all \
        libonig-dev \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        zlib1g-dev \
        libicu-dev \
        g++ \
        libxml2-dev \
        libaio-dev \
        libmemcached-dev \
        openssl \
        libzip-dev \
        libxslt-dev

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- \
        --install-dir=/usr/local/bin \
        --filename=composer

# Install PHP extensions
RUN docker-php-ext-configure gd --enable-gd --with-freetype --with-jpeg
RUN pecl install redis
RUN pecl install memcached
RUN docker-php-ext-install \
        iconv \
        mbstring \
        intl \
        gd \
        pgsql
RUN docker-php-ext-install \
        pdo_pgsql \
        soap \
        zip \
        pcntl
RUN docker-php-ext-install \
        ftp \
        bcmath \
        xsl \
        exif
RUN docker-php-ext-enable \
            redis \
            memcached \
            opcache

# workaround
RUN CFLAGS="$CFLAGS -D_GNU_SOURCE" docker-php-ext-install sockets

# Install APCu and APC backward compatibility
RUN pecl install apcu \
    && docker-php-ext-enable apcu --ini-name 20-docker-php-ext-apcu.ini \
    && pecl install xdebug \
    && docker-php-ext-enable xdebug --ini-name 30-docker-php-ext-xdebug.ini

# Clean repository
RUN apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Set user permissions for var
RUN setfacl -dR -m u:"$RUNNING_USER":rwX -m u:$(whoami):rwX /var
RUN setfacl -R -m u:"$RUNNING_USER":rwX -m u:$(whoami):rwX /var

CMD ["/usr/bin/supervisord","--configuration=/etc/supervisor/supervisord.conf"]
