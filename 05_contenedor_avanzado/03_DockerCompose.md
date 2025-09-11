# Administrar contenedores de forma eficaz con `Docker Compose`

## Problemas de Docker
1. **Difícil de gestionar varios contenedores:** Iniciar y detener juntos el servidor web, la aplicación y la base de datos es complicado.
2. **Necesidad de ajustar el orden de inicio:** Ejemplo: primero la base de datos → luego la aplicación → después el servidor web.
3. **Baja reproducibilidad del entorno:** Las opciones de `docker run` varían según la persona, lo que genera diferencias en el funcionamiento.
4. **Difícil compartir la configuración:** No basta con describir puertos o volúmenes en un README.

👉 **Docker Compose** resuelve estos problemas. Permite gestionar la configuración en un **archivo YAML** y crear fácilmente un entorno reproducible.
  > <img width="273" height="212" alt="image" src="https://github.com/user-attachments/assets/d56520f8-62f1-40e8-a3f2-47b6c45e508d" />

## ¿Qué es Docker Compose?
> <img width="378" height="168" alt="image" src="https://github.com/user-attachments/assets/80c3bdc7-d093-4b1e-9f09-3047cb89e5b6" />
Docker Compose es una **herramienta para administrar varios contenedores al mismo tiempo**.

Normalmente, al crear una aplicación web con Docker, los pasos serían:
* Crear la red para permitir la comunicación entre contenedores
* Crear el volúmen para mantener los datos de la base de datos
* Ejecutar `docker run` para iniciar el servidor web
* Ejecutar `docker run` para iniciar la base de datos

Escribir todos estos comandos cada vez es tedioso.<br>
→ Con **docker-compose.yml**, se puede escribir toda la configuración en un solo archivo y gestionarla con un solo comando.

### Elementos básicos de Docker Compose

* **`docker-compose.yml`**: archivo donde se describe la configuración de múltiples contenedores
* **Servicios (services)**: unidades que conforman la aplicación (ej.: servidor web, servidor de base de datos)
* **Redes (networks)**: redes virtuales que conectan los diferentes servicios
* **Volúmenes (volumes)**: mecanismo para la persistencia de los datos

## Características
1. **Inicio y detención de múltiples contenedores a la vez**
   * `docker compose up` → inicia varios servicios juntos
   * `docker compose down` → los detiene y elimina en conjunto

2. **Configuración como código**
   * La imagen del contenedor, los puertos, las variables de entorno, las redes y los volúmenes se definen en un archivo YAML. Los principales componentes son los siguientes:
      * **`docker-compose.yml`**: archivo donde se describe la configuración de múltiples contenedores
      * **Servicios (services)**: unidades que conforman la aplicación (ej.: servidor web, servidor de base de datos)
      * **Redes (networks)**: redes virtuales que conectan los diferentes servicios
      * **Volúmenes (volumes)**: mecanismo para la persistencia de los datos

3. **Reproducibilidad del entorno de desarrollo**
   * Si todos los miembros del equipo usan el mismo `docker-compose.yml`, cualquiera puede construir el mismo entorno fácilmente.

## Ejemplo sencillo
Utilice compose para lanzar un contenedor de base de datos y un contenedor de servidor web en la misma red.
### 1. Crear directorio de trabajo
```
mkdir compose_ejemplo
```
```
cd compose_ejemplo/
```

### 2. Crear un archivo de configuración `docker-compose.yml`
`docker-compose.yml`
```yaml
services:
  db:
    image: postgres:15
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: sampledb
    volumes:
      - db_data:/var/lib/postgresql/data

  web:
    image: nginx:latest
    ports:
      - "8080:80"
    depends_on:
      - db

volumes:
  db_data:
```
**Puntos clave:**
* Se definen el servicio `db` (Postgres) y el servicio `web` (Nginx)
* Con `depends_on`, el servidor web se inicia después de que la base de datos esté en marcha
* Con `volumes`, los datos de la base de datos se mantienen de forma persistente
* No se ha definido ninguna red; se crea una automáticamente al iniciar.
Los contenedores dentro del mismo archivo yaml se conectan automáticamente a la red predeterminada.

### 3. El siguiente comando crea contenedores, volúmenes y redes según el archivo de configuración, por lotes.
```bash
docker compose up  # Iniciar
```
### 4. El siguiente comando elimina contenedores, volúmenes y redes.
```bash
docker compose down --volume  # Detener
```

<details>
<summary>↓Pasos sin Docker Compose</summary>

## Pasos (sin Docker Compose)

### 1. Crear una red

Para que los contenedores se comuniquen entre sí:

```bash
docker network create mynetwork
```

---

### 2. Crear un volumen

Para la persistencia de datos de PostgreSQL:

```bash
docker volume create db_data
```

---

### 3. Iniciar el contenedor de base de datos

```bash
docker run -d \
  --name db \
  --network mynetwork \
  -e POSTGRES_USER=user \
  -e POSTGRES_PASSWORD=password \
  -e POSTGRES_DB=sampledb \
  -v db_data:/var/lib/postgresql/data \
  postgres:15
```

---

### 4. Iniciar el contenedor web (Nginx)

```bash
docker run -d \
  --name web \
  --network mynetwork \
  -p 8080:80 \
  nginx:latest
```

---

## Puntos clave

* **Red**: con Compose se crea y conecta automáticamente; sin Compose, hay que usar `docker network create` y `--network`.
* **Volumen**: con Compose se define en `volumes:`, sin Compose hay que crearlo manualmente con `docker volume create`.
* **Dependencias**: con `depends_on` en Compose se controla el orden; sin Compose, hay que **iniciar manualmente primero la DB y luego el web**.

---

👉 Sin Docker Compose, se deben gestionar **redes, volúmenes y orden de inicio manualmente**, lo que resulta más laborioso.

</details>

## Resumen
* Docker Compose = herramienta para administrar varios contenedores fácilmente
* **Escribes la configuración en `docker-compose.yml` → ejecutas `docker compose up` para levantar todo**
* Es muy útil para crear entornos de desarrollo y en proyectos de equipo



---
# Tutorial básico de Docker Compose

## Objetivo
* Aprender cómo gestionar contenedores con Docker Compose
* Definir de forma sencilla la interacción entre múltiples contenedores (PHP y MySQL)
* Conectar un contenedor de PHP a un contenedor de MySQL y verificar su funcionamiento

## 1. Crear directorio de trabajo
```bash
mkdir compose_clase
cd compose_clase/
```

## 2. Estructura del proyecto

La estructura será la siguiente:

```
compose_clase/
├─ docker-compose.yml
├─ php/
│   ├─ Dockerfile
│   └─ main.php
```

## 3. Crear docker-compose.yml
Guarde lo siguiente como `docker-compose.yml`:
```yaml
services:
  db:
    image: mysql:8.2.0
    container_name: db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: sample
    ports:
      - "3306:3306"
    volumes:
      - db-data:/var/lib/mysql   # ★ persistencia de datos

  php:
    build: ./php
    container_name: php-app
    depends_on:
      - db
    command: php /main.php

volumes:
  db-data:   # ★ definición de volumen nombrado
```
Puntos clave:
* **services:** define múltiples contenedores
* **db** → Contenedor MySQL (creación de contraseña y base de datos con variables de entorno)
* **php** → Imagen construida a partir de un Dockerfile
* **`db-data:/var/lib/mysql`**
  → Persistencia de los datos de MySQL en un **volumen con nombre**
* En la sección `volumes:` se define `db-data`, por lo que Compose lo crea y lo gestiona automáticamente

## 4. Crear Dockerfile para PHP
`php/Dockerfile`
```dockerfile
FROM php:8.2.12

RUN apt-get update && apt-get install -y iputils-ping \
    && docker-php-ext-install pdo_mysql

COPY ./main.php .
```

## 5. Crear script PHP

`php/main.php`
```php
<?php
$dsn = 'mysql:host=db;port=3306;dbname=sample';
$username = 'root';
$password = 'secret';

try {
    $pdo = new PDO($dsn, $username, $password);

    $stmt = $pdo->query('SELECT * FROM user');
    while ($row = $stmt->fetch(PDO::FETCH_ASSOC)) {
        echo '- ID: ' . $row['id'] . ', Name: ' . $row['name'] . PHP_EOL;
    }

    $pdo = null;
} catch (PDOException $e) {
    echo 'Connection failed: ' . $e->getMessage();
}
```

## 6. Iniciar los contenedores
Ejecutar el siguiente comando para levantar todo de una vez:
```bash
docker compose up --build
```

## 7. Limpieza
Para detener y eliminar los contenedores y la red creada:
```bash
docker compose down
```

## Resumen
* Con Docker Compose es posible gestionar redes y dependencias de forma automática
* Lo que antes requería ejecutar `docker run` por separado, ahora se puede definir y ejecutar de manera sencilla en un `docker-compose.yml`
* En este ejemplo hemos aprendido lo básico de Compose con un caso **PHP → MySQL**
