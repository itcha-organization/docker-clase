# Migración a Docker Desktop
Anteriormente, Docker se instalaba directamente en una máquina virtual Ubuntu.

Sin embargo, usar `Docker Desktop` es más cómodo, así que sigue estos pasos para instalar `Docker Desktop`.

# Desinstalar completamente Docker de Ubuntu en WSL

Para eliminar completamente Docker de tu instalación de Ubuntu que se ejecuta en Windows Subsystem for Linux (WSL), sigue estos pasos:

## 0. Abrir el terminal de Ubuntu en `Windows Terminal`

> ![image](https://github.com/user-attachments/assets/e7c38eb0-e796-444f-bb47-38074d2c85f4)

## 1. Detener los servicios de Docker
Si Docker se está ejecutando, detén todos los servicios de Docker:
```bash
sudo service docker stop
```

## 2. Desinstalar los paquetes de Docker
Elimina Docker Engine, CLI, containerd y otros paquetes relacionados:
```bash
sudo apt-get purge -y docker-engine docker docker.io docker-ce docker-ce-cli containerd runc
```

## 3. Eliminar las dependencias de Docker
Limpia los paquetes no utilizados relacionados con Docker:
```bash
sudo apt-get autoremove -y --purge docker-engine docker docker.io docker-ce
```
```bash
sudo apt-get autoclean
```

## 4. Eliminar los datos y configuraciones de Docker
Elimina todas las imágenes, contenedores, volúmenes y archivos de configuración de Docker:
```bash
sudo rm -rf /var/lib/docker /etc/docker /var/run/docker.sock
```

## 5. Eliminar el grupo de Docker
Si habías creado el grupo `docker`, puedes eliminarlo con:
```bash
sudo groupdel docker
```

## 6. Verificar la eliminación
Verifica que Docker haya sido eliminado correctamente:
```bash
docker --version
```
Si Docker fue completamente eliminado, deberías ver un error como “command not found” u otro mensaje similar.

<details>

<summary>Ejemplo de ejecución de comandos
</summary>

> ![image](https://github.com/user-attachments/assets/0012f0eb-9c9e-425d-b79a-8042dfb8892c)

</details>

# Instalar Docker Desktop en Windows
Para instalar Docker Desktop en un entorno Windows, sigue los pasos detallados a continuación. Estos pasos aplican a Windows 10/11 (64-bit) con WSL 2 habilitado.

## 0. Verificar los requisitos del sistema
Antes de comenzar la instalación, asegúrate de cumplir con los siguientes requisitos:
* Windows 10 64-bit: versión 1903 o superior, o Windows 11.
* Subsistema de Windows para Linux 2 (WSL 2) habilitado.
* Virtualización habilitada en la BIOS.
* Una cuenta con privilegios de administrador.

> ![image](https://github.com/user-attachments/assets/331a3bfe-fb12-433c-9c55-c7e1648eafcc)
> ![image](https://github.com/user-attachments/assets/1c107914-f66e-4145-bcb8-26e8ab7f304f)

## 1. Verificar el tipo de procesador
Antes de descargar el instalador de Docker Desktop, anote el tipo de procesador para elegir la versión de Docker Desktop que sea compatible con el tipo de procesador de su PC. 
> ![image](https://github.com/user-attachments/assets/7861035b-981c-4952-a273-d38c0859c6ec)
> ![image](https://github.com/user-attachments/assets/307e60b7-39fe-4727-8ddb-a967d69b706a)

En **Información del sistema**, busque: **Tipo de sistema**
- Si dice `x64-based PC (PC basado en x64)` → Descargue el instalador para *Windows – AMD64*
- Si dice `ARM-based PC (PC basado en ARM)` → Descargue el instalador para *Windows – ARM64*

## 2. Descargar el instalador de Docker Desktop
Descarga el instalador oficial de Docker Desktop desde el sitio web oficial: https://www.docker.com/products/docker-desktop/

Selecciona la versión adecuada según tu arquitectura:
> ![image](https://github.com/user-attachments/assets/47396400-75c0-490c-a3bc-76b8b51d69ad)

## 3. Ejecutar el instalador
1. Haz doble clic en el archivo descargado (`Docker Desktop Installer.exe`).
2. En el asistente de instalación, selecciona:
   * ✔️ Use WSL 2 instead of Hyper-V (recommended)
   * ✔️ Add shortcut to desktop (opcional)
3. Haz clic en **OK**.
4. Espera a que finalice la instalación.
> ![image](https://github.com/user-attachments/assets/9e87456f-aa6f-4f70-b94f-937962cb06a0)


## 4. Reiniciar el sistema (si es necesario)
Si el instalador lo solicita, reinicia tu computadora para completar la instalación.

## 5. Iniciar Docker Desktop

Una vez instalado:

1. Abre **Docker Desktop** desde el menú de inicio.
   > ![image](https://github.com/user-attachments/assets/5510761a-12e7-44cf-99c2-4d655a148b3d)
2. En la primera ejecución, se le pedirá que acepte las Condiciones de uso.
   > ![image](https://github.com/user-attachments/assets/e2d032cf-1f2c-46fd-a8c8-94ad4012ae37)
3. Si se le pide que inicie sesión, sáltela.
   > ![image](https://github.com/user-attachments/assets/6e21540e-82e3-4a89-ad5a-d3c7bc4884fa)
4. Espere hasta que se inicie Docker Engine y aparezca la siguiente pantalla.
   > ![image](https://github.com/user-attachments/assets/4fcc6363-9396-40de-806b-9d811f003257)

> [!CAUTION]
>  Las empresas con 250 empleados o más, o con ingresos anuales superiores a 10 millones de dólares (aproximadamente 1.500 millones de yenes), deben suscribirse al plan Pro o superior para utilizar Docker Desktop con fines comerciales.

## 6. verificar si la integración con WSL 2 está habilitada
Cuando instalas `Docker Desktop`, una distribución dedicada llamada `docker-desktop` se añade automáticamente en la WSL y Docker se instala allí.
Esto significa que Docker no está instalado en Ubuntu.

Para poder operar el Docker en `docker-desktop` desde el terminal de Ubuntu, el ajuste `WSL Integration` debe estar habilitado.

1. Haz clic en el ícono de engranaje ⚙️ (Settings / Configuración).
2. Ve a la sección **“Resources” > “WSL Integration”**.
3. Asegúrate de que la opción **“Enable integration with my default WSL distro”** esté activada (✔️).
4. Si hizo cambios, haga clic en **Apply & Restart** .
> ![image](https://github.com/user-attachments/assets/7750116e-c3e0-4128-b069-73c9290cef3e)

## 7. Verificar la instalación

Abra el terminal de Ubuntu en `Windows Terminal` y ejecute:
```bash
docker --version
```

Deberías ver una salida similar a:
```bash
Docker version 24.x.x, build xxxxxxx
```

También puedes probar con:
```bash
docker run hello-world
```
Para confirmar que Docker puede ejecutar contenedores correctamente.
