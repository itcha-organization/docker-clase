# Publicar puerto del contenedor

<img width="670" height="246" alt="image" src="https://github.com/user-attachments/assets/d6896c09-b926-4760-a211-79bfdc246793" />

## 1. Objetivo
Aprender a usar la opción `--publish` (`-p`) en `docker container run` para mapear puertos entre el host y el contenedor, permitiendo acceder a los servicios que corren dentro del contenedor desde el exterior.

## 2. ¿Qué es la opción `--publish`?
* Sintaxis:
  ```bash
  --publish <puerto_host>:<puerto_contenedor>
  ```

* Función:
  * Mapea un puerto del host (lado izquierdo) a un puerto del contenedor (lado derecho).
  * Por ejemplo, mapeando el puerto 8080 del host al puerto 80 del contenedor, podrás acceder a la aplicación dentro del contenedor a través de `localhost:8080` en tu navegador.

## 3. Ejemplo práctico
### 3-1. Ejecutar un contenedor Nginx y publicar puertos
```bash
docker container run --rm --publish 8080:80 nginx
```
* `--publish 8080:80`: conecta el puerto 8080 del host con el puerto 80 del contenedor.

### 3-2. Comprobación
* Accede en tu navegador a `http://localhost:8080` y verás la página de bienvenida por defecto de Nginx.

## 4. Puntos importantes
* Si el puerto del host ya está en uso, el contenedor no podrá iniciarse y mostrará un error.
* Los puertos deben estar dentro del rango 0 a 65535.
* Puedes publicar varios puertos agregando varias opciones `--publish`.

Ejemplo:
```bash
docker container run --rm -d -p 8080:80 -p 8443:443 nginx
```

## 5. Ejercicios prácticos

1. Ejecuta un contenedor Nginx y publica el puerto 8081 del host al 80 del contenedor. Verifica que funcione desde el navegador.
2. Ejecuta un contenedor php con `bash` y publica el puerto 9999 del host al puerto 8000 del contenedor. Verifica que no haya pagina todavia desde el navegador.
3. En el contenedor php, inicia un servidor Web con `php -S 0.0.0.0:8000` y publica el puerto 8000 del host.  Verifica que haya pagina desde el navegador.
4. ¿Qué sucede si intentas mapear un puerto que ya está siendo usado en el host?

---

## 6. Respuestas

1.
```bash
docker container run --rm -p 8081:80 nginx
```

2.
```bash
docker container run --rm -it -p 9999:8000 php bash
```

3.
```bash
# Dentro del contenedor:
php -S 0.0.0.0:8000
```

4. El contenedor no se iniciará y mostrará un error por conflicto de puertos en el host.
