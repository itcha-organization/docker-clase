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
<br>⚠️ Importante: guárdalas en un lugar seguro, solo se muestran una vez.
   * `Access Key ID`
   * `Secret Access Key`
   * `Endpoint URL` (ejemplo: `https://<accountid>.r2.cloudflarestorage.com`)
     > <img width="1193" height="788" alt="image" src="https://github.com/user-attachments/assets/38b8de4e-bfa0-443f-9fc3-5fa4a2e33947" />

## 4. Configuración en Laravel

### 4.1 Instalar dependencias
Laravel usa el driver para conectarse a R2, por lo que necesitas:
```bash
composer require league/flysystem-aws-s3-v3 "^3.0"
```
### 4.2 Variables de entorno
Agrega en `.env`:
```env
CLOUDFLARE_R2_ACCESS_KEY_ID=★su_access_key★
CLOUDFLARE_R2_SECRET_ACCESS_KEY=★su_secret_key★
CLOUDFLARE_R2_BUCKET=test
CLOUDFLARE_R2_ENDPOINT=★su_endpoint★
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

### 5.1 Subida de archivos (Controlador)
**ProductoController**
```diff
use App\Models\Imagen;
use App\Models\Producto;
use Illuminate\Http\Request;
// ★↓↓↓Añade lo siguiente↓↓↓
use Illuminate\Support\Facades\Storage;
use Illuminate\Support\Str;
// ★↑↑↑Añade hasta aquí↑↑↑

class ProductoController extends Controller
{
...Omitido...
    public function store(Request $request)
    {
        try{
     ...Omitido...
            $producto->save(); //guardamos en productos
            //verificamos si la peticion trae imágenes
            if($request->hasFile('imagenes')){
// ★↓↓↓Añade lo siguiente↓↓↓
                // Obtener FILESYSTEM_DISK (local / r2)
                $disk = config('filesystems.default');
// ★↑↑↑Añade hasta aquí↑↑↑
                //recorremos la colección de imagenes para guardarlas en "imagenes"
                foreach($request->file('imagenes') as $img){
                    //creamos un nombre único de la imagen
                    $imageName = time() . '_' . $img->getClientOriginalName();
-                    //subimos el archivo de imagen a una carpeta publica del servidor
-                    $img->move(public_path('images/products/'),$imageName);
// ★↓↓↓Añade lo siguiente↓↓↓
                    // Subida según el disco
                    if ($disk === 'r2') {
                        // Subida a Cloudflare R2
                        Storage::disk('r2')->putFileAs('images/products', $img, $imageName);
                    } else {
                        // Subida local (public/images/products)
                        $img->move(public_path('images/products/'), $imageName);
                    }
// ★↑↑↑Añade hasta aquí↑↑↑
...Omitido...
```

### 5.2 Eliminación de archivos (Controlador)
**ProductoController**
```diff
class ProductoController extends Controller
{
...Omitido...
    public function destroy($id)
    {
        try{
    ...Omitido...
            //verficamos si no hay registros del producto en la tabla de detalle_ordenes
            $registro = DetalleOrden::where("producto_id",$producto->id)->first();
            if(!$registro){
// ★↓↓↓Añade lo siguiente↓↓↓
                // Obtener FILESYSTEM_DISK (local / r2)
                $disk = config('filesystems.default');
// ★↑↑↑Añade hasta aquí↑↑↑

                //podemos eliminar el producto, primero eliminamos las imagenes del servidor
                foreach($producto->imagenes as $image){
-                    $imagePath = public_path() .'/images/products/' . $image->nombre;
-                    unlink($imagePath);
// ★↓↓↓Añade lo siguiente↓↓↓
                    if ($disk === 'r2') {
                        // Eliminación de Cloudflare R2
                        $filePath = 'images/products/' . $image->nombre;
                        if (Storage::disk('r2')->exists($filePath)) {
                            Storage::disk('r2')->delete($filePath);
                        }
                    } else {
                        $imagePath = public_path() .'/images/products/' . $image->nombre;
                        unlink($imagePath);
                    }
// ★↑↑↑Añade hasta aquí↑↑↑
                }
                //eliminamos los registros de la tabla de imagenes
                $producto->imagenes()->delete();
...Omitido...
```

### 5.3 Accesor para obtener la URL de la imagen (Controlador / Modelo)
**ProductoController**
```diff
class ProductoController extends Controller
{
...Omitido...
    public function index()
    {
        try{
-            return response()->json(Producto::with('marca','categoria','imagenes')->get());
// ★↓↓↓Añade lo siguiente↓↓↓
            $productos = Producto::with('marca','categoria','imagenes')->get();

            // Incluir la url generada por el accessor en el JSON
            $productos->each(function($producto){
                $producto->imagenes->each(function($imagen){
                    $imagen->url = $imagen->url; // getUrlAttribute() es llamado
                });
            });

            return response()->json($productos);
// ★↑↑↑Añade hasta aquí↑↑↑
        }catch(\Exception $e){
            return response()->json(['error'=>$e->getMessage()],500);
        }
...Omitido...
```

**Imagen**
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Support\Facades\Storage; // ★←←←Añade esta línea.

class Imagen extends Model
{
    use HasFactory;
    protected $table = "imagenes";
    protected $fillable = ['nombre','producto_id'];
    //relacion con el modelo Imagen
    public function producto(){
        return $this->belongsTo(Producto::class);
    }

// ★↓↓↓Añade lo siguiente↓↓↓
    // Accesor que devuelve URL
    public function getUrlAttribute()
    {
        $filePath = 'images/products/' . $this->nombre;

        // Si es R2, devuelve la URL pública; si es local, devuelve la ruta de almacenamiento
        if (config('filesystems.default') === 'r2') {
            return Storage::disk('r2')->temporaryUrl($filePath, now()->addMinutes(60));
        } else {
            return asset($filePath);
        }
    }
// ★↑↑↑Añade hasta aquí↑↑↑
}
```


### 5.4 Frontend (Vue.js)

```vue
<!-- Catalogo.vue / Productos.vue -->
<SwiperSlide v-for="img in producto.imagenes" :key="img">
    <img :src="img.url" class="w-full h-40 object-contain" />
</SwiperSlide>
```

**Punto clave:** en el frontend siempre se usa `img.url`, lo que permite mostrar imágenes **tanto desde R2 como desde almacenamiento local con el mismo código**.
