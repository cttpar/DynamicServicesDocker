FROM php:7.1-apache

MAINTAINER ramity "github.com/ramity"

# configure php
COPY ./config/php.ini /usr/local/etc/php/

# configure apache
COPY ./config/apache.conf /etc/apache2/sites-enabled/000-default.conf
COPY ./config/ports.conf /etc/apache2/ports.conf


# set up directory
WORKDIR /var/www/cttp-symfony-application/

# install packages
RUN apt-get update -y
RUN apt-get install -y nano
RUN apt-get install -y curl
RUN apt-get install -y wget
RUN apt-get install -y zip
RUN apt-get install -y unzip
RUN apt-get install -y libldap2-dev

# install php extensions
RUN docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/
RUN docker-php-ext-install ldap
RUN docker-php-ext-install mysqli
RUN docker-php-ext-install opcache
RUN docker-php-ext-install pdo_mysql
RUN docker-php-ext-install pcntl

# install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# install pecl extension
RUN pecl install apcu
RUN echo "extension=apcu.so" > /usr/local/etc/php/conf.d/apcu.ini

# remove default wkhtmltopdf
RUN apt-get remove -y libqt4-dev
RUN apt-get remove -y qt4-dev-tools
RUN apt-get remove -y wkhtmltopdf
RUN apt-get autoremove -y

# install wkhtmltopdf + deps
RUN apt-get install -y openssl
RUN apt-get install -y build-essential
RUN apt-get install -y libjpeg62-turbo
RUN apt-get install -y xfonts-base
RUN apt-get install -y xfonts-75dpi
RUN apt-get install -y libssl-dev
RUN apt-get install -y libxrender-dev
RUN apt-get install -y git-core
RUN apt-get install -y libx11-dev
RUN apt-get install -y libxext-dev
RUN apt-get install -y libfontconfig1-dev
RUN apt-get install -y libfreetype6-dev
RUN apt-get install -y fontconfig
RUN mkdir /var/wkhtmltopdf
RUN cd /var/wkhtmltopdf && wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox_0.12.5-1.stretch_amd64.deb
RUN dpkg -i /var/wkhtmltopdf/wkhtmltox_0.12.5-1.stretch_amd64.deb && apt-get install -f
RUN cp /usr/local/bin/wkhtmltopdf /bin/wkhtmltopdf
RUN cp /usr/local/bin/wkhtmltoimage /bin/wkhtmltoimage
RUN chmod +x /bin/wkhtmltopdf && chmod +x /bin/wkhtmltoimage

# Install arc
RUN cd /var && git clone https://github.com/phacility/libphutil.git
RUN cd /var && git clone https://github.com/phacility/arcanist.git

# Add utils to path
ENV PATH="/var/arcanist/bin:${PATH}"
ENV PATH="/var/www/cttp-symfony-application/bin:${PATH}"

# Expose apache port
EXPOSE 8000
CMD chown www-data:www-data -R /var/www; a2enmod rewrite; apache2-foreground
