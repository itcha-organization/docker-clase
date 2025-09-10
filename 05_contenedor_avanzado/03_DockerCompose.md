# Administrar contenedores de forma eficaz con `Docker Compose`

## ¿Qué es Docker Compose?

Docker Compose es una **herramienta para administrar varios contenedores al mismo tiempo**.

Normalmente, al crear una aplicación web con Docker, los pasos serían:

* Ejecutar `docker run` para iniciar el servidor web
* Ejecutar `docker run` para iniciar la base de datos
* Configurar la red y las variables de entorno para conectarlos

Escribir todos estos comandos cada vez es tedioso.
→ Con **docker-compose.yml**, se puede escribir toda la configuración en un solo archivo y gestionarla con un solo comando.

---

## Características

1. **Inicio y detención de múltiples contenedores a la vez**

   * `docker compose up` → inicia varios servicios juntos
   * `docker compose down` → los detiene y elimina en conjunto

2. **Configuración como código**

   * La imagen del contenedor, los puertos, las variables de entorno, las redes y los volúmenes se definen en un archivo YAML.

3. **Reproducibilidad del entorno de desarrollo**

   * Si todos los miembros del equipo usan el mismo `docker-compose.yml`, cualquiera puede construir el mismo entorno fácilmente.

---

## Ejemplo sencillo

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

Comandos:

```bash
docker compose up -d   # Iniciar
docker compose ps      # Verificar
docker compose down    # Detener
```


## Resumen

* Docker Compose = herramienta para administrar varios contenedores fácilmente
* **Escribes la configuración en `docker-compose.yml` → ejecutas `docker compose up` para levantar todo**
* Es muy útil para crear entornos de desarrollo y en proyectos de equipo

---
