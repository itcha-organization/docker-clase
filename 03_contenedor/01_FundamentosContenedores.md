# Fundamentos de contenedores

## Un contenedor de Docker es un **entorno de ejecución aislado y ligero**
Un contenedor de Docker es un **entorno de ejecución aislado y ligero** que se crea en la la máquina host.
Al iniciarse, el contenedor **ejecuta un comando especificado como el proceso PID 1 (el primer proceso dentro del contenedor)**.
Este proceso es el principal dentro del contenedor y, **cuando termina, el contenedor también se detiene automáticamente**.
Además, cada contenedor funciona en un entorno separado, con su propio sistema de archivos, espacio de procesos y red, para evitar interferencias con la la máquina host u otros contenedores.
> El primer proceso (PID 1) que se ejecuta dentro del contenedor representa su "función principal".
> Por ejemplo, si se trata de un contenedor para un servidor web, el proceso PID 1 será el que arranque el servidor web (como `nginx`) y espere las solicitudes de los clientes.

> <img width="756" height="518" alt="image" src="https://github.com/user-attachments/assets/b5b4a12d-d595-4c72-bf7d-8981cce14f04" />

## El proceso PID 1 en contenedor
Un contenedor, al iniciarse, **ejecuta un solo comando como su primer proceso, el cual se convierte en el proceso PID 1 dentro del contenedor**.
Por ejemplo, es posible iniciar un contenedor que ejecute el comando `ls` como PID 1 y finalice inmediatamente después de completar la tarea.
> <img width="228" height="155" alt="image" src="https://github.com/user-attachments/assets/34ca579c-9828-43e8-b4bc-34a6beca6f93" />

Cuando se ejecuta `ls` en la máquina host, normalmente el proceso es generado como un proceso hijo del PID 1 del sistema, que suele ser `systemd`.
Sin embargo, cuando `ls` se ejecuta dentro de un contenedor, **ese proceso se convierte directamente en el PID 1 del contenedor**.

De manera similar, si se especifica un proceso que permanece en ejecución, como `top`, este se ejecutará como PID 1 dentro del contenedor y **el contenedor seguirá activo mientras `top` esté en funcionamiento**.
Docker permite esta flexibilidad porque **cada contenedor utiliza su propio espacio de procesos (PID namespace)**.

Gracias a esto, es posible que varios contenedores tengan su propio PID 1 de forma aislada, **sin que haya conflictos entre ellos ni con la máquina host**.
> <img width="649" height="287" alt="image" src="https://github.com/user-attachments/assets/bb29a0e8-9415-4d68-b0eb-b868db1d2b24" />

## Características del contenedor

1. Los contenedores se crean a partir de **imágenes**, que contienen todo el entorno necesario para ejecutar una aplicación, incluyendo archivos, bibliotecas y configuraciones.
   > <img width="740" height="328" alt="image" src="https://github.com/user-attachments/assets/a325e653-b35e-4136-b290-9f3ce6330af6" />

2. Cada contenedor funciona de manera **independiente**, con su propio sistema de archivos, red y espacio de procesos, lo que evita interferencias entre contenedores.
   > Por ejemplo, diferentes versiones del contenedor MySQL pueden ejecutarse simultáneamente en la misma máquina host.
   > <img width="705" height="336" alt="image" src="https://github.com/user-attachments/assets/597671f4-cb10-453d-b098-fbfc9cca4a33" />

3. Un contenedor puede ejecutarse **en cualquier lugar donde haya un runtime de contenedores** (como Docker).
   > Esto incluye sistemas como **Windows y macOS** (aunque en estos casos se utiliza internamente una máquina virtual Linux para ejecutar los contenedores).
   > En entornos en la nube, también es posible ejecutar contenedores sobre plataformas compatibles, como **Amazon ECS, Google Cloud Run**, entre otros.
   > <img width="756" height="440" alt="image" src="https://github.com/user-attachments/assets/2c3c4441-38e7-478b-be36-0d69daa293a7" />

### 🧪 Ejemplo práctico

```bash
docker run --name web -d nginx
```

Este comando inicia un contenedor donde el proceso `nginx` se ejecuta como **PID 1**.
Cuando `nginx` termina, el contenedor se **detiene automáticamente**.

---

ご希望があれば、スペイン語話者向けにたとえ話（学校や cocina など）を交えて説明することも可能です。お気軽にお申し付けください。




* Un **contenedor** es un entorno de ejecución ligero que incluye una aplicación y sus dependencias.
* El contenedor se ejecuta como un único **proceso** en el sistema operativo anfitrión.
* Los **estados** comunes incluyen:

  * `running` (en ejecución)
  * `exited` (detenido)
  * `created` (creado, pero no iniciado)

## 1.2 Comandos de contenedor

Los comandos principales para manipular contenedores inician con `docker container`.

Ejemplos:

```bash
docker container run
docker container ls
docker container stop
docker container rm
```

### ✅ Preguntas prácticas (Capítulo 1)

1. ¿Qué significa "proceso" en un contenedor Docker?
2. ¿Qué indica el estado `Exited (0)`?
3. ¿Cuál es el prefijo común de los comandos de contenedor?

### ✅ Respuestas

1. El proceso principal que se ejecuta dentro del contenedor, como un script o servidor.
2. Que el contenedor terminó correctamente sin errores.
3. `docker container`

---

# Capítulo 2: Operaciones básicas con contenedores

## 2.1 Ejecutar un contenedor

```bash
docker container run ubuntu echo "Hello Container"
```

## 2.2 Listar contenedores

```bash
docker container ls
docker container ls --all
```

## 2.3 Detener un contenedor

```bash
docker container stop ID_del_contenedor
```

## 2.4 Eliminar un contenedor

```bash
docker container rm ID_del_contenedor
```

### ✅ Preguntas prácticas (Capítulo 2)

1. Inicia un contenedor usando la imagen `hello-world`.
2. Lista todos los contenedores, incluso los detenidos.
3. Elimina un contenedor que ya está detenido.

### ✅ Respuestas

1.

```bash
docker container run hello-world
```

2.

```bash
docker container ls --all
```

3.

```bash
docker container rm ID
```

---

# Capítulo 3: Ejecutar comandos en línea con contenedor Ruby

## 3.1 Ejecutar comandos al iniciar el contenedor

```bash
docker container run ruby ruby -e "puts 'Hola desde Ruby'"
```

## 3.2 Asignar un nombre al contenedor

```bash
docker container run --name miruby ruby ruby -e "puts 123"
```

## 3.3 Eliminar contenedor automáticamente al detenerse

```bash
docker container run --rm ruby ruby -e "puts '¡Adiós!'"
```

### ✅ Preguntas prácticas (Capítulo 3)

1. Ejecuta un contenedor Ruby que imprima `"Hola Docker"` y que se llame `miruby`.
2. Usa `--rm` y verifica que el contenedor no quede registrado tras ejecutarse.
3. ¿Por qué aparece un error si se intenta reutilizar un nombre ya asignado?

### ✅ Respuestas

1.

```bash
docker container run --name miruby ruby ruby -e "puts 'Hola Docker'"
```

2.

```bash
docker container run --rm ruby ruby -e "puts 'Este contenedor se elimina automáticamente'"
```

3. Porque Docker no permite nombres duplicados. Se debe eliminar primero:

```bash
docker container rm miruby
```

---

# Capítulo 4: Shell interactivo en contenedor Python

## 4.1 Usar Python en modo interactivo

```bash
docker container run -it python
>>> print("Hola Python")
```

### ✅ Preguntas prácticas (Capítulo 4)

1. Inicia un contenedor interactivo y ejecuta `print(3 + 4)`.
2. ¿Cómo se sale del modo interactivo?
3. Explica la función de `-it`.

### ✅ Respuestas

1.

```bash
docker container run -it python
>>> print(3 + 4)
```

2. Con `exit()` o `Ctrl + D`.

3. `-i` mantiene entrada estándar, `-t` asigna terminal. Juntos permiten interacción en vivo.

---

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
