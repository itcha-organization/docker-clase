# Reducción del tamaño de la imagen mediante la optimización del Dockerfile

## 1. ¿Por qué es importante reducir el tamaño de las imágenes?

Al crear una imagen de Docker, si dejamos archivos innecesarios o cachés sin limpiar, la imagen se vuelve más pesada y se generan problemas como:

* Mayor tiempo en la **construcción y despliegue**
* Consumo innecesario de **ancho de banda de red**
* Ocupación excesiva de **almacenamiento**

Por ello, es fundamental realizar una **limpieza de archivos y cachés** para reducir el tamaño de la imagen.

## 2. Problemas del Dockerfile original

En el Dockerfile anterior quedaban los siguientes elementos innecesarios:

* **Caché de Composer** (`/root/.composer/cache`)
* **Código de pruebas y caché** (`/var/www/tests`, `/var/www/storage/framework/cache/*`)
* **Dependencias de Node.js** (`/var/www/node_modules`) y **caché de npm** (`/root/.npm`)
* **Caché de Alpine apk** (`/var/cache/apk/*`)

## 3. Dockerfile optimizado
Modifique el `Dockerfile` como sigue.
```diff
# 1. Imagen de base
FROM php:8.3-fpm-alpine

...Omitido...

# 6. Instalación de Composer (producción) y eliminación de archivos innecesarios
-RUN composer install --optimize-autoloader --no-dev --prefer-source
# ★Añade lo siguiente.
RUN composer install --optimize-autoloader --no-dev --prefer-source \
    && rm -rf /var/www/tests \
    && rm -rf /var/www/storage/framework/cache/* \
    && rm -rf /root/.composer/cache
# ★Añade hasta aquí

# 7. Compilación de Node.js y limpieza
-RUN npm install && npm run build
# ★Añade lo siguiente.
RUN npm install && npm run build \
    && rm -rf /var/www/node_modules \
    && rm -rf /root/.npm \
    && rm -rf /var/www/package-lock.json

# 8. Limpiar apt cache al final
RUN rm -rf /var/cache/apk/*
# ★Añade hasta aquí

# 9. Establecer permisos para Laravel
RUN chown -R www-data:www-data /var/www/storage /var/www/bootstrap/cache

# 10. Comandos de inicio de la aplicación Laravel
CMD ["php", "artisan", "serve", "--host=0.0.0.0", "--port=8000"]
```
Los puntos clave de la modificación son los siguientes.

### (1) Limpieza relacionada con Composer
```dockerfile
rm -rf /var/www/tests
rm -rf /var/www/storage/framework/cache/*
rm -rf /root/.composer/cache
```
* **Código de pruebas (`tests/`)**: innecesario en producción.
* **Caché de Laravel**: se eliminan archivos temporales de construcción.
* **Caché de Composer**: archivos temporales usados para descargar paquetes.
👉 Solo queda el código realmente necesario para ejecutar la aplicación.

### (2) Limpieza relacionada con Node.js
```dockerfile
rm -rf /var/www/node_modules
rm -rf /root/.npm
rm -rf /var/www/package-lock.json
```
* **`node_modules/`**: innecesario tras la compilación del frontend.
* **Caché de npm**: archivos temporales de instalación.
* **`package-lock.json`**: no es necesario dentro de la imagen de producción (debe mantenerse en el repositorio).
👉 Se conserva únicamente el resultado de la compilación (`public/build` u otros).

### (3) Limpieza de apk
```dockerfile
rm -rf /var/cache/apk/*
```
* Se eliminan los archivos de caché de Alpine Linux.
👉 Importante realizar la limpieza al final de la construcción.

## 4. Verificación del efecto
Después de optimizar `Dockerfile`, construye la imagen con la etiqueta `v1.2`.
```
docker image build -t laravel-app:v1.2 .
```
Lista las imágenes de `laravel-app` y comprueba los cambios de tamaño.
```
docker image ls laravel-app
```
