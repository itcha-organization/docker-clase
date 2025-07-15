# Ejecutar MySQL en segundo plano

En este capítulo aprenderás a ejecutar un servidor **MySQL** dentro de un contenedor Docker. MySQL es un sistema de gestión de bases de datos muy utilizado en aplicaciones web.

## ¿Por qué usar MySQL en un contenedor?

* No necesitas instalar MySQL directamente en tu sistema operativo.
* Puedes tener múltiples versiones de MySQL en diferentes contenedores.
* El entorno es fácilmente replicable en otros equipos o servidores.

## 1 Configurar variables de entorno

Cuando ejecutas el contenedor de la imagen oficial de MySQL, **es obligatorio definir la contraseña del usuario root** usando la variable de entorno `MYSQL_ROOT_PASSWORD`.

### 📌 Sintaxis

```bash
docker container run -env VARIABLE=valor imagen
```

### 📌 Ejemplo con MySQL

```bash
docker container run --name mysql8 --rm --env MYSQL_ROOT_PASSWORD=secreto --publish 3306:3306 mysql:8.0
```

* **`--env MYSQL_ROOT_PASSWORD=secreto`**: define la contraseña del usuario `root` (administrador de la base de datos).
* `mysql:8.0`: imagen oficial de MySQL, versión 8.0.
* Este comando **inicia el contenedor en primer plano**, por lo que ocupará la terminal hasta que lo detengas o falle.

👉 En la práctica, es recomendable usar `--detach` para ejecutar el contenedor en segundo plano.

## 2 Ejecutar en segundo plano

Para que el contenedor no bloquee tu terminal y siga ejecutándose en segundo plano, se utiliza la opción `-d` o `--detach`.

### 📌 Ejemplo detallado

```bash
docker container run -d --name mysql8 --rm --env MYSQL_ROOT_PASSWORD=secreto --publish 3306:3306 mysql:8.0
```
* `-d`: ejecuta el contenedor en segundo plano (modo “detached”).

### ✅ Verifica que el contenedor está corriendo:

```bash
docker container ls
```

## 3 Cómo conectarte al servidor MySQL dentro del contenedor

### Paso 1: Instalar mysql-client

```bash
apt update
```
```bash
apt install -y mysql-client
```
```bash
mysql --version
```

### Paso 2: Conectarte a MySQL como root

```bash
mysql --host=127.0.0.1 --port=3306 --user=root --password=secreto
```
> Resultado:
> ```bash
> mysql>
> ```
> Ahora puedes escribir:
> ```bash
> select version();
> ```
> Para salir del modo interactivo: Presiona Ctrl + D

## Opciones adicionales útiles para contenedor MySQL

| Opción Docker                     | Descripción                               |
| --------------------------------- | ----------------------------------------- |
| `--env MYSQL_DATABASE=nombre`     | Crea una base de datos inicial            |
| `--env MYSQL_USER=usuario`        | Crea un usuario adicional                 |
| `--env MYSQL_PASSWORD=contraseña` | Define la contraseña del usuario anterior |


## ✅ Preguntas prácticas

1. Ejecuta un contenedor de MySQL con contraseña `mysecret`.
2. Usa `--detach` para que se ejecute en segundo plano.
3. Verifica que el contenedor esté activo.
4. Conéctate al contenedor e ingresa al cliente MySQL como root.
5. ¿Qué pasa si no defines `MYSQL_ROOT_PASSWORD`?

---

## ✅ Respuestas

1.

```bash
docker container run --name mysqltest --env MYSQL_ROOT_PASSWORD=mysecret mysql:8.0
```

2.

```bash
docker container run -d --name mysqlbg --env MYSQL_ROOT_PASSWORD=mysecret mysql:8.0
```

3.

```bash
docker container ls
```

Deberías ver una fila con la imagen `mysql:8.0` y el estado `Up`.

4.

```bash
docker container exec -it mysqlbg bash
mysql -u root -p
```

Introduce la contraseña `mysecret`.

5.

Si no defines `MYSQL_ROOT_PASSWORD`, el contenedor **fallará al iniciarse** por razones de seguridad. Verás un mensaje de error en los logs:

```bash
docker container logs mysqlbg
```
