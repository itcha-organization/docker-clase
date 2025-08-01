# Ejecutar comandos dentro de un contenedor en funcionamiento

<img width="557" height="264" alt="image" src="https://github.com/user-attachments/assets/8a4af561-6353-4ad4-b287-c9cbf0cec7ee" />

## Objetivo

* Comprender y practicar el comando `docker container exec` para ejecutar comandos dentro de un contenedor en funcionamiento.
* Aplicar el comando para interactuar con contenedores de manera práctica.

## ¿Qué hace `docker container exec`?
Se pueden ejecutar nuevos comandos en el contenedor en ejecución.
Es útil para investigar los contenedores en ejecución. Por ejemplo, comprobar los archivos de configuración, la estructura de directorios, etc.

### 📌 Sintaxis
```bash
docker container exec [opciones] <nombre_contenedor> <comando>
```
* `<nombre_contenedor>`: nombre o ID del contenedor
* `<comando>`: el comando que quieres ejecutar dentro del contenedor

## Preparación: iniciar un contenedor de PostgreSQL
Usaremos la imagen oficial de PostgreSQL para probar los ejemplos.
```bash
docker container run -d --name my-postgres -e POSTGRES_PASSWORD=secret postgres
```

## Ejemplo 1: Ejecutar comandos simples dentro del contenedor
### Ver el contenido del directorio raíz del contenedor:
```bash
docker container exec my-postgres ls /
```

### Ver el contenido del directorio `/var/lib/postgresql`:
```bash
docker container exec my-postgres ls /var/lib/postgresql
```

## Ejemplo 2: Ejecutar en modo interactivo (`-it`)
Si deseas interactuar con el contenedor (por ejemplo, iniciar una shell o consola), usa `-it`:
### Acceder al shell bash (si está disponible):
```bash
docker container exec -it my-postgres bash
```
> ❗ Algunos contenedores no tienen `bash` por defecto. Usa `sh` si `bash` no está disponible:

## Ejemplo 3: Ejecutar psql dentro del contenedor
### Iniciar la consola de PostgreSQL:
```bash
docker container exec -it my-postgres psql -U postgres
```
### Ejecutar una consulta desde la consola:
```sql
SELECT current_database();
```
> Para salir de `psql`, usa:
> ```sql
> \q
> ```

## Limpieza

Cuando termines, puedes detener y eliminar el contenedor:
```bash
docker stop my-postgres
docker rm my-postgres
```

## Ejercicios 
Preparación:
```bash
docker container run -d --name my-postgres -e POSTGRES_PASSWORD=secret postgres
```

1. Ejecuta el comando `env` dentro del contenedor para ver sus variables de entorno.
2. Ejecute `bash` en modo interactivo y navegue hasta el directorio `/etc` en el contenedor.

## Respuesta

1. 
```
docker container exec my-postgres env
```
> <img width="895" height="259" alt="image" src="https://github.com/user-attachments/assets/b36b3c35-fe23-47e6-a152-735001cea506" />

2.
```
docker container exec -it my-postgres bash
```
```bash
cd /etc
```
> <img width="559" height="78" alt="image" src="https://github.com/user-attachments/assets/0de3bd11-fa17-42ee-88fe-730f8fdac67b" />
