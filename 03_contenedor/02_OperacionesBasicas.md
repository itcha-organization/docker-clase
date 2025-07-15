# Operaciones básicas con contenedores

## 2.1 Ejecutar un contenedor

### Sintaxis:
```bash
docker container run [opciones] IMAGEN [COMANDO] [ARGUMENTOS...]
```
* `IMAGEN`: el nombre de la imagen que se va a usar (por ejemplo, `ubuntu`).
* `COMANDO` y `ARGUMENTOS`: comando que se ejecutará dentro del contenedor.

### Opciones comunes:

| Opción   | Descripción                                                            | Ejemplo                |
| -------- | ---------------------------------------------------------------------- | ---------------------- |
| `--name` | Asigna un nombre al contenedor                                         | `--name mi_contenedor` |
| `-d`     | Ejecuta el contenedor en segundo plano (modo *detached*)               | `-d`                   |
| `--rm`   | Elimina el contenedor automáticamente al detenerse                     | `--rm`                 |
| `-it`    | Permite la interacción con el contenedor (modo interactivo + terminal) | `-it ubuntu bash`      |

### Pruebe lo siguiente:
```bash
docker container run hello-world
```
```bash
docker container run -it ubuntu bash
```
> Este comando ejecuta bash en ubuntu en modo interactivo.
> En modo interactivo, se puede ejecutar cualquier comando en el contenedor.
> ```bash
> whoami
> ```
> ```bash
> head -n 4 /etc/os-release
> ```
> ```bash
> echo $SHELL
> ```

## 2.2 Listar contenedores
### Sintaxis:
```bash
docker container ls [opciones]
```

### Opciones comunes:
| Opción           | Descripción                                                  | Ejemplo                           |
| ---------------- | ------------------------------------------------------------ | --------------------------------- |
| `--all`, `-a`    | Muestra todos los contenedores, incluso los detenidos        | `docker container ls -a`          |
| `--filter`, `-f` | Filtra contenedores según un criterio (estado, nombre, etc.) | `--filter "status=exited"`        |
| `--format`       | Muestra la salida en un formato personalizado                | `--format "{{.ID}}: {{.Status}}"` |

### Pruebe lo siguiente:
```bash
docker container ls
```
```bash
docker container ls --all
```

## 2.3 Detener un contenedor
### Sintaxis
```bash
docker container stop [opciones] CONTENEDOR [CONTENEDOR...]
```
* `CONTENEDOR`: puede ser el ID o el nombre del contenedor.

### Pruebe lo siguiente:
Utilice `docker container ls` para comprobar el ID del contenedor de Ubuntu y elimínelo especificando su ID como argumento a `docker container stop`.

## 2.4 Eliminar un contenedor
## Sintaxis:
```bash
docker container rm [opciones] CONTENEDOR [CONTENEDOR...]
```
* `CONTENEDOR`: ID o nombre del contenedor a eliminar.

### Opciones comunes:
| Opción            | Descripción                                                            | Ejemplo |
| ----------------- | ---------------------------------------------------------------------- | ------- |
| `-f`, `--force`   | Fuerza la eliminación del contenedor en ejecución (lo detiene primero) | `-f`    |

### Pruebe lo siguiente:
Elimina los contenedores parados con `docker container rm`.

## 2.5 Probar las opciones del comando `run`

### Nombrar contenedor: `docker container run --name <nombre>`
Para detener o eliminar un contenedor, es necesario especificar el ID del contenedor o su nombre. Sin embargo, ambos son valores generados aleatoriamente por defecto, por lo que es necesario verificarlos previamente.
Asignar un nombre personalizado al contenedor permite evitar la necesidad de comprobar esta información cada vez.
```bash
docker container run --name hello hello-world
```
```bash
docker container ls --all
```
```bash
docker container rm hello
```

### Eliminar automáticamente los contenedores al detener: `docker container run --rm`
Si intentas iniciar un contenedor con el nombre hello usando el siguiente comando dos veces, se producirá un error.
Esto se debe a que el contenedor hello que se inició anteriormente todavía existe con el estado Exited
```bash
docker container run --name hello hello-world
```
Si inicias un contenedor con la opción `--rm`, este se eliminará automáticamente al finalizar, sin necesidad de usar `docker container rm` cada vez.
```bash
docker container run --name hello2 --rm hello-world
```
```bash
docker container ls --all
```

## Información de referencia: ¿Qué son los comandos antiguos (legacy) de Docker?
Docker solía utilizar una serie de **comandos antiguos** que hoy en día han sido reemplazados por una estructura más organizada basada en subcomandos.
Aunque muchos de estos comandos antiguos **aún funcionan**, se consideran obsoletos y se recomienda utilizar las versiones nuevas.

Como el número de comandos ha aumentado demasiado, se han reorganizado en un nuevo formato mediante subcomandos.

### Ejemplos de comandos antiguos y sus equivalentes actuales
| Comando antiguo (obsoleto) | Comando actual recomendado | Descripción                        |
| -------------------------- | -------------------------- | ---------------------------------- |
| `docker ps`                | `docker container ls`      | Listar contenedores                |
| `docker rm`                | `docker container rm`      | Eliminar contenedores              |
| `docker run`               | `docker container run`     | Crear y ejecutar un contenedor     |
| `docker images`            | `docker image ls`          | Listar imágenes                    |
| `docker rmi`               | `docker image rm`          | Eliminar una imagen                |
| `docker build`             | `docker image build`       | Construir una imagen               |
| `docker exec`              | `docker container exec`    | Ejecutar comandos en un contenedor |

<img width="782" height="492" alt="image" src="https://github.com/user-attachments/assets/389ae3f6-20db-443b-9493-c27231d36afe" />
