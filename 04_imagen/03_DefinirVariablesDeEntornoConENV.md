# Definir variables de entorno con la instrucción Dockerfile `ENV`

## Objetivo

En este módulo, aprenderás a definir variables de entorno con la instrucción Dockerfile `ENV`

Cubriremos los siguientes conceptos clave:
* Uso de la instrucción `ENV`

##  ¿Qué es la instrucción `ENV`?

* Es una instrucción usada en Dockerfile para **establecer variables de entorno** dentro del contenedor.
* Permite modificar el comportamiento o configuración del contenedor mediante estas variables.

**Ejemplo: establecer una variable en Dockerfile**
```dockerfile
ENV MYSQL_ROOT_PASSWORD=mi_contraseña_secreta
```

Con esto, la variable `MYSQL_ROOT_PASSWORD` estará disponible dentro del contenedor.

## Tutorial: configurar la zona horaria en un contenedor MySQL

### Preparación previa: compruebe la zona horaria por defecto

Ejecute el contenedor `mysql:8.2.0`.
```
docker container run --name db --rm -d --env MYSQL_ROOT_PASSWORD=secret -p 3306:3306 mysql:8.2.0
```
Accede al contenedor mysql.
```
mysql --host=127.0.0.1 --port=3306 --user=root --password=secret
```
Si el acceso es correcto y se muestra el prompt `mysql>`, utilice el siguiente SQL para mostrar la hora actual y la zona horaria.
```sql
select now();
```
```sql
show variables like '%time_zone%';
```
⇒ La zona horaria por defecto es tiempo universal coordinado (UTC)
> <img width="1381" height="685" alt="image" src="https://github.com/user-attachments/assets/079a69e4-6596-420b-8b82-0e0a3feb4308" />

Detiene el contenedor.
Como se inició con la opción `--rm`, se borrará automáticamente después de que se detenga.
```
docker container stop db
```

### Práctica: configuración de variables de entorno con la instrucción `ENV` para cambiar zona horaria 
Crea un `Dockerfile` como sigue.
```dockerfile
FROM mysql:8.2.0

ENV TZ=America/El_Salvador
```
En el directorio donde guardaste el `Dockerfile`, ejecuta el comando `image build` para crear una imagen.
El nombre de la imagen es `my-mysql` y la etiqueta es `es`.
```
docker image build --tag my-mysql:es .
```
Crea un contenedor con la imagen `my-mysql:es`.
```
docker container run --name db --rm -d --env MYSQL_ROOT_PASSWORD=secret -p 3306:3306 my-mysql:es
```
Conéctate al contenedor como antes y comprueba la hora actual y la zona horaria.
```
mysql --host=127.0.0.1 --port=3306 --user=root --password=secret
```
Si el acceso es correcto y se muestra el prompt `mysql>`, utilice el siguiente SQL para mostrar la hora actual y la zona horaria.
```sql
select now();
```
```sql
show variables like '%time_zone%';
```
