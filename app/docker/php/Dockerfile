FROM php:7.4-fpm

# Устанавливаем рабочую директорию 
WORKDIR /var/www/app

# Копируем composer.lock и composer.json
COPY ../../app/composer.lock ../../src/composer.json /var/www/app

# Устанавливаем зависимости
RUN apt-get update && apt-get install -y \
    build-essential \
    libpng-dev \
    libjpeg62-turbo-dev \
    libfreetype6-dev \
    locales \
    zip \
    jpegoptim optipng pngquant gifsicle \
    vim \
    unzip \
    git \
    curl \
    libpq-dev \
    libonig-dev \
    libzip-dev \
    npm \
    libxml2-dev \
    libgd-dev

# Очищаем кэш
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Устанавливаем расширения PHP
RUN docker-php-ext-install pdo_mysql mbstring zip exif pcntl soap gd

#Устанавливаем расширение mongodb
RUN pecl install mongodb && docker-php-ext-enable mongodb

# Загружаем актуальную версию Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Создаём пользователя и группу www для приложения Laravel
RUN groupadd -g 1000 admin
RUN useradd -u 1000 -ms /bin/bash -g admin admin

# Копируем содержимое текущего каталога в рабочую директорию
COPY ./kgz /var/www/app
COPY --chown=admin:admin ../../src /var/www/app

#Создание папки для модулей npm
RUN mkdir /var/www/node_modules && chown -R admin:admin /var/www/node_modules

# Меняем пользователя на www
USER admin

#Устанавливаем библиотеки npm
RUN npm install

#Устанавливаем библиотеки php
RUN composer install --no-interaction --no-plugins --no-scripts

# В контейнере открываем 9000 порт и запускаем сервер php-fpm
EXPOSE 9000
CMD ["php-fpm"]
