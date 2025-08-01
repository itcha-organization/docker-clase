# Comprobar registro de contenedor funcionando en segundo plano

<img width="574" height="255" alt="image" src="https://github.com/user-attachments/assets/20e9cac9-497f-4117-9f3e-840c76b93472" />

## Objetivo del capítulo
- Aprender cómo usar `docker container logs` para comprobar registros de contenedor funcionando en segundo plano.
- Aprender a usar la opción `--follow` para monitorear registros en tiempo real.

## ¿Qué hace `docker container logs`?

El nombre del contenedor se especifica en el argumento y se muestran los registros de ese contenedor.
Esto es útil, por ejemplo, para investigar errores en contenedores funcionandos en segundo plano.

### 📌 Sintaxis

```bash
docker container logs [opciones] CONTENEDOR
```
* `CONTENEDOR`: puede ser el ID o el nombre del contenedor.

## Ejemplo 1: Compruebe error del contenedor PostgreSQL
Inicie el contenedor en segundo plano desde la imagen `postgres`.
```
docker container run --name postgres --detach --publish 5432:5432 postgres
```
Compruebe el estado del contenedor.
```
docker container ls -a
```
> <img width="1039" height="95" alt="image" src="https://github.com/user-attachments/assets/e255010e-d496-4973-8167-ee8eee1135f9" />
> → El estado es `Exited` (finalizado)
Compruebe los registros de contenedores para investigar.
```
docker container logs postgres
```
> <img width="722" height="178" alt="image" src="https://github.com/user-attachments/assets/0402224d-6b7a-44ce-a318-50c6ba82889d" />
> <br>
> → Error: La base de datos no está inicializada y no se ha especificado la contraseña del superusuario.Debe especificar POSTGRES_PASSWORD a un valor no vacío para el superusuario.

## Ejemplo 2: Monitorear registros en tiempo real con la opción `--follow`
Borrar contenedores `postgres` terminados.
```
docker container logs postgres
```
Especifique la variable de entorno `POSTGRES_PASSWORD` e inicie el contenedor. Nómbralo `postgres2`.
```
docker container run --name postgres2 --detach --env POSTGRES_PASSWORD=secret --publish 5432:5432 postgres
```
Inicie la monitorización en tiempo real de los registros del contenedor `postgres2` con el siguiente comando.
```
docker container logs --follow postgres2
```
> Puedes salir pulsando `Ctrl`+`C`.
