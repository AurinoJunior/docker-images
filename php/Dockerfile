FROM php:7.4-apache

RUN a2enmod rewrite

# Atualiza a lista de pacotes e instala as dependências do PHP
RUN apt-get update && apt-get install -y \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    libzip-dev \
    unzip \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd mysqli pdo pdo_mysql zip

COPY . /var/www/html

WORKDIR /var/www/html

EXPOSE 80

# Inicia o servidor Apache
CMD ["apache2-foreground"]
