#Recibimos Argumentos
ARG php_version

FROM php:${php_version}
LABEL  maintainer="ricurdinola@gmail.com"

#Actualizamos paquete e instalamos algunas librerías por defecto.
#Modificar según requerimiento del proyecto
RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
		zip unzip libzip-dev \
		libmcrypt-dev \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install zip
	
#Librería para conexión de Mysql
RUN docker-php-ext-install mysqli pdo pdo_mysql

#Copiamos de la imagen composer a la maquina local.
COPY --from=composer /usr/bin/composer /usr/bin/composer

#Instalamos Node
ENV NODE_VERSION=14.16.0
RUN apt install -y curl
RUN curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
ENV NVM_DIR=/root/.nvm
RUN . "$NVM_DIR/nvm.sh" && nvm install ${NODE_VERSION}
RUN . "$NVM_DIR/nvm.sh" && nvm use v${NODE_VERSION}
RUN . "$NVM_DIR/nvm.sh" && nvm alias default v${NODE_VERSION}
ENV PATH="/root/.nvm/versions/node/v${NODE_VERSION}/bin/:${PATH}"

#Copiamos los archivos de configuración del Apache y PHP
COPY ./config/apache2/apache2.conf /etc/apache2/
COPY ./config/apache2/sites-available/000-default.conf /etc/apache2/sites-available/
COPY ./config/php/ /usr/local/etc/php
RUN a2enmod rewrite

#Seteamos en el path, el directorio de composer
RUN echo 'export PATH="$PATH:$HOME/.composer/vendor/bin"' >> ~/.bashrc

#Seteamos la zona horaria
#RUN cp /usr/share/zoneinfo/America/Argentina/Buenos_Aires /etc/localtime

#Exponemos el puerto 80 y 443
EXPOSE 80 443