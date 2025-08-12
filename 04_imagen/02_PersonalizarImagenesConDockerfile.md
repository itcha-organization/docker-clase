# Personalización de Imágenes mediante Dockerfile con `FROM` y `RUN` (Nivel Inicial)

## Objetivo

En este módulo, aprenderás a crear tu propia imagen personalizada basada en Ubuntu usando un Dockerfile.

Cubriremos los siguientes conceptos clave:
* Nombre y etiqueta de la imagen
* Uso del comando `FROM`
* Uso del comando `RUN`
* Cómo construir la imagen con `docker image build`

## Estructura completa del nombre y la etiqueta de una imagen Docker
Una imagen Docker se nombra con la siguiente estructura:
```
[host del registro]/[namespace]/[repositorio]:[etiqueta]
```
Ejemplo:
```
docker.io/library/nginx:1.25
```
### Explicación de cada parte:
| Elemento          | Ejemplo     | Descripción                                          |
| ----------------- | ----------- | ---------------------------------------------------- |
| Host del registro | `docker.io` | El servidor donde se aloja la imagen                 |
| Namespace         | `library`   | Suele ser el nombre del usuario o de la organización |
| Repositorio       | `nginx`     | Lugar donde se almacena la imagen como tal           |
| Etiqueta (tag)    | `1.25`      | Etiqueta que identifica versión o propósito          |

### Elementos que pueden omitirse
Docker permite omitir ciertos elementos, que se rellenan automáticamente con valores por defecto:
| Elemento omitido | Valor por defecto          | Ejemplo interpretado                |
| ---------------- | -------------------------- | ----------------------------------- |
| Registro         | `docker.io`                | `nginx` → `docker.io/library/nginx` |
| Namespace        | `library` (para oficiales) | `nginx` → `docker.io/library/nginx` |
| Etiqueta         | `latest`                   | `nginx` → `nginx:latest`            |

Por ejemplo:
```bash
docker pull nginx
```
En realidad significa:
```bash
docker pull docker.io/library/nginx:latest
```

### ❌ NO se recomienda usar la etiqueta `latest`
* La etiqueta `latest` simplemente apunta a la versión predeterminada en ese momento.
	* Por ejemplo, hoy `ubuntu:latest` podría ser `ubuntu:22.04`, pero mañana podría cambiar a `ubuntu:24.04`.
* Si usas `latest`, cada nueva construcción podría tener diferentes dependencias.
  > #### Ejemplo:
  > ```dockerfile
  > FROM ubuntu:latest
  > ```
  > * En 2024 → se usará Ubuntu 22.04
  > * En 2025 → podría cambiarse a Ubuntu 24.04
  > → El mismo `Dockerfile` podría construirse sobre un sistema operativo **diferente**.

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
* Prueba escribir `vi` para verificar que esté instalado.

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
