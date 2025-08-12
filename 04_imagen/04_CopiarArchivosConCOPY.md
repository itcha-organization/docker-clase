# 📦 Copiar archivos con la instrucción `COPY` en un Dockerfile

## 1. ¿Qué es la instrucción `COPY`?

`COPY` es una instrucción que:

* Copia archivos o directorios **desde tu máquina local (host)** al sistema de archivos **dentro de la imagen** durante la construcción.
* Se usa para agregar configuraciones, scripts de instalación, binarios o cualquier recurso necesario para que el contenedor funcione correctamente.

---

## 2. Sintaxis básica

```dockerfile
COPY [ruta_origen] [ruta_destino]
```

* **ruta\_origen** → relativa al **contexto de construcción** (la carpeta donde ejecutas `docker build`).
* **ruta\_destino** → ruta en la imagen; si no existe, se crea automáticamente.

---

## 3. Ejemplo práctico: Ubuntu 22.04 + script personalizado

Supongamos que quieres crear una imagen basada en Ubuntu 22.04 que:

* Copie un script `install_tools.sh` a `/usr/local/bin/`
* Copie un archivo de configuración `app.conf` a `/etc/myapp/`

```dockerfile
FROM ubuntu:22.04

# Actualizar el sistema e instalar paquetes básicos
RUN apt-get update && apt-get install -y curl vim

# Copiar script de instalación
COPY install_tools.sh /usr/local/bin/install_tools.sh

# Copiar archivo de configuración
COPY app.conf /etc/myapp/app.conf

# Dar permisos de ejecución al script
RUN chmod +x /usr/local/bin/install_tools.sh
```

---

## 4. Puntos clave sobre `COPY`

* **Solo** puedes copiar archivos dentro del contexto de construcción (la carpeta desde donde corres `docker build`).
* Las rutas de destino que no existan serán creadas.
* Los permisos se mantienen tal como están en el host, salvo que los cambies luego con `RUN chmod`.
