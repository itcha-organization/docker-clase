# Personalización de Imágenes mediante Dockerfile con `FROM` y `RUN` (Nivel Inicial)

## Objetivo

En este módulo, aprenderás a crear tu propia imagen personalizada basada en Ubuntu usando un Dockerfile.

Cubriremos los siguientes conceptos clave:
* Nombre y etiqueta de la imagen
* Uso del comando `FROM`
* Uso del comando `RUN`
* Cómo construir la imagen con `docker image build`

## Explicación de cada comando

### 1️⃣ `FROM`

```dockerfile
FROM ubuntu:22.04
```
* Este comando especifica la **imagen base** sobre la cual se construirá la nueva imagen.
* Aquí estamos usando `ubuntu:22.04`.

🔎 Ejemplos comunes:
* `FROM node:20` → Imagen oficial de Node.js
* `FROM php:8.2` → Imagen oficial de PHP

---

### 2️⃣ `RUN`
```dockerfile
RUN apt-get update
RUN apt-get install -y vim
```
* `RUN` ejecuta comandos dentro de la imagen durante la construcción.
* En este caso, primero actualiza los paquetes y luego instala `vim`.

💡 Consejo: Puedes combinar múltiples comandos en una sola línea:
```dockerfile
RUN apt-get update && apt-get install -y vim
```

## Cómo construir la imagen

Con tu Dockerfile listo, usa este comando para construir la imagen:
```bash
docker image build -t ubuntu-vim:latest .
```
* `-t` define el nombre y la etiqueta de la imagen (aquí: `ubuntu-vim:latest`)
* `.` indica que el Dockerfile está en el directorio actual

🧠 ¿Qué es una **etiqueta (tag)**?
* Es la parte después de los dos puntos (`:`), como `latest`, `dev`, `v1.0`, etc.
* Útil para versionar tus imágenes

## ✅ Verificar imágenes creadas
```bash
docker image ls
```
Ejemplo de salida:
```
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
ubuntu-vim    latest    abcd12345678   1 minute ago    80MB
```

## 🚀 Ejecutar un contenedor desde tu imagen personalizada
```bash
docker container run -it ubuntu-vim:latest
```
* `-it` te permite interactuar con la terminal del contenedor.
* Prueba escribir `vim` para verificar que esté instalado.

## Ejercicios
1. Crea un Dockerfile basado en `ubuntu:20.04` que instale `curl`. Llámalo `ubuntu-curl:dev`.
2. ¿Qué comando usarías para eliminar una imagen de Docker?

## Soluciones
### Ejercicio 1

```dockerfile
# Dockerfile
FROM ubuntu:20.04
RUN apt-get update && apt-get install -y curl
```

```bash
docker image build -t ubuntu-curl:dev .
```

### Ejercicio 2
```bash
docker image rm <nombre o ID de la imagen>
```
