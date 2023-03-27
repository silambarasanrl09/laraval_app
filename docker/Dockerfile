FROM httpd:2.4

# Disable frontend dialogs
ENV DEBIAN_FRONTEND noninteractive

# Define other variables
ENV APACHE_DOCUMENT_ROOT /var/www/html/
ENV PHP_VERSION 7.3

# Configure PHP package repo
RUN apt-get update && apt-get -y install ca-certificates apt-transport-https wget gnupg curl cron nano && \
    wget -q https://packages.sury.org/php/apt.gpg -O- | apt-key add - && \
    echo "deb https://packages.sury.org/php/ buster main" | tee /etc/apt/sources.list.d/php.list && \
    apt-get update
    
# Install basic PHP packages
RUN apt-get install -y \
    libapache2-mod-php${PHP_VERSION} \
    php${PHP_VERSION}-apcu \
    php${PHP_VERSION}-bcmath \
    php${PHP_VERSION}-bz2 \
    php${PHP_VERSION}-curl \
    php${PHP_VERSION}-cli \
    php${PHP_VERSION}-gettext \
    php${PHP_VERSION}-gd \
    php${PHP_VERSION}-intl \
    php${PHP_VERSION}-json \
    php${PHP_VERSION}-mbstring \
    php${PHP_VERSION}-memcached \
    php${PHP_VERSION}-mysql \
    php${PHP_VERSION}-opcache \
    php${PHP_VERSION}-soap \
    php${PHP_VERSION}-sqlite3 \
    php${PHP_VERSION}-tidy \
    php${PHP_VERSION}-xml \
    php${PHP_VERSION}-xmlrpc \
    php${PHP_VERSION}-xsl \
    php${PHP_VERSION}-zip \
    zip \
    unzip

# Tweak apache settings
COPY ./php.conf /usr/local/apache2/conf/extra/php.conf
RUN mkdir -p ${APACHE_DOCUMENT_ROOT}public \
    && sed -ri -e 's!/usr/local/apache2/htdocs!${APACHE_DOCUMENT_ROOT}public!g' /usr/local/apache2/conf/httpd.conf \
    && sed -ri -e 's/LoadModule mpm_event_module/#LoadModule mpm_event_module/g' /usr/local/apache2/conf/httpd.conf \
    && sed -ri -e 's/#LoadModule mpm_prefork_module/LoadModule mpm_prefork_module/g' /usr/local/apache2/conf/httpd.conf \
    && sed -ri -e 's/#LoadModule deflate_module/LoadModule deflate_module/g' /usr/local/apache2/conf/httpd.conf \
    && sed -ri -e 's/#LoadModule rewrite_module/LoadModule rewrite_module/g' /usr/local/apache2/conf/httpd.conf \
    && sed -ri -e 's/#LoadModule expires_module/LoadModule expires_module/g' /usr/local/apache2/conf/httpd.conf \
    && echo 'LoadModule php7_module /usr/lib/apache2/modules/libphp${PHP_VERSION}.so' | tee -a /usr/local/apache2/conf/httpd.conf \
    && echo 'Include conf/extra/php.conf' | tee -a /usr/local/apache2/conf/httpd.conf \
    && echo "<Directory \"\${APACHE_DOCUMENT_ROOT}public\">\n\tAllowOverride All\n</Directory>" | tee -a /usr/local/apache2/conf/httpd.conf

#Tweak PHP settings
#RUN sed -ri -e 's!memory_limit = 128M!memory_limit = 512M!g' /etc/php/${PHP_VERSION}/apache2/php.ini \
#    && sed -ri -e 's!post_max_size = 8M!post_max_size = 128M!g' /etc/php/${PHP_VERSION}/apache2/php.ini \
#    && sed -ri -e 's!upload_max_filesize = 2M!upload_max_filesize = 128M!g' /etc/php/${PHP_VERSION}/apache2/php.ini \
#    && sed -ri -e 's!;pcre.jit=1!pcre.jit=0!g' /etc/php/${PHP_VERSION}/apache2/php.ini \
#    && sed -ri -e 's!memory_limit = 128M!memory_limit = 512M!g' /etc/php/${PHP_VERSION}/cli/php.ini \
#    && sed -ri -e 's!post_max_size = 8M!post_max_size = 128M!g' /etc/php/${PHP_VERSION}/cli/php.ini \
#    && sed -ri -e 's!upload_max_filesize = 2M!upload_max_filesize = 128M!g' /etc/php/${PHP_VERSION}/cli/php.ini \
#    && sed -ri -e 's!;pcre.jit=1!pcre.jit=0!g' /etc/php/${PHP_VERSION}/cli/php.ini \
#    && sed -ri -e 's!;gd.jpeg_ignore_warning = 1!gd.jpeg_ignore_warning = 1!g' /etc/php/${PHP_VERSION}/cli/php.ini
COPY ./opcache.ini /etc/php/${PHP_VERSION}/mods-available/opcache.ini

RUN mkdir -p ${APACHE_DOCUMENT_ROOT}

WORKDIR ${APACHE_DOCUMENT_ROOT}


COPY  ./custom-httpd-foreground /usr/local/bin/httpd-foreground

RUN chmod +x /usr/local/bin/httpd-foreground
