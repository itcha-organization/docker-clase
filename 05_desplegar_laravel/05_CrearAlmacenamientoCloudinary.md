# Configuración de Cloudinary como almacenamiento en Internet

## 1. Introducción

Al desplegar una aplicación Laravel en un entorno de producción, **guardar archivos directamente dentro de un contenedor Docker puede causar problemas**.
Los contenedores son entornos temporales: cada vez que se reinician o se redeploya la aplicación, los archivos almacenados internamente desaparecen. Esto significa que las imágenes subidas por los usuarios no se conservarán de manera segura.

Para resolver este problema, se necesita un **almacenamiento persistente separado de la aplicación**.

En este material, utilizaremos **Cloudinary** como almacenamiento de objetos en la nube.
Cloudinary ofrece 25 GB de almacenamiento gratuito y cuenta con paquetes para Laravel, lo que facilita la integración.
> <img width="668" height="419" alt="image" src="https://github.com/user-attachments/assets/5b4338e3-c323-4ebb-bd6d-c03116742dff" />


## 2. Confirmación de `CLOUDINARY_URL` en Cloudinary

1. Inicia sesión en [Cloudinary Dashboard](https://cloudinary.com/users/login).
2. Haga clic en el icono de la esquina superior izquierda.
   > <img width="1323" height="710" alt="image" src="https://github.com/user-attachments/assets/78241c34-2716-43b5-bacf-701be2aeca09" />
3. Haga clic en **Get Started**.
   > <img width="1322" height="620" alt="image" src="https://github.com/user-attachments/assets/a05669d8-9901-49a3-a339-4d7ebf5867c9" />
4. Haga clic en **View API Keys**.
   > <img width="1320" height="625" alt="image" src="https://github.com/user-attachments/assets/1c224fa1-ba68-4b91-9bf0-a5a90029640a" />
5. Haga clic en el icono del ojo.
   > <img width="516" height="255" alt="image" src="https://github.com/user-attachments/assets/2c3d28b9-2577-4e03-a66d-69e58d74178a" />
6. Introduzca el código que recibirás en su correo electrónico y haga clic en **Approve**.
   > <img width="494" height="234" alt="image" src="https://github.com/user-attachments/assets/1d7215d8-fce4-4289-96cd-a42021b5cc00" />
7. Copie la `CLOUDINARY_URL` mostrada y péguela en `.env`.
   > <img width="590" height="255" alt="image" src="https://github.com/user-attachments/assets/6b1949a4-8c3e-4704-a5b1-25536c395151" />

## 3. Configuración en Laravel

### 3.1 Instalar SDK
Laravel usa el driver para conectarse a Cloudinary, por lo que necesitas:
```bash
composer require cloudinary-labs/cloudinary-laravel
```
Una vez instalado el SDK, puede invocar el comando install para configurarlo todo:
```bash
php artisan cloudinary:install
```
Este comando:
- Crea `config/cloudinary.php`.
- Instale los paquetes npm pertinentes

### 3.2 Variables de entorno
Agrega en `.env`:
```env
CLOUDINARY_URL=★La URL que acaba de copiar★
```
### 3.3 Configuración en `config/filesystems.php`
En el arreglo `disks`, agrega (o ajusta) la configuración de `cloudinary`:
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
        'cloudinary' => [
            'driver' => 'cloudinary',
            'key' => env('CLOUDINARY_KEY'),
            'secret' => env('CLOUDINARY_SECRET'),
            'cloud' => env('CLOUDINARY_CLOUD_NAME'),
            'url' => env('CLOUDINARY_URL'),
            'secure' => (bool) env('CLOUDINARY_SECURE', true),
            'prefix' => env('CLOUDINARY_PREFIX'),
        ],
        // ★Añade hasta aquí
    ],
```
Con esto, Laravel ya podrá almacenar archivos en Cloudinary.

## 4. Modificación del código

### 4.1 Subida de archivos (Controlador)
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
            if($request->hasFile('imagenes')){
        ...Omitido...
                foreach($request->file('imagenes') as $img){
                    //creamos un nombre único de la imagen
                    $imageName = time() . '_' . $img->getClientOriginalName();
-                    //subimos el archivo de imagen a una carpeta publica del servidor
-                    $img->move(public_path('images/products/'),$imageName);
// ★↓↓↓Añade lo siguiente↓↓↓
                    // Subida según FILESYSTEM_DISK (local / cloudinary)
                    if (config('filesystems.default') === 'cloudinary') {
                        // Subida a Cloudinary
                        Storage::disk('cloudinary')->putFileAs('images/products/', $img, $imageName);
                    } else {
                        // Subida local (public/images/products)
                        $img->move(public_path('images/products/'), $imageName);
                    }
// ★↑↑↑Añade hasta aquí↑↑↑
...Omitido...
```

### 4.2 Eliminación de archivos (Controlador)
**ProductoController.php**
```diff
class ProductoController extends Controller
{
...Omitido...
    public function destroy($id)
    {
        try{
    ...Omitido...
            if(!$registro){
                //podemos eliminar el producto, primero eliminamos las imagenes del servidor
                foreach($producto->imagenes as $image){
-                    $imagePath = public_path() .'/images/products/' . $image->nombre;
-                    unlink($imagePath);
// ★↓↓↓Añade lo siguiente↓↓↓
                    // Subida según FILESYSTEM_DISK (local / cloudinary)
                    if (config('filesystems.default') === 'cloudinary') {
                        // Eliminación de Cloudinary
                        $filePath = 'images/products/' . $image->nombre;
                        Storage::disk('cloudinary')->delete($filePath);
                    } else {
                        // Eliminar de almacenamiento local
                        $imagePath = public_path() .'/images/products/' . $image->nombre;
                        unlink($imagePath);
                    }
// ★↑↑↑Añade hasta aquí↑↑↑
                }
                //eliminamos los registros de la tabla de imagenes
                $producto->imagenes()->delete();
...Omitido...
```

### 4.3 Accesor para obtener la URL de la imagen (Modelo)
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
    // Accesor para la URL de la imagen
    protected $appends = ['url'];

    // Accesor que devuelve URL
    public function getUrlAttribute()
    {
        $filePath = 'images/products/' . $this->nombre;
        // Si es Cloudinary, devuelve la URL pública; si es local, devuelve la ruta de almacenamiento
        if (config('filesystems.default') === 'cloudinary') {
            try {
                return Storage::disk('cloudinary')->url($filePath);
            } catch (\Cloudinary\Api\Exception\NotFound $e) {
                return asset($filePath);
            }
        }
        return asset($filePath);
    }
// ★↑↑↑Añade hasta aquí↑↑↑
}
```

### 4.4 Frontend (Vue.js)
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

## 5. Crear una nueva versión de la imagen
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

## 6. Modificación las variables de entorno para el entorno de producción
Modifique los valores de las variables de entorno para el entorno de producción como se indica a continuación.

- FILESYSTEM_DISK: `cloudinary`
- CLOUDINARY_URL=★La URL que acaba de copiar★
> <img width="520" height="169" alt="image" src="https://github.com/user-attachments/assets/8656e54d-3fae-4c9f-89d3-65830d7b78d0" />

Una vez realizadas las modificaciones, haga clic en `Save and deploy`.
> <img width="596" height="642" alt="image" src="https://github.com/user-attachments/assets/05e2bdb0-b978-4f03-86f0-18722c9cd5af" />
