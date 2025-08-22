# Configuración de Cloudflare R2 como almacenamiento en Internet

## 1. Introducción

Al desplegar una aplicación Laravel en un entorno de producción, **guardar archivos directamente dentro de un contenedor Docker puede causar problemas**.
Los contenedores son entornos temporales: cada vez que se reinician o se redeploya la aplicación, los archivos almacenados internamente desaparecen. Esto significa que las imágenes subidas por los usuarios no se conservarán de manera segura.

Para resolver este problema, se necesita un **almacenamiento persistente separado de la aplicación**.

En este material, utilizaremos **Cloudflare R2** como almacenamiento de objetos en la nube.
R2 ofrece 10 GB de almacenamiento gratuito y cuenta con paquetes para Laravel, lo que facilita la integración.

## 2. Creación de un bucket en Cloudflare R2

1. Inicia sesión en [Cloudflare Dashboard](https://dash.cloudflare.com).
2. Ve al menú **R2**.
   > <img width="1175" height="593" alt="image" src="https://github.com/user-attachments/assets/22faa566-a79a-4249-84bc-c88791b63aee" />
3. Haz clic en **Create bucket**.
   > <img width="1176" height="562" alt="image" src="https://github.com/user-attachments/assets/54840232-4e86-447e-9f01-4d9a96a076f5" />
4. Haz clic en **Create bucket**.
   > <img width="1180" height="566" alt="image" src="https://github.com/user-attachments/assets/8e6f1c45-c175-4c80-a19d-045d49f2b6f0" />

## 3. Obtención de credenciales

1. Haz clic en **API** y abre **Manage R2 API Tokens**.
   > <img width="1175" height="571" alt="image" src="https://github.com/user-attachments/assets/5ecf7d7d-8c9d-45b6-8e8a-6562fc8b8816" />
3. Haz clic en **Create Account API token**.
   > <img width="1195" height="493" alt="image" src="https://github.com/user-attachments/assets/09e9c6c5-3c00-4b23-9c18-593579cc69d1" />
3. Selecciona 'Permisos: lectura/escritura', 'Bucket: test' y haz clic en **Create Account API token**.
   > <img width="1048" height="770" alt="image" src="https://github.com/user-attachments/assets/1856ab06-8d3c-4828-b47c-c96b6b45d7d7" />

5. Anote las credenciales:
   * `Access Key ID`
   * `Secret Access Key`
   * `Endpoint URL` (ejemplo: `https://<accountid>.r2.cloudflarestorage.com`)
    > <img width="1193" height="788" alt="image" src="https://github.com/user-attachments/assets/38b8de4e-bfa0-443f-9fc3-5fa4a2e33947" />
⚠️ Importante: guárdalas en un lugar seguro, solo se muestran una vez.

## 4. Configuración en Laravel

### 4.1 Instalar dependencias
Laravel usa el driver para conectarse a R2, por lo que necesitas:
```bash
composer require league/flysystem-aws-s3-v3 "^3.0"
```
### 4.2 Variables de entorno
Agrega en `.env`:
```env
AWS_ACCESS_KEY_ID=tu_access_key
AWS_SECRET_ACCESS_KEY=tu_secret_key
AWS_DEFAULT_REGION=auto
AWS_BUCKET=laravel-storage
AWS_ENDPOINT=https://<accountid>.r2.cloudflarestorage.com
```
### 4.3 Configuración en `config/filesystems.php`
En el arreglo `disks`, agrega (o ajusta) la configuración de `s3`:
```php
<?php

return [

...Omitido...

    'disks' => [

        ...Omitido...

        's3' => [
            ...Omitido...
        ],
        // ★Añade lo siguiente.
        'r2' => [
             'driver' => 's3',
             'key' => env('CLOUDFLARE_R2_ACCESS_KEY_ID'),
             'secret' => env('CLOUDFLARE_R2_SECRET_ACCESS_KEY'),
             'region' => 'us-east-1', // Cloudflare R2 no tiene regiones específicas, por lo que 'us-east-1' está bien.
             'bucket' => env('CLOUDFLARE_R2_BUCKET'),
             'endpoint' => env('CLOUDFLARE_R2_ENDPOINT'),
             'use_path_style_endpoint' => env('CLOUDFLARE_R2_USE_PATH_STYLE_ENDPOINT', false),
             'throw' => true,
        ],
        // ★Añade hasta aquí
    ],
```
Con esto, Laravel ya podrá almacenar archivos en R2.



## 5. Modificación del código

### 5.1 Subir archivos a R2

```php
use Illuminate\Support\Facades\Storage;

public function store(Request $request)
{
    $file = $request->file('imagen');
    $path = Storage::disk('s3')->put('productos', $file);

    return response()->json([
        'url' => Storage::disk('s3')->url($path)
    ]);
}
```

### 5.2 Eliminar archivos de R2

```php
use Illuminate\Support\Facades\Storage;

public function destroy($id)
{
    $producto = Producto::findOrFail($id);

    // Eliminar imagen asociada en R2
    if ($producto->imagen) {
        Storage::disk('s3')->delete($producto->imagen);
    }

    $producto->delete();

    return response()->json(['mensaje' => 'Producto eliminado correctamente']);
}
```

---

## 6. Verificación en producción

1. Sube la aplicación a tu servidor.
2. Ejecuta las migraciones y caches:

```bash
php artisan config:cache
php artisan route:cache
```

3. Prueba subiendo un archivo desde tu aplicación.
4. Confirma en el **Dashboard de R2** que el archivo se guardó en el bucket.

---

## ✅ Resumen

* Cloudflare R2 es totalmente compatible con la API S3.
* Configuramos `.env` y `config/filesystems.php` para que Laravel use R2.
* Ajustamos el código de subida y eliminación de archivos usando `Storage::disk('s3')`.
* Con esto, tu aplicación ya está lista para producción con almacenamiento en la nube sin costos de salida.

---

👉 ご希望に応じて、この教材を「講義用スライド形式」や「ハンズオン実習手順書」風に書き換えることも可能です。

先生の研修教材としては、どちらの形にまとめましょうか？
