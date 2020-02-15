FROM php:7.3-apache-stretch

COPY 000-default.conf /etc/apache2/sites-available/000-default.conf

RUN useradd -m -d /home/markteam markteam \
 && usermod -a -G root markteam \
 && usermod -a -G www-data markteam \
 && chown -R markteam:root /var/www/ \
 && chown -R markteam:root /usr/local/ \
 && apt-get update \
 && apt-get install --no-install-recommends -y sudo \
 && echo "markteam ALL=(root) NOPASSWD:ALL" > /etc/sudoers.d/markteam \
 && chmod 0440 /etc/sudoers.d/markteam

ENV DEBIAN_FRONTEND noninteractive
RUN apt-get update \
    && apt-get install --no-install-recommends -y \
        curl \
        wget \
        git \
        vim \
        nano \
        tzdata \
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
    && ln -fs /usr/share/zoneinfo/America/Fortaleza /etc/localtime && dpkg-reconfigure -f noninteractive tzdata \
    && LANG=pt_BR.UTF-8 \
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

RUN echo "---> Cleaning up" \
 && apt-get autoremove -y \
 && apt-get autoclean -y \
 && apt-get clean -y \
 && rm -rf /tmp/*

RUN a2enmod rewrite && service apache2 restart
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]

USER markteam

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

# Install PHP XDebug
RUN yes | pecl install xdebug \
 && echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini \
 && echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/xdebug.ini \
 && echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/xdebug.ini

WORKDIR /var/www/app

EXPOSE 80
