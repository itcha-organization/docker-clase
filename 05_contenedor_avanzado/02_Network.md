# Tutorial Básico de Redes en Docker

## Objetivo

* Aprender a crear y administrar redes en Docker
* Verificar la comunicación entre contenedores
* Conectar un contenedor PHP a un contenedor MySQL

## 1. Crear el directorio de trabajo
Primero, creamos un directorio para el tutorial.

```bash
mkdir network_clase
```
```bash
cd network_clase/
```
> <img width="537" height="96" alt="image" src="https://github.com/user-attachments/assets/f3002e51-6410-4733-b1bd-af2e4aec9c88" />

## 2. Crear una red en Docker
Creamos una red personalizada:
```bash
docker network create my-network
```
Verificar las redes existentes:
```bash
docker network ls
```
> <img width="838" height="245" alt="image" src="https://github.com/user-attachments/assets/6e35453b-20f3-4c1e-9db4-61581f12263a" />

## 3. Crear imagen de PHP con ping
En primer lugar, se utiliza ping para comprobar la comunicación entre contenedores.

Cree el siguiente Dockerfile.
**Dockerfile**
```dockerfile
FROM php:8.2.12

RUN apt-get update
RUN apt-get install -y iputils-ping
```
- Para que el contenedor PHP pueda hacer ping a otros contenedores, instalamos `iputils-ping`.

Construir la imagen:
```bash
docker image build --tag my-php:ping .
```

## 4. Iniciar contenedor MySQL
Conectamos el contenedor a la red creada:
```bash
docker container run --name db --rm --detach \
    --env MYSQL_ROOT_PASSWORD=secret \
    --env MYSQL_DATABASE=sample \
    --publish 3306:3306 \
    --network my-network \
    mysql:8.2.0
```
- **`--network`**: Opción para vincular el contenedor a la red.
> <img width="1942" height="126" alt="image" src="https://github.com/user-attachments/assets/f616e145-fb43-4107-934e-85e61c337e20" />

## 5. Verificar comunicación con ping desde PHP
```bash
docker container run --network my-network my-php:ping ping -c 3 -t 1 db
```
- **`--network`**: Opción para vincular el contenedor a la red.
👉 `db` es el nombre del contenedor y se puede resolver dentro de la red.
> <img width="1275" height="250" alt="image" src="https://github.com/user-attachments/assets/a6bfa3f9-7c96-46bd-afa3-130a7f4993ba" />

## 6. Conexión a MySQL desde el host
```bash
mysql --host=127.0.0.1 --port=3306 --user=root --password=secret sample
```
Dentro de MySQL, creamos una tabla y agregamos registros:
```sql
create table user (id int, name varchar(32));
```
```sql
insert into user (id, name) values (1, 'John Doe');
```
```sql
insert into user (id, name) values (2, 'Jane Doe');
```
```sql
select * from user;
```

Salir de MySQL:
```sql
exit
```
> <img width="1260" height="779" alt="image" src="https://github.com/user-attachments/assets/9e6cc0f3-eee2-44fb-8f0f-e057bd063dc8" />

## 7. Conectar PHP a MySQL
Habilitamos `pdo_mysql` y conectamos desde PHP:

Edite el Dockerfile de la siguiente manera.

**Dockerfile**
```dockerfile
FROM php:8.2.12

RUN apt-get update
RUN apt-get install -y iputils-ping
RUN docker-php-ext-install pdo_mysql

COPY ./main.php .
```

Crear `main.php`.

**main.php**
```php
<?php
$dsn = 'mysql:host=db;port=3306;dbname=sample';
$username = 'root';
$password = 'secret';
$pdo = new PDO($dsn, $username, $password);

$stmt = $pdo->query('SELECT * FROM user');
$stmt->execute();
while ($row = $stmt->fetch(PDO::FETCH_ASSOC)) {
    echo '- ID: ' . $row['id'] . ', Name: ' . $row['name'] . PHP_EOL;
}

$pdo = null;
```

Construir la imagen:
```bash
docker image build --tag my-php:pdo_mysql .
```

Ejecutar el contenedor:
```bash
docker container run --rm --network my-network my-php:pdo_mysql php /main.php
```
> <img width="1338" height="77" alt="image" src="https://github.com/user-attachments/assets/f7a9b6a5-6a1c-4b7f-a095-a010722e4041" />

## 8. Limpieza

```bash
docker container stop db
```
```bash
docker network rm my-network
```
> <img width="800" height="115" alt="image" src="https://github.com/user-attachments/assets/40222502-3c4d-4248-a665-350e4b36acda" />
