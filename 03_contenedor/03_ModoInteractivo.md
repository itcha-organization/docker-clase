# Modo interactivo en contenedor Python

## Usar Python en modo interactivo

Puedes lanzar un contenedor basado en la imagen oficial de Python e iniciar el intérprete en modo interactivo con el siguiente comando:

```bash
docker container run --rm --interactive --tty python
```
Este comando inicia un contenedor y abre el prompt interactivo de Python (`>>>`), permitiéndote ejecutar comandos de Python directamente dentro del contenedor.

### Desglose del comando

| Opción                 | Descripción                                                            |
| ---------------------- | ---------------------------------------------------------------------- |
| `--interactive` o `-i` | Mantiene la entrada estándar (stdin) abierta, permitiendo interacción. |
| `--tty` o `-t`         | Asigna una terminal virtual para ver el resultado de forma amigable.   |
| `python`               | Nombre de la imagen que se usará (última versión oficial de Python).   |
> 💡 `--interactive --tty` también se puede abreviar como `-it`, que es lo más común.

---

### 🧪 Ejemplo práctico

```bash
docker container run -it --rm python
```

Una vez que veas el prompt `>>>`, puedes escribir comandos como:

```python
>>> print("Hola desde Docker")
Hola desde Docker
>>> print(5 * 6)
30
```

Para salir del modo interactivo:

```python
>>> exit()
```

O presiona `Ctrl + D`.


## ✅ Preguntas prácticas

1. Inicia un contenedor Ubuntu en modo interactivo y ejecuta el comando `ls`.
2. ¿Cómo puedes verificar el directorio de trabajo actual dentro del contenedor?
3. ¿Cuál es la ventaja de usar la opción `--rm` al ejecutar el contenedor?
4. ¿Qué sucede si omites la opción `-it`?
5. ¿Qué muestra el comando `whoami` dentro del contenedor?

---

## ✅ Respuestas

1.

```bash
docker container run -it --rm ubuntu bash
# Dentro del contenedor:
ls
```

2.

```bash
pwd
```

Salida esperada:

```
/
```

3. La opción `--rm` elimina automáticamente el contenedor cuando termina su ejecución. Esto evita que se acumulen contenedores innecesarios en el sistema.

4. Si omites `-it`, el contenedor no abrirá una terminal interactiva. No podrás escribir comandos manualmente y el contenedor puede cerrarse inmediatamente.

5. El comando `whoami` muestra el usuario actual. En la mayoría de los casos dentro de un contenedor Ubuntu, serás el usuario:

```
root
```

---

必要であれば、図や追加の練習問題もご提供できます。お気軽にご依頼ください。


# Capítulo 5: Ejecutar servidor Nginx

## 5.1 Publicar puerto del contenedor

```bash
docker container run --publish 8080:80 nginx
```

### ✅ Preguntas prácticas (Capítulo 5)

1. Ejecuta Nginx en el puerto `8081`.
2. Accede desde el navegador a `http://localhost:8081`.
3. Detén el contenedor y verifica el resultado al recargar la página.

### ✅ Respuestas

1.

```bash
docker container run -d --name servidorweb -p 8081:80 nginx
```

2. Se debe ver la página de bienvenida de Nginx.

3.

```bash
docker container stop servidorweb
```

→ Aparece un error en el navegador ("No se puede acceder").

---

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
