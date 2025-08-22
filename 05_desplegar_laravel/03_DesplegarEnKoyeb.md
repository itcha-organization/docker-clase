# Desplegar la imagen de Docker en Koyeb

## Crear un nuevo servicio web en Koyeb

Inicia sesión en Koyeb. https://www.koyeb.com/

Seleccione `Docker` y cree un servicio web.
> <img width="982" height="517" alt="image" src="https://github.com/user-attachments/assets/bde140a1-57d0-48a4-8fd3-c78e2a00346a" />

Especifica una imagen publicada en DockerHub.
> <img width="811" height="481" alt="image" src="https://github.com/user-attachments/assets/a870e5e8-da18-449e-a0de-13cdf6b03c1a" />

Seleccione la instancia `Free` y haga clic en `Next`.
> <img width="784" height="504" alt="image" src="https://github.com/user-attachments/assets/f50312c2-3ef2-4f10-80b9-7b0bc16a3d8a" />

Abra la sección `Environment variables and files` y haga clic en `Raw editor`.
> <img width="928" height="490" alt="image" src="https://github.com/user-attachments/assets/1ba6897f-9ffb-44f8-af3a-bcd29d1a1cd5" />

Pega el contenido del archivo `.env` y guárdalo.
> <img width="1063" height="624" alt="image" src="https://github.com/user-attachments/assets/3732a41a-4a0a-457a-8583-ec9f61aa3828" />

Haga clic en `Deploy`.
> <img width="724" height="512" alt="image" src="https://github.com/user-attachments/assets/374ebccf-b827-45a9-bf16-a0515a9513d6" />

Una vez finalizado el proceso, abra la URL.
> [!CAUTION]
> El acceso puede ser denegado en la red de la institución. En tales casos, utilice una conexión a Internet alternativa.

> <img width="745" height="508" alt="image" src="https://github.com/user-attachments/assets/adf9b9d9-08b9-4ac3-b0af-1f0972817787" />

⇒ Si el código fuente no soporta `https`, se producen los siguientes errores de `Mixed Content`
> <img width="1064" height="532" alt="image" src="https://github.com/user-attachments/assets/14056f6f-0354-4a74-ae42-ac55c36bb9b2" />

## Modificación del código para el entorno de producción

Modifique el archivo `AppServiceProvider.php` de la siguiente manera.
Esto es para que todas las URL generadas se conviertan a HTTPS solo en el entorno de producción (production).
```php
<?php
...Omitido...
use Illuminate\Support\Facades\URL;　// ★Añade esta línea

class AppServiceProvider extends ServiceProvider
{
    ...Omitido...
    public function boot(): void
    {
        // ★Añade lo siguiente.
        if (config('app.env') === 'production') {
            URL::forceScheme('https');
        }
        // ★Añade hasta aquí

        Vite::prefetch(concurrency: 3);
    }
}
```

Si hay partes en las que la URL absoluta del entorno local está codificada de forma rígida, corríjala a una URL relativa.
En este caso, corregir `Catalogo.vue` y `Categorias.vue`.
- Catalogo.vue
  ```diff
  - const urlBase = 'http://127.0.0.1:8000/api/'
  const urlBase = '/api/'
  ```
- Categorias.vue
  ```diff
  - const url = "http://127.0.0.1:8000/api/categorias";
  const url = "/api/categorias";
  ```
## Crear una nueva versión de la imagen
Ejecute el siguiente comando e introduzca la contraseña en el prompt que aparece. (Omita este paso si ya ha iniciado sesión.)
```
docker login -u <nombre_de_usuario>
```

Después de modificar el código, construye la imagen con la etiqueta `v1.1`.
```
docker image build -t laravel-app:v1.1 .
```

Etiquetar la imagen local para asociarla con el repositorio remoto.
```
docker tag <nombre_imagen_local>:<etiqueta> <nombre_usuario>/<nombre_repositorio>:<etiqueta>
```
> <img width="1039" height="71" alt="image" src="https://github.com/user-attachments/assets/6f994bfe-2088-434c-ba85-c204d8b74bd4" />

Subir la imagen al repositorio remoto
```
docker push <nombre_usuario>/<nombre_repositorio>:<etiqueta>
```
> <img width="897" height="74" alt="image" src="https://github.com/user-attachments/assets/7dd4435b-af80-41e6-b85f-644077a93cb6" />

Una vez finalizado el envío de imágenes, actualice la etiqueta de imagen `Setting`>`Source` en Koyeb.
> <img width="870" height="357" alt="image" src="https://github.com/user-attachments/assets/ad2ae829-4f36-421a-9a27-72d26157b09d" />

## Modificación las variables de entorno para el entorno de producción

Modifique los valores de las variables de entorno para el entorno de producción como se indica a continuación.

- APP_DEBUG: `false`
- APP_ENV: `production`
- APP_URL: URL pública de su servicio web
- LOG_LEVEL: `info`
- `SESSION_SECURE_COOKIE=true` ←Crear nuevo
> <img width="704" height="761" alt="image" src="https://github.com/user-attachments/assets/cf108be0-7cbd-4f38-b03b-428aded71f5f" />

Una vez realizadas las modificaciones, haga clic en `Save and deploy`.
> <img width="596" height="642" alt="image" src="https://github.com/user-attachments/assets/05e2bdb0-b978-4f03-86f0-18722c9cd5af" />
