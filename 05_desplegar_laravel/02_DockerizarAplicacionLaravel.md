# Dockerizar una aplicación Laravel

## Crear `Dockerfile`

### Crear un archivo `.dockerignore`
**Los archivos que no son necesarios para la creación de la imagen se deben incluir en el archivo `.dockerignore`.**
<br>Crear el archivo `.dockerignore` tiene varios beneficios, como reducir el tiempo de creación y disminuir el tamaño de la imagen de Docker.

Cree un archivo `.dockerignore` en el directorio raíz del proyecto y pegue lo siguiente.
```
# Sistema operativo / Editor
.DS_Store
Thumbs.db
.vscode/
.idea/

# Git
.git
.gitignore

# Laravel
/node_modules
/vendor
/storage/*.key
/storage/debugbar
.env
.env.*    # Excluir archivos de entorno de desarrollo
.phpunit.result.cache

# npm / Vue
npm-debug.log*
yarn-error.log*
dist/
*.log

# Caché / Artefactos de compilación
*.cache
*.tmp
*.swp

# Otros
ordersdb.sql
```

### Crear `Dockerfile` de Laravel
En primer lugar, consideremos los pasos para configurar directamente el entorno de ejecución de Laravel en una máquina Linux.

Estos pasos se traducen en el siguiente `Dockerfile`.
```Dockerfile
# 1. Imagen de base
FROM php:8.3-fpm-alpine

# 2. Paquetes necesarios
RUN apk add --no-cache \
    bash \
    curl \
    unzip \
    git \
    zip \
    libpng-dev \
    libxml2-dev \
    oniguruma-dev \
    build-base \
    nodejs \
    npm \
    && apk add --no-cache --virtual .build-deps $PHPIZE_DEPS

# 3. Extensiones PHP
RUN docker-php-ext-install pdo pdo_mysql mbstring exif pcntl bcmath gd \
    && docker-php-source delete \
    && apk del .build-deps

# 4. Instalación de Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# 5. Copiar código de aplicación
WORKDIR /var/www
COPY . .

# 6. Instalación de Composer (producción) y eliminación de archivos innecesarios
RUN composer install --optimize-autoloader --no-dev --prefer-source

# 7. Compilación de Node.js y limpieza
RUN npm install && npm run build

# 8. Establecer permisos para Laravel
RUN chown -R www-data:www-data /var/www/storage /var/www/bootstrap/cache

# 9. Comandos de inicio de la aplicación Laravel
CMD ["php", "artisan", "serve", "--host=0.0.0.0", "--port=8000"]
```


## Crear una imagen e iniciar un contenedor localmente

```
docker image build -t laravel-app:v1.0 .
```
```
docker container run --rm \
  --name laravel-app \
  -p 8000:8000 \
  -e APP_NAME=Laravel \
  -e APP_ENV=local \
  -e APP_KEY=★la clave★ \
  -e APP_DEBUG=true \
  -e APP_URL=http://localhost \
  -e DB_CONNECTION=mysql \
  -e DB_HOST=★su TiDB HOST★ \
  -e DB_PORT=4000 \
  -e DB_DATABASE=test \
  -e DB_USERNAME=★su TiDB USERNAME★ \
  -e DB_PASSWORD=★su TiDB USERNAME★ \
  -e MYSQL_ATTR_SSL_CA_B64=★su certificado CA codificado en Base64★ \
  laravel-app:v1.0
```
## Publicar la imagen en Docker Hub

https://hub.docker.com/
