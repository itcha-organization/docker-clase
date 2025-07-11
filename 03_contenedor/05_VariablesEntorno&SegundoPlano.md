# Capítulo 6: Ejecutar MySQL en segundo plano

## 6.1 Configurar variables de entorno

```bash
docker container run --env MYSQL_ROOT_PASSWORD=secreto mysql:8.0
```

## 6.2 Ejecutar en segundo plano

```bash
docker container run -d --name mysql8 --env MYSQL_ROOT_PASSWORD=secreto mysql:8.0
```

### ✅ Preguntas prácticas (Capítulo 6)

1. Ejecuta MySQL con contraseña `mysecret`.
2. Usa `--detach` para que se ejecute en segundo plano.
3. Verifica que el contenedor esté activo.

### ✅ Respuestas

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

---

# Capítulo 7: PostgreSQL en contenedor

## 7.1 Ver logs del contenedor

```bash
docker container logs mipg
```

## 7.2 Ejecutar comandos dentro del contenedor

```bash
docker container exec -it mipg bash
```

## 7.3 Conectarse a PostgreSQL

```bash
psql -U postgres
```

### ✅ Preguntas prácticas (Capítulo 7)

1. Ejecuta PostgreSQL (nombre: `mipg`) y muestra los logs.
2. Accede a bash dentro del contenedor y entra a `psql`.
3. Muestra las bases de datos existentes.

### ✅ Respuestas

1.

```bash
docker container run -d --name mipg -e POSTGRES_PASSWORD=secreto postgres
docker container logs mipg
```

2.

```bash
docker container exec -it mipg bash
psql -U postgres
```

3.

```sql
\l
```

---

¿Deseas que lo convierta en archivo PDF o Markdown listo para imprimir o compartir? También puedo adaptar esta guía como presentación de clase o incluir ejercicios con calificación automática.
