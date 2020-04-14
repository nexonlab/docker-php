FROM php:7.3-apache-stretch

COPY configs/oracle /opt/oracle/
COPY configs/000-default.conf /etc/apache2/sites-available/000-default.conf

ENV DEBIAN_FRONTEND noninteractive
RUN apt-get update && apt-get install --no-install-recommends -y \
    curl \
    wget \
    git \
    vim \
    tzdata \
    zip \
    unzip \
    gnupg \
    openssl \
    apt-transport-https \
    build-essential \
    make \
    unixodbc \
    unixodbc-dev \
    ca-certificates \
    locales \
    sqlite3 \
    ffmpeg \
    freetds-dev \
    libpq-dev \
    libicu-dev \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libmcrypt-dev \
    libpng-dev \
    libxslt1-dev \
    libldb-dev \
    libaio1 \
    libldap2-dev \
    smbclient \
    libsmbclient-dev \
    libgmp3-dev \
    libmagickcore-6.q16-3-extra \
    zlibc \
  ## Config TimeZone
  && ln -fs /usr/share/zoneinfo/America/Fortaleza /etc/localtime && dpkg-reconfigure -f noninteractive tzdata \
  && LANG=pt_BR.UTF-8 \
  ## Installing SQL Server Libs
  && curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add - \
  && curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > /etc/apt/sources.list.d/mssql-release.list \
  && apt-get update \
  && ACCEPT_EULA=Y apt-get install -y msodbcsql17 mssql-tools \
  && echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile \
  && echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc \
  && pecl install sqlsrv pdo_sqlsrv \
  && echo "extension=pdo_sqlsrv.so" >> `php --ini | grep "Scan for additional .ini files" | sed -e "s|.*:\s*||"`/30-pdo_sqlsrv.ini \
  && echo "extension=sqlsrv.so" >> `php --ini | grep "Scan for additional .ini files" | sed -e "s|.*:\s*||"`/20-sqlsrv.ini \
  && echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && locale-gen
ENV DEBIAN_FRONTEND teletype

# Installing relational database libs and other related libs.
RUN docker-php-ext-install -j$(nproc) pgsql pdo_pgsql pdo_mysql ldap xsl gettext \
 && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
 && docker-php-ext-install -j$(nproc) gd intl \
 && pecl install mcrypt \
 && docker-php-ext-enable mcrypt \
 && docker-php-ext-install gmp \
 && pecl install smbclient \
 && docker-php-ext-enable smbclient

# Installing Oracle Libs
RUN gunzip /opt/oracle/instantclient_12_2/*.gz
RUN ln /opt/oracle/instantclient_12_2/libclntsh.so.12.1 /opt/oracle/instantclient_12_2/libclntsh.so \
 && ln /opt/oracle/instantclient_12_2/libocci.so.12.1 /opt/oracle/instantclient_12_2/libocci.so \
 && echo /opt/oracle/instantclient_12_2 > /etc/ld.so.conf.d/oracle-instantclient.conf \
 && ldconfig \
 && echo "instantclient,/opt/oracle/instantclient_12_2" | pecl install oci8 \
 && docker-php-ext-configure pdo_oci \
    --with-pdo-oci=instantclient,/opt/oracle/instantclient_12_2,12.2 \
 && docker-php-ext-install pdo_oci \
 && docker-php-ext-configure pdo_dblib --with-libdir=/lib/x86_64-linux-gnu \
 && docker-php-ext-install pdo_dblib \
 && docker-php-ext-enable oci8 pdo_oci pdo_dblib

# Cleaning up
RUN apt-get autoremove -y \
 && apt-get autoclean -y \
 && apt-get clean -y \
 && rm -rf /tmp/*

# Installing Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
 && chmod +x /usr/local/bin/composer

# Installing PHP Tools
RUN composer global require phpunit/phpunit \
 && composer global require squizlabs/php_codesniffer \
 && composer global require friendsofphp/php-cs-fixer \
 && composer global require phpmd/phpmd \
 && composer global require behat/behat \
 && composer global require phpstan/phpstan

RUN a2enmod rewrite && service apache2 restart

EXPOSE 80

WORKDIR /var/www/app

EXPOSE 80
