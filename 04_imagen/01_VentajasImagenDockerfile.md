# Ventajas de Docker Imagen y Dockerfile

### ― De “hacerlo a mano cada vez” a “definir una vez y repetir”

## 1. Una analogía simple

* **Contenedor**: un plato cocinado en el momento
* **Imagen**: un chef especializado solo para ese plato
* **Dockerfile**: la receta paso a paso

🍳 Hacer todo desde cero cada vez es cansado.
<br>📄 Pero si escribes la receta, tú y otras personas pueden preparar el mismo plato con facilidad.
<br>📦 Y si se forma un chef especializado, No necesita leer la receta cada vez. Ya sabe los pasos, ingredientes y tiempos y lo hace rápido, siempre igual, en cualquier cocina (es decir, en cualquier entorno).

## 2. ¿Por qué necesitamos imágenes y Dockerfile?

Hasta ahora has aprendido a:

* Ejecutar contenedores
* Exponer puertos
* Establecer variables de entorno
* Ejecutar comandos dentro del contenedor
* Usar contenedores de forma interactiva

Pero tal vez te hayas preguntado:

> ☹️ “¡Los comandos son demasiado largos cada vez!”
> <br>☹️ “Tener que configurar el entorno a mano es tedioso…”
> <br>☹️ “¿Cómo comparto mi entorno con otros de forma sencilla?”

👉 Todo eso se puede **automatizar, reutilizar y compartir** gracias a **Docker Image** y **Dockerfile**

## 3. Ejemplo práctico: ¿qué diferencia hacen?

### 🚶 Sin Dockerfile (solo contenedor)
```bash
docker container run -it ubuntu:22.04 bash
apt update
apt install -y vim
```
* Cada vez instalas vim manualmente
* La configuración se pierde al cerrar el contenedor
* No es fácil compartir ese entorno

### 🚀 Con Dockerfile
Dockerfile:
```Dockerfile
FROM ubuntu:22.04
RUN apt-get update
RUN apt-get install -y vim
```

```bash
docker image build -t myubuntu .
docker container run -it myubuntu bash
```
✅ Con una sola imagen:
* Siempre tienes el mismo entorno listo para usar
* Las dependencias ya están instaladas
* Lo puedes compartir fácilmente con otros/as

## 4. El valor de Dockerfile: “Reproducibilidad” y “Colaboración”
| Lo aprendido antes      | Con Dockerfile                 |
| ----------------------- | ------------------------------ |
| Crear el entorno a mano | Crear una imagen reutilizable  |
| Configurar manualmente  | Automatizar el proceso         |
| Difícil de compartir    | Dockerfile es portable y claro |
| Comandos largos         | Solo necesitas `docker run`    |

## En resumen
> 💬 Antes: “¡Con que el contenedor funcione, basta!”
> 💡 Ahora: “Quiero **recrear ese entorno fácilmente** y **compartirlo con otros**.”
Aprender a usar **Docker Image** y **Dockerfile** es el siguiente paso para aprovechar Docker de forma profesional.

## ¿Qué aprenderás a continuación?
* Cómo se escribe un Dockerfile
* Cómo crear tu propia imagen
