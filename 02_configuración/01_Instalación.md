# Instalación de WSL2 y Docker

## 0. ¿Qué es WSL2?

## 1. Instalación de Windows Terminal

Revisar el uso del espacio de almacenamiento. Es deseable tener unos 20GB libres para instalar WSL2 y Docker.
> ![image](https://github.com/user-attachments/assets/f7fb914a-09dc-4231-916a-1f4a9a07afd6)

Abre `Microsoft Store`, busca y obtén `Windows Terminal`.
- Si `Windows Terminal` ya está instalado, obtenga la última versión.
- Si te preguntan si quieres iniciar sesión con tu cuenta de MS y compartir, básicamente puedes decir que No.
> ![image](https://github.com/user-attachments/assets/f57fcad4-9ab8-4fe5-8739-cee43dceeaa5)
> ![image](https://github.com/user-attachments/assets/69bf5f78-73f5-4c36-9464-f09e237b7a2c)
> ![image](https://github.com/user-attachments/assets/5e8367f6-c479-4ba6-ab58-f107002d7b3d)

## 2. Instalación de WSL2

Antes de instalar WSL2, compruebe que la versión del sistema operativo cumple los requisitos.
> https://learn.microsoft.com/es-mx/windows/wsl/install#prerequisites
> Debes ejecutar Windows 10 versión 2004 y posteriores (compilación 19041 y posteriores) o Windows 11 para usar los comandos siguientes. Si está en versiones anteriores, consulte la página de instalación manual.

> ![image](https://github.com/user-attachments/assets/49e70e12-1fee-43e7-b941-df347c19dbd5)
> ![image](https://github.com/user-attachments/assets/0cee307e-add0-4742-a537-d515925b11d3)

Inicie `PowerShell` con privilegios administrativos.
> ![image](https://github.com/user-attachments/assets/7c81782c-eba1-44c3-a909-0265d03e3f5f)

Ejecute el siguiente comando para instalar WSL2.
- Este comando habilita las características necesarias de Windows e instala el kernel de Linux y Ubuntu a la vez.
```
wsl --install
```
> ![image](https://github.com/user-attachments/assets/b2bb1d0b-728a-410b-98f1-844400536d45)
> **Pulse Sí cuando se le pregunte si desea ejecutarlo.**

Reinicie el PC una vez que la instalación se haya completado con éxito, como se indica a continuación.
> ![image](https://github.com/user-attachments/assets/3a9566ff-432a-462e-983e-01e22444884c)

Tras reiniciar el PC, se abrirá automáticamente el cuadro de diálogo de Ubuntu y comenzará la descarga.
> ![image](https://github.com/user-attachments/assets/215e29fd-d50a-450f-9a7f-d892239c0672)

Una vez finalizada la descarga de Ubuntu, se te pedirá que configures un nombre de usuario y una contraseña.
**Asegúrate de anotar tu nombre de usuario y contraseña para no olvidarlos.**
> ![image](https://github.com/user-attachments/assets/60dc2862-0751-411e-8330-fd796d8de4a4)

## 3. Instalación de Docker
Abre el Terminal de Windows y abre una nueva pestaña de Ubuntu.
> ![image](https://github.com/user-attachments/assets/a2591989-e98f-4ef0-9795-ef5efe0b7bf7)

Ejecute el siguiente comando para instalar Docker.
```
curl -fsSL https://get.docker.com -o get-docker.sh
```
```
sudo sh get-docker.sh
```
> ![image](https://github.com/user-attachments/assets/7d46e457-8032-4791-aaa3-0cfd71d4995e)

Si se muestra la versión, la instalación ha finalizado correctamente.
```
docker -v
```
> ![image](https://github.com/user-attachments/assets/5e228c0e-5f94-412c-aa73-d977038b39fe)

3-2. Configuración inicial de Docker (establecimiento de privilegios y comprobación del funcionamiento)
La ejecución del siguiente comando para comprobar la operación dará lugar a un error debido a una configuración de autorización insuficiente.
```
docker run hello-world
```
> ![image](https://github.com/user-attachments/assets/4fab2c29-7aba-42df-baf4-93f9c5bfb220)

Ejecute los siguientes comandos para establecer las autorizaciones.
```
sudo service docker start
```
```
sudo usermod -aG docker $USER
```
```
groups $USER
```
```
su - ${USER}
```
> ![image](https://github.com/user-attachments/assets/2363941b-e259-4b8e-b13f-e57614cfc2a3)


## 4. Ejecución de un juego JavaScript en Docker.
```
wget https://raw.githubusercontent.com/pianomanfrazier/lemmings-clone/master/server/docker-compose.yml
```
```
cat ./docker-compose.yml
```
```
docker compose up
```

Abra http://localhost:3000 en su navegador

GitHub del juego : https://github.com/pianomanfrazier/lemmings-clone
