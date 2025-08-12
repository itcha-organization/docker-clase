# Copiar archivos con la instrucción Dockerfile `COPY`

## Objetivo

En este módulo, aprenderás a copiar archivos con la instrucción Dockerfile `COPY`

## 1. ¿Qué es la instrucción `COPY`?

* Es una instrucción en Dockerfile que se usa para **copiar archivos o directorios desde el host (tu PC, por ejemplo) al sistema de archivos dentro del contenedor**.
* Se utiliza para incluir archivos de configuración, programas u otros recursos necesarios durante la construcción de la imagen.


## 2. Sintaxis básica

```dockerfile
COPY [ruta_origen] [ruta_destino]
```

* La ruta de origen es relativa al contexto de construcción (el directorio donde ejecutas `docker build`).
* La ruta de destino es la ruta dentro del contenedor, puede ser absoluta o relativa.


## 3. Ejemplo: copiar un archivo simple

Copiar el archivo `config.txt` al contenedor en `/app/config.txt`:

```dockerfile
COPY config.txt /app/config.txt
```


## 4. Ejemplo: copiar un directorio completo

Copiar el directorio `src` completo al contenedor en `/app/src`:

```dockerfile
COPY src /app/src
```


## 5. Puntos clave sobre COPY

* Solo puedes copiar archivos o carpetas que estén dentro del **contexto de construcción** (la carpeta desde donde ejecutas `docker build`). No se pueden copiar archivos fuera de ese contexto.
* Si la carpeta de destino no existe dentro del contenedor, se creará automáticamente.
* Los permisos de los archivos se mantienen igual al copiarlos.


## 6. Ejercicios prácticos

### Ejercicio 1

Escribe una línea en Dockerfile para copiar `app.py` del directorio actual al contenedor en `/usr/src/app/`.


### Ejercicio 2

Copia la carpeta `static` al contenedor en `/usr/src/app/static`.


### Ejercicio 3

En el Dockerfile, escribe la instrucción para copiar `config/settings.yaml` al contenedor en `/etc/myapp/settings.yaml`.
