# Configuración de Cloudflare R2 como almacenamiento en Internet

## 1. Introducción

Al desplegar una aplicación Laravel en un entorno de producción, **guardar archivos directamente dentro de un contenedor Docker puede causar problemas**.
Los contenedores son entornos temporales: cada vez que se reinician o se redeploya la aplicación, los archivos almacenados internamente desaparecen. Esto significa que las imágenes subidas por los usuarios no se conservarán de manera segura.

Para resolver este problema, se necesita un **almacenamiento persistente separado de la aplicación**.

En este material, utilizaremos **Cloudflare R2** como almacenamiento de objetos en la nube.
R2 ofrece 10 GB de almacenamiento gratuito y cuenta con paquetes para Laravel, lo que facilita la integración.
> <img width="670" height="414" alt="image" src="https://github.com/user-attachments/assets/9c221b8c-c347-4c66-8287-5abbd4e077de" />

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
**ProductoController.php**
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
**ProductoController.php**
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
**ProductoController.php**
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

**Imagen.php**
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
**Productos.vue**
```diff
...Omitido...
<!-- Modal para mostrar las imágenes de cada producto -->
<Dialog v-model:visible="showImageDialog" 
header="Imágenes del Producto" :style="{width:'550px'}" class="p-fluid">
   <Swiper :modules="[Navigation]" navigation class="h-40">
       <SwiperSlide v-for="img in producto.imagenes" :key="img">
-          <img :src="`images/products/${img.nombre}`" class="w-full h-40 object-contain" />
           <img :src="img.url" class="w-full h-40 object-contain" /> <!-- ★Añade esta línea -->
       </SwiperSlide>
   </Swiper>
</Dialog>
...Omitido...
```

**Catalogo.vue**
```diff
...Omitido...
<div class="grid grid-cols-1 md:grid-cols-3 gap-6">
  <div v-for="producto in filteredProducts" :key="producto.id" class="bg-white rounded-lg shadow-lg overflow-hidden p-4">
    <Swiper :modules="[Navigation]" navigation class="h-40">
      <SwiperSlide v-for="img in producto.imagenes" :key="img">
-       <img :src="`images/products/${img.nombre}`" class="w-full h-40 object-contain" />
        <img :src="img.url" class="w-full h-40 object-contain" /> <!-- ★Añade esta línea -->
      </SwiperSlide>
    </Swiper>
...Omitido...
```

## 6. Crear una nueva versión de la imagen
Ejecute el siguiente comando e introduzca la contraseña en el prompt que aparece. (Omita este paso si ya ha iniciado sesión.)
```
docker login -u <nombre_de_usuario>
```

Después de modificar el código, construye la imagen con la etiqueta `v1.3`.
```
docker image build -t laravel-app:v1.3 .
```

Etiquetar la imagen local para asociarla con el repositorio remoto.
```
docker tag <nombre_imagen_local>:<etiqueta> <nombre_usuario>/<nombre_repositorio>:<etiqueta>
```

Subir la imagen al repositorio remoto
```
docker push <nombre_usuario>/<nombre_repositorio>:<etiqueta>
```

Una vez finalizado el envío de imágenes, actualice la etiqueta de imagen `Setting`>`Source` en Koyeb.
> <img width="870" height="357" alt="image" src="https://github.com/user-attachments/assets/ad2ae829-4f36-421a-9a27-72d26157b09d" />

## 7. Modificación las variables de entorno para el entorno de producción
Modifique los valores de las variables de entorno para el entorno de producción como se indica a continuación.

- FILESYSTEM_DISK: `r2`
- CLOUDFLARE_R2_BUCKET=test
- CLOUDFLARE_R2_ACCESS_KEY_ID=★su_access_key★
- CLOUDFLARE_R2_SECRET_ACCESS_KEY=★su_secret_key★
- CLOUDFLARE_R2_ENDPOINT=★su_endpoint★
> <img width="704" height="693" alt="image" src="https://github.com/user-attachments/assets/eba4c001-2f16-4d20-9886-054b0d2f429e" />

Una vez realizadas las modificaciones, haga clic en `Save and deploy`.
> <img width="596" height="642" alt="image" src="https://github.com/user-attachments/assets/05e2bdb0-b978-4f03-86f0-18722c9cd5af" />

## ⚠️ Importante:　Establecer alertas de tasas de Cloudflare
Hemos registrado una tarjeta de crédito para utilizar Cloudflare R2.
Si supera el límite gratuito de 10 GB, se le cobrará.

**Para evitar que se le cobre, configure la notificación para que le avise cuando alcance el 80 % del límite gratuito de 10 GB.**

1. Inicia sesión en [Cloudflare Dashboard](https://dash.cloudflare.com).

2. Haga clic en **Administrar cuenta**.
> <img width="1035" height="432" alt="image" src="https://github.com/user-attachments/assets/63970fab-a3da-458f-ad9a-306bd475d71e" />

3. Abra el panel de **facturación** y haga clic en **notificacion de utilizacion facturable**.
> <img width="1050" height="499" alt="image" src="https://github.com/user-attachments/assets/fc766375-d328-455e-9d5d-d9d16b2307ed" />

4. Haga clic en el botón **Agregar**.
> <img width="1057" height="204" alt="image" src="https://github.com/user-attachments/assets/35942a25-fb3f-40c0-b57e-4f50f06dfbf1" />

5. Seleccione las opciones de **facturación**.
> <img width="1046" height="337" alt="image" src="https://github.com/user-attachments/assets/a2915899-c500-4a68-a100-1af9ca8d5645" />

6. Configure los elementos como se indica a continuación y haga clic en **Guardar**.
   - Nombre de la notificación: `Notificación cuando se alcanza el 80 % del límite gratuito de 10 GB`
   - Producto: `R2 Storage`
   - Notificar cuando el total supere bytes of storage: `8000000000`
   - Correo electrónico de notificación: su correo
     > <img width="1045" height="484" alt="image" src="https://github.com/user-attachments/assets/a04549f1-7fb0-4754-a43e-d194c26bf516" />
