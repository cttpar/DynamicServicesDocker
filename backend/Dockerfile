FROM php:7.1-apache

MAINTAINER ramity "github.com/ramity"

# copy files
COPY ./config/php.ini /usr/local/etc/php/
COPY ./config/symfony.conf /etc/apache2/sites-enabled/000-default.conf

# set up directory
WORKDIR /var/www/cttp-symfony-application/

# Install PHP extensions and composer
RUN apt-get update -y
RUN apt-get install nano curl wget git zip unzip libldap2-dev -y
RUN docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/
RUN docker-php-ext-install ldap
RUN docker-php-ext-install mysqli opcache pdo_mysql
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN pecl install apcu
RUN echo "extension=apcu.so" > /usr/local/etc/php/conf.d/apcu.ini

# Install WKHTMLTOPDF - https://goo.gl/ZcB6MM
RUN apt-get update -y
RUN apt-get remove -y libqt4-dev qt4-dev-tools wkhtmltopdf
RUN apt-get autoremove -y
RUN apt-get install -y openssl build-essential libjpeg62-turbo xfonts-base xfonts-75dpi libssl-dev libxrender-dev git-core libx11-dev libxext-dev libfontconfig1-dev libfreetype6-dev fontconfig
RUN mkdir /var/wkhtmltopdf
RUN cd /var/wkhtmltopdf && wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox_0.12.5-1.stretch_amd64.deb
RUN dpkg -i /var/wkhtmltopdf/wkhtmltox_0.12.5-1.stretch_amd64.deb && apt-get install -f
RUN chown -R $USER:$USER /var/wkhtmltopdf
RUN cp /usr/local/bin/wkhtmltopdf /bin/wkhtmltopdf && cp /usr/local/bin/wkhtmltoimage /bin/wkhtmltoimage
RUN chmod +x /bin/wkhtmltopdf && chmod +x /bin/wkhtmltoimage

# Install Arcanist
RUN cd /var && git clone https://github.com/phacility/libphutil.git
RUN cd /var && git clone https://github.com/phacility/arcanist.git

# Add some utils to the path
ENV PATH="/var/arcanist/bin:${PATH}"
ENV PATH="/var/www/bin:${PATH}"

# Expose the default apache port
EXPOSE 80
