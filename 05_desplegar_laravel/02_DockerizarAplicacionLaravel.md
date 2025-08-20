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
/public/build
/public/hot

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

<details>
<summary>Pasos para configurar un entorno de ejecución de una aplicación Laravel & Vue en Linux (sin DB local)</summary>

#### 1. Instalar los paquetes necesarios

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y \
    git unzip curl \
    php-cli php-mbstring php-xml php-bcmath php-curl php-zip \
    composer \
    nodejs npm \
    nginx
```

#### 2. Desplegar el proyecto Laravel

```bash
cd /var/www
sudo git clone https://github.com/your-repo/laravel-vue-app.git
cd laravel-vue-app
```

#### 3. Instala dependencias:

```bash
composer install --no-dev --optimize-autoloader
npm install
npm run build
```

#### 4. Configurar permisos

```bash
sudo chown -R www-data:www-data /var/www/laravel-vue-app/storage /var/www/laravel-vue-app/bootstrap/cache
```

</details>

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
1. `FROM php:8.3-fpm-alpine`
    * **Alpine Linux** es una distribución de Linux muy ligera.
    * Usarla como imagen base en Docker reduce el tamaño de la imagen y acelera el arranque del contenedor.

2. `apk add --no-cache`
    * **`apk`** es el gestor de paquetes de Alpine Linux.
    * **`add`** instala paquetes.
    * **`--no-cache`** evita guardar caché durante la instalación, reduciendo el tamaño de la imagen.

3. `apk add --no-cache --virtual .build-deps $PHPIZE_DEPS`
    * **`--virtual .build-deps`** crea un “paquete virtual” que agrupa varios paquetes. Esto permite eliminar todos esos paquetes juntos más adelante.
    * **`$PHPIZE_DEPS`** contiene herramientas necesarias para compilar extensiones PHP (gcc, make, autoconf, etc.).
    * En otras palabras, se instalan temporalmente herramientas de desarrollo necesarias únicamente para compilar extensiones PHP.

4. `docker-php-ext-install`
    * Comando proporcionado por las imágenes oficiales de PHP para instalar extensiones PHP.

5. `docker-php-source delete`
    * Elimina el código fuente de PHP para reducir el tamaño de la imagen.

6. `apk del .build-deps`
    * Elimina todas las herramientas de compilación instaladas como parte del paquete virtual `.build-deps`.
    * Esto mantiene la imagen ligera tras compilar las extensiones PHP.
    
7. `--from=composer:latest`
    * Utiliza una construcción multi-stage para copiar Composer`/usr/bin/composer` desde otra imagen (la oficial de Composer).

## Crear una imagen
```
docker image build -t laravel-app:v1.0 .
```

## Ejectar un contenedor localmente con Docker Compose
**Los valores marcados con un símbolo ★ deben ser configurados por usted mismo.**
```bash
docker container run --rm \
  --name laravel-app \
  -p 8000:8000 \
  -e APP_NAME=Laravel \
  -e APP_ENV=local \
  -e APP_KEY=★sobrescríbase usted mismo★ \
  -e APP_DEBUG=true \
  -e APP_TIMEZONE=UTC \
  -e APP_URL=http://localhost \
  -e APP_LOCALE=en \
  -e APP_FALLBACK_LOCALE=en \
  -e APP_FAKER_LOCALE=en_US \
  -e APP_MAINTENANCE_DRIVER=file \
  -e PHP_CLI_SERVER_WORKERS=4 \
  -e BCRYPT_ROUNDS=12 \
  -e LOG_CHANNEL=stack \
  -e LOG_STACK=single \
  -e LOG_DEPRECATIONS_CHANNEL=null \
  -e LOG_LEVEL=debug \
  -e DB_CONNECTION=mysql \
  -e DB_HOST=★sobrescríbase usted mismo★ \
  -e DB_PORT=4000 \
  -e DB_DATABASE=test \
  -e DB_USERNAME=★sobrescríbase usted mismo★ \
  -e DB_PASSWORD=★sobrescríbase usted mismo★ \
  -e MYSQL_ATTR_SSL_CA_B64=★sobrescríbase usted mismo★ \
  -e SESSION_DRIVER=database \
  -e SESSION_LIFETIME=120 \
  -e SESSION_ENCRYPT=false \
  -e SESSION_PATH=/ \
  -e SESSION_DOMAIN=null \
  -e BROADCAST_CONNECTION=log \
  -e FILESYSTEM_DISK=local \
  -e QUEUE_CONNECTION=database \
  -e CACHE_STORE=database \
  -e CACHE_PREFIX= \
  -e MEMCACHED_HOST=127.0.0.1 \
  -e REDIS_CLIENT=phpredis \
  -e REDIS_HOST=127.0.0.1 \
  -e REDIS_PASSWORD=null \
  -e REDIS_PORT=6379 \
  -e MAIL_MAILER=log \
  -e MAIL_SCHEME=null \
  -e MAIL_HOST=127.0.0.1 \
  -e MAIL_PORT=2525 \
  -e MAIL_USERNAME=null \
  -e MAIL_PASSWORD=null \
  -e MAIL_FROM_ADDRESS="hello@example.com" \
  -e MAIL_FROM_NAME="${APP_NAME}" \
  -e AWS_ACCESS_KEY_ID= \
  -e AWS_SECRET_ACCESS_KEY= \
  -e AWS_DEFAULT_REGION=us-east-1 \
  -e AWS_BUCKET= \
  -e AWS_USE_PATH_STYLE_ENDPOINT=false \
  -e VITE_APP_NAME="${APP_NAME}" \
laravel-app:v1.0
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
> <img width="991" height="464" alt="image" src="https://github.com/user-attachments/assets/871f5404-03cd-419a-8401-0304d87edeb6" />

Puede ver que la imagen se ha añadido en Docker Hub.
> <img width="1904" height="745" alt="image" src="https://github.com/user-attachments/assets/86bc07ed-35f1-4577-b3e1-77384fb781de" />
