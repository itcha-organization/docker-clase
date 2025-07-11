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

## Estado de los contenedores
Hay siete estados para los contenedores: centrarse en PID1 le ayudará a identificar con precisión cada estado.

| **Estado**     | **Descripción**                                      | **Estado del PID 1**                           |
| -------------- | ------------------------------------------- | ---------------------------------------------- |
| **created**    | Creado, pero aún no iniciado                | No existe (aún no ha iniciado)                 |
| **running**    | En ejecución                                | En ejecución (proceso activo)                  |
| **paused**     | En pausa (puede reanudarse)                 | En pausa (interrumpido por `SIGSTOP`)          |
| **restarting** | Reiniciando                                 | Detenido temporalmente o reiniciando           |
| **removing**   | En proceso de eliminación                   | Detenido o no existe (eliminándose)            |
| **exited**     | Finalizado (con código de salida)           | Finalizado                                     |
| **dead**       | Finalizado con error, estado no recuperable | Finalizado o desconocido (terminación anormal) |
* `restarting` y `removing` son estados que normalmente aparecen de forma temporal durante el proceso de gestión de contenedores, y los usuarios rara vez necesitan preocuparse por ellos.
* `dead` se muestra cuando ocurre un error, como una falla al detener o eliminar un contenedor. No hay forma de recuperar un contenedor en este estado, por lo que es necesario eliminarlo.
> <img width="684" height="547" alt="image" src="https://github.com/user-attachments/assets/abae7a8a-d900-4d1a-86d9-7344fc4070e7" />
