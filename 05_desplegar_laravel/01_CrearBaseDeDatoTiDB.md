# Crear una base de datos en Internet utilizand TiDB

## Configuración inicial de la base de datos
Regístrate en TiDB.
https://tidbcloud.com/signup

Una vez registrado, haz clic en `Connect` en la pantalla de inicio.
> <img width="1328" height="631" alt="image" src="https://github.com/user-attachments/assets/85ee3ef2-0279-4b0f-8f5b-c016a468e118" />

Crear una contraseña.
> <img width="604" height="482" alt="image" src="https://github.com/user-attachments/assets/575075a7-7984-48bb-ad27-df995ddd5717" />

Anote la contraseña generada. Sólo se puede ver inmediatamente después de la generación.
> <img width="592" height="533" alt="image" src="https://github.com/user-attachments/assets/fef4191a-d87c-4b8e-81a9-5cf7da706a74" />

Haga clic en `CA cert` para descargar el certificado CA.
> <img width="604" height="477" alt="image" src="https://github.com/user-attachments/assets/c15bb1c6-2f5a-4eb0-8b66-de27d2036a55" />

**El archivo de certificado CA descargado debe moverse al directorio raíz del proyecto de ejemplo.**

Abra el directorio raíz del proyecto de ejemplo en el terminal de Ubuntu.
```
cd /mnt/<su directorio raíz del proyecto de ejemplo>
```
> [!NOTE]
> En WSL2, se puede acceder al directorio de Windows a través de `/mnt`.

Importe la copia de seguridad de la base de datos con el siguiente comando.
```
mysql --comments --connect-timeout 150 -u '<your_username>' -h <your_cluster_host> -P 4000 -D test --ssl-mode=VERIFY_IDENTITY --ssl-ca=<your_ca_path> -p<your_password> < product_data.sql
```
> Ejemplo:
> ```
> mysql --comments --connect-timeout 150 -u '2puvRTdtCXSZp6Z.root' -h gateway01.us-east-1.prod.aws.tidbcloud.com -P 4000 -D test --ssl-mode=VERIFY_IDENTITY --ssl-ca=isrgrootx1.pem -pss8rqUIX4nUONld7 < ordersdb.sql
> ```

## Actualiza la información de la base de dato en `.env`
Cambia la información del fichero `.env` por la del TiDB.

> <img width="758" height="690" alt="image" src="https://github.com/user-attachments/assets/a07c3b1e-7c61-454f-8c8e-e3945aab33dc" />

## Habilitar SSL para conexiones de base de datos

El contenido del certificado CA es demasiado largo y difícil de manejar, por lo que se codifica en Base64.

En el terminal de Ubuntu, abra el directorio raíz del proyecto y ejecute el siguiente comando.
```bash
base64 -w 0 isrgrootx1.pem > ca.pem.b64
```

Añade la variable de entorno `MYSQL_ATTR_SSL_CA_B64` al archivo `.env`.
```
MYSQL_ATTR_SSL_CA_B64=<Contenido de ca.pem.b64.>
```
> <img width="1132" height="70" alt="image" src="https://github.com/user-attachments/assets/b0227a87-9bf7-4721-a13c-f8bfc4a0d93a" />

En `database.php`, sustituye el contenido de `connections`>`mysql`>`options` por el siguiente código.
```php
<?php
...Omitido...
    'connections' => [
    ...Omitido...
        'mysql' => [
        ...Omitido...
            'options' => extension_loaded('pdo_mysql') ? array_filter([
                PDO::MYSQL_ATTR_SSL_CA => (function () {
                    $b64 = env('MYSQL_ATTR_SSL_CA_B64');
                    if (!$b64) return null;

                    // descodificar
                    $caContent = base64_decode($b64);

                    // Guardar en archivo temporal
                    $tmpFile = sys_get_temp_dir() . '/ca_' . uniqid() . '.pem';
                    file_put_contents($tmpFile, $caContent);

                    return $tmpFile;
                })(),
            ]) : [],
```
