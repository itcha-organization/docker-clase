# Volume para almacenar datos


# Tutorial 1: introducción a Docker Volume

En este tutorial aprenderás los conceptos básicos sobre **Docker Volume**.
Los volúmenes permiten que los datos persistan incluso después de eliminar un contenedor, y además se pueden compartir entre varios contenedores.

## 1. Crear un volumen

Primero, crea un nuevo volumen con `docker volume create`:
```bash
docker volume create --name my-volume
```
Verifica que el volumen haya sido creado:
```bash
docker volume ls
```
👉 El volumen **`my-volume`** es el espacio de almacenamiento que usaremos.
> <img width="739" height="182" alt="image" src="https://github.com/user-attachments/assets/85218d5c-7cab-4c7b-95c1-7cf91294423a" />

## 2. Montar el volumen en un contenedor
Ahora inicia un contenedor de Ubuntu y monta el volumen creado:
```bash
docker container run --name ubuntu1 --rm -it \
  --mount type=volume,src=my-volume,dst=/my-work \
  ubuntu:22.04
```
* `--mount type=volume` : monta un volumen
* `src=my-volume` : nombre del volumen a usar
* `dst=/my-work` : directorio dentro del contenedor donde se montará el volumen

Dentro del contenedor, revisa el punto de montaje `/my-work`:
```bash
ls /
```
👉 El directorio `/my-work` corresponde al volumen montado.
> <img width="718" height="139" alt="image" src="https://github.com/user-attachments/assets/5ec16289-8817-49b6-8fd0-b1c2e8f731ac" />

## 3. Escribir datos en el volumen
Guarda un archivo dentro del volumen:
```bash
echo 'hello from container.' > /my-work/hello.txt
```
```bash
cat /my-work/hello.txt
```
👉 El archivo `hello.txt` se guardó en el volumen.

Luego sal del contenedor:
```bash
exit
```
> <img width="698" height="241" alt="image" src="https://github.com/user-attachments/assets/d7f98cfe-bf0a-4d0c-a451-3b58854ce16d" />

## 4. Acceder al volumen desde otro contenedor
Si montas el mismo volumen en otro contenedor, los datos seguirán estando disponibles:
```bash
docker container run --name ubuntu2 --rm -it \
  --mount type=volume,src=my-volume,dst=/my-work \
  ubuntu:22.04
```
Dentro del contenedor:
```bash
cat /my-work/hello.txt
```
👉 Aunque sea otro contenedor, los datos persisten en el volumen.
```bash
exit
```
> <img width="692" height="191" alt="image" src="https://github.com/user-attachments/assets/95215a6b-b2c4-46ad-97a7-f638cedce25e" />

## 5. Eliminar un volumen
Cuando ya no necesites el volumen, elimínalo con:
```bash
docker volume rm my-volume
```
Verifica:
```bash
docker volume ls
```
👉 El volumen `my-volume` fue eliminado.
> <img width="556" height="155" alt="image" src="https://github.com/user-attachments/assets/e2c875d0-be13-4e9c-8597-3416c36f9f15" />

# Tutorial 2: Persistencia de datos con MySQL y Docker Volume

En este tutorial aprenderás cómo usar **Docker Volume** para que una base de datos MySQL mantenga sus datos incluso después de detener o eliminar un contenedor.


## 1. Crear un volumen

Primero creamos un volumen llamado `db-volume`:

```bash
docker volume create --name db-volume
```

Verifica que el volumen existe:

```bash
docker volume ls
```

---

## 2. Revisar la configuración de MySQL

Antes de iniciar MySQL con volumen, podemos revisar dónde guarda sus datos.

```bash
docker container run --rm mysql:8.2.0 cat /etc/my.cnf
```

Dentro del archivo aparece:

```
datadir=/var/lib/mysql
```

👉 Esto significa que los datos de MySQL se almacenan en `/var/lib/mysql`.
Ese será el directorio que montaremos al volumen.

---

## 3. Iniciar un contenedor MySQL con volumen

Ahora ejecutamos un contenedor MySQL con el volumen montado:

```bash
docker container run --name db1 --rm --detach \
  --env MYSQL_ROOT_PASSWORD=secret \
  --env MYSQL_DATABASE=sample \
  --publish 3306:3306 \
  --mount type=volume,src=db-volume,dst=/var/lib/mysql \
  mysql:8.2.0
```

* `--env MYSQL_ROOT_PASSWORD=secret` → establece la contraseña del usuario root.
* `--env MYSQL_DATABASE=sample` → crea una base de datos inicial llamada `sample`.
* `--mount ...` → monta el volumen `db-volume` en `/var/lib/mysql`.

---

## 4. Conectarse a la base de datos

Con el contenedor en ejecución, nos conectamos desde el cliente MySQL instalado en la máquina host:

```bash
mysql --host=127.0.0.1 --port=3306 --user=root --password=secret sample
```

Salida esperada:

```
Welcome to the MySQL monitor...
```

---

## 5. Crear una tabla y agregar datos

Dentro de MySQL, creamos una tabla y agregamos registros:

```sql
create table user (id int, name varchar(32));
insert into user (id, name) values (1, 'John Doe');
insert into user (id, name) values (2, 'Jane Doe');
select * from user;
```

Resultado:

```
+------+----------+
| id   | name     |
+------+----------+
|    1 | John Doe |
|    2 | Jane Doe |
+------+----------+
```

👉 Los datos fueron guardados en el volumen.

Salir de MySQL:

```sql
exit
```

---

## 6. Detener el contenedor

Ahora detenemos el contenedor MySQL:

```bash
docker container stop db1
```

Verificamos que ya no está en ejecución:

```bash
docker container ls -a
```

---

## 7. Iniciar un nuevo contenedor con el mismo volumen

Ejecutamos otro contenedor (`db2`) usando el mismo volumen:

```bash
docker container run --name db2 --rm --detach \
  --publish 3306:3306 \
  --mount type=volume,src=db-volume,dst=/var/lib/mysql \
  mysql:8.2.0
```

Nos conectamos de nuevo:

```bash
mysql --host=127.0.0.1 --port=3306 --user=root --password=secret sample
```

Consultamos la tabla:

```sql
select * from user;
```

Resultado:

```
+------+----------+
| id   | name     |
+------+----------+
|    1 | John Doe |
|    2 | Jane Doe |
+------+----------+
```

👉 Los datos persisten aunque el contenedor original (`db1`) haya sido eliminado.

---

## 8. Detener el contenedor y eliminar el volumen

Cuando ya no necesites la base de datos, puedes detener el contenedor y borrar el volumen:

```bash
docker container stop db2
docker volume rm db-volume
```

---

## Resumen

En este tutorial aprendiste:

1. Crear un volumen para MySQL
2. Montarlo en `/var/lib/mysql` para persistir datos
3. Crear tablas y registros en MySQL
4. Acceder a los mismos datos desde otro contenedor
5. Eliminar el volumen cuando ya no se necesita

👉 Gracias a los volúmenes, **los datos sobreviven aunque los contenedores se eliminen**. Esto es fundamental para aplicaciones en producción.
