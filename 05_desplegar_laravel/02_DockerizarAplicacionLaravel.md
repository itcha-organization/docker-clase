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



## Crear una imagen
```
docker image build -t laravel-app:v1.0 .
```

## Ejectar un contenedor localmente con Docker Compose
Docker Compose es una herramienta que permite gestionar varios contenedores de forma conjunta.

Todos los contenedores que se van a iniciar y la información de las opciones se pueden describir en un único archivo, `docker-compose.yml`, lo que simplifica la ejecución, ya que se ahorra el trabajo de introducir comandos y se evitan errores debidos a errores tipográficos.

**En esta ocasión solo hay un contenedor, pero hay una gran cantidad de variables de entorno.
Por lo tanto, como el uso de `container run` alarga la sección de opciones, definiremos las opciones en `docker-compose.yml`.**

Cree un archivo `docker-compose.yml` en el directorio raíz del proyecto y pegue lo siguiente.
<br>**Los valores marcados con un símbolo ★ deben ser configurados por usted mismo.**
```yaml
services:
    app:
        build:
            context: .
            dockerfile: Dockerfile
        container_name: laravel-app
        ports:
            - "8000:8000"
        environment:
            - APP_NAME=Laravel
            - APP_ENV=local
            - APP_KEY=★sobrescríbase usted mismo★
            - APP_DEBUG=true
            - APP_TIMEZONE=UTC
            - APP_URL=http://localhost
            - APP_LOCALE=en
            - APP_FALLBACK_LOCALE=en
            - APP_FAKER_LOCALE=en_US
            - APP_MAINTENANCE_DRIVER=file
            - PHP_CLI_SERVER_WORKERS=4
            - BCRYPT_ROUNDS=12
            - LOG_CHANNEL=stack
            - LOG_STACK=single
            - LOG_DEPRECATIONS_CHANNEL=null
            - LOG_LEVEL=debug
            - DB_CONNECTION=mysql
            - DB_HOST=★sobrescríbase usted mismo★
            - DB_PORT=4000
            - DB_DATABASE=test
            - DB_USERNAME=★sobrescríbase usted mismo★
            - DB_PASSWORD=★sobrescríbase usted mismo★
            - MYSQL_ATTR_SSL_CA_B64=★sobrescríbase usted mismo★
            - SESSION_DRIVER=database
            - SESSION_LIFETIME=120
            - SESSION_ENCRYPT=false
            - SESSION_PATH=/
            - SESSION_DOMAIN=null
            - BROADCAST_CONNECTION=log
            - FILESYSTEM_DISK=local
            - QUEUE_CONNECTION=database
            - CACHE_STORE=database
            - CACHE_PREFIX=
            - MEMCACHED_HOST=127.0.0.1
            - REDIS_CLIENT=phpredis
            - REDIS_HOST=127.0.0.1
            - REDIS_PASSWORD=null
            - REDIS_PORT=6379
            - MAIL_MAILER=log
            - MAIL_SCHEME=null
            - MAIL_HOST=127.0.0.1
            - MAIL_PORT=2525
            - MAIL_USERNAME=null
            - MAIL_PASSWORD=null
            - MAIL_FROM_ADDRESS="hello@example.com"
            - MAIL_FROM_NAME="${APP_NAME}"
            - AWS_ACCESS_KEY_ID=
            - AWS_SECRET_ACCESS_KEY=
            - AWS_DEFAULT_REGION=us-east-1
            - AWS_BUCKET=
            - AWS_USE_PATH_STYLE_ENDPOINT=false
            - VITE_APP_NAME="${APP_NAME}"
```
## Publicar la imagen en Docker Hub

#### 1️⃣ Crear un repositorio remoto

1. Accede a [hub.docker.com](https://hub.docker.com/).
2. Inicia sesión.
3. Haz clic en **"Create a Repository"**.
4. Ingresa un nombre para el repositorio y selecciona su visibilidad (pública o privada), luego haz clic en **"Create"**.

#### 2️⃣ Iniciar sesión en DockerHub desde tu entorno local
Ejecute el siguiente comando e introduzca la contraseña en el prompt que aparece.
```bash
docker login -u <nombre_de_usuario>
```
Si la autenticación es exitosa, verás el mensaje:
```
Login Succeeded
```
> <img width="808" height="158" alt="image" src="https://github.com/user-attachments/assets/d4cc2756-d35a-4309-b8bf-d1b90a193168" />

#### 3️⃣ Etiquetar la imagen local para asociarla con el repositorio remoto

```bash
docker tag <nombre_imagen_local>:<etiqueta> <nombre_usuario>/<nombre_repositorio>:<etiqueta>
```
* **\<nombre\_imagen\_local>**: Nombre de la imagen en tu entorno local.
* **<etiqueta>**: Etiqueta de la imagen (por ejemplo, `latest`).
* **\<nombre\_usuario>**: Tu nombre de usuario en DockerHub.
* **\<nombre\_repositorio>**: Nombre del repositorio que creaste en DockerHub.
> <img width="1161" height="56" alt="image" src="https://github.com/user-attachments/assets/df2a6398-7a79-4bef-971c-ba9fc96452c5" />

#### 4️⃣ Subir la imagen al repositorio remoto
```bash
docker push <nombre_usuario>/<nombre_repositorio>:<etiqueta>
```
>

Puede ver que la imagen se ha añadido en Docker Hub.
