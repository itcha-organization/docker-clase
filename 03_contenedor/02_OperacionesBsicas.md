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
