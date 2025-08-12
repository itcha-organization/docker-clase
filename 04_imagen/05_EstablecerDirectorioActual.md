# Establecer el directorio actual con la instrucción `WORKDIR`

## 1. ¿Qué es la instrucción WORKDIR?

* Es una instrucción en Dockerfile para **establecer el directorio de trabajo (directorio actual)**.
* A partir de esta instrucción, todas las órdenes (`RUN`, `COPY`, `CMD`, etc.) se ejecutan tomando este directorio como referencia.
* Si el directorio especificado no existe, se crea automáticamente.

---

## 2. Sintaxis básica

```dockerfile
WORKDIR /ruta/al/directorio
```

---

## 3. Tutorial práctico: comparación entre usar WORKDIR y no usarlo

---

### 3-1. Archivos de ejemplo a preparar

Crea en tu directorio de trabajo dos archivos Dockerfile con estos contenidos:

* **Dockerfile.noworkdir**

```dockerfile
FROM ubuntu:22.04

RUN mkdir -p /app

RUN echo "Archivo en /app" > /app/sample.txt

RUN ls -l sample.txt
```

* **Dockerfile.workdir**

```dockerfile
FROM ubuntu:22.04

WORKDIR /app

RUN mkdir -p /app

RUN echo "Archivo en /app" > sample.txt

RUN ls -l sample.txt
```

---

### 3-2. Construcción de las imágenes y explicación

#### Sin WORKDIR

```bash
docker build -f Dockerfile.noworkdir -t noworkdir-image .
```

Verás que falla con un error parecido a:

```
/bin/sh: 1: sample.txt: not found
```

¿Por qué? Porque el comando `RUN ls -l sample.txt` se ejecuta en el directorio por defecto `/`, donde no existe `sample.txt` (el archivo fue creado en `/app`).

---

#### Con WORKDIR

```bash
docker build -f Dockerfile.workdir -t workdir-image .
```

Esta construcción se completa sin errores y muestra que `sample.txt` existe en `/app`, porque el comando `RUN` se ejecuta dentro del directorio `/app`.

---

## 4. Resumen

| Punto               | Explicación                                                                                                |
| ------------------- | ---------------------------------------------------------------------------------------------------------- |
| Sin WORKDIR         | El directorio por defecto es `/`. Los archivos pueden no encontrarse si no se especifican rutas absolutas. |
| Con WORKDIR         | Los comandos se ejecutan en el directorio definido, facilitando la referencia a archivos y carpetas.       |
| Creación automática | Si el directorio no existe, WORKDIR lo crea automáticamente.                                               |


Si deseas, puedo ayudarte a crear ejercicios prácticos o explicaciones adicionales. ¡Solo dime!
