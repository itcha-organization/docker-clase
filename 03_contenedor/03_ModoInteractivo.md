# Modo interactivo en contenedor

<img width="660" height="177" alt="image" src="https://github.com/user-attachments/assets/46f4a212-c10a-4b2d-b36c-e662e5ba343d" />

## Objetivo del capítulo
Aprender cómo usar `docker container run --interactive --tty` (`-it`) para trabajar de manera interactiva con un contenedor que utiliza la imagen oficial de PHP.

## ¿Qué hacen `--interactive` y `--tty`?
| Opción                 | Descripción                                                                         |
| ---------------------- | ----------------------------------------------------------------------------------- |
| `--interactive` o `-i` | Mantiene la entrada estándar (stdin) abierta para permitir interacción manual.      |
| `--tty` o `-t`         | Asigna una terminal virtual para mostrar la salida de forma legible (modo consola). |
> Combinadas como `-it`, estas opciones permiten trabajar con el contenedor como si fuera un entorno interactivo real.

**Esta opción es útil en situaciones como las siguientes:**
* Cuando quieres usar `bash` dentro del contenedor para investigar la estructura de archivos o el estado de funcionamiento.
* Cuando deseas probar un poco de código para fines de prueba o verificación.

## Ejemplo 1: Iniciar un contenedor Ubuntu en modo interactivo
```bash
docker container run --rm --interactive --tty ubuntu bash
```
Al ejecutar este comando:

* Se descarga y ejecuta la imagen `ubuntu` (si aún no está en tu máquina).
* Se abre el modo interactivo con el comando `bash` y se aparece el prompt `root@... :/#`.
* Puedes escribir y ejecutar comandos Linux línea por línea.

**⌨ Ejemplo de uso:**
> ```bash
> docker container run --rm --interactive --tty ubuntu bash
> ```
> Resultado:
> ```
> Interactive mode enabled
> root@c0e780abcf6e:/#
> ```
>
> Ahora puedes escribir:
> ```bash
> whoami
> ```
> Para salir del modo interactivo: Presiona `Ctrl + D`, o escriba `exit`

## Ejemplo 2: Iniciar un contenedor PHP en modo interactivo
```bash
docker container run --rm --interactive --tty php php -a
```
Al ejecutar este comando:

* Se descarga y ejecuta la imagen `php` (si aún no está en tu máquina).
* Se abre el modo interactivo con el comando `php -a` y se aparece el prompt `php >`.
* Puedes escribir y ejecutar comandos PHP línea por línea.

**⌨ Ejemplo de uso:**
> ```bash
> docker container run --rm -it php php -a
> ```
> Resultado:
> ```
> Interactive mode enabled
> php >
> ```
>
> Ahora puedes escribir:
> ```php
> echo "Hola desde el contenedor\n";
> ```
> Para salir del modo interactivo: Presiona `Ctrl + D`


## Ejemplo 3: Iniciar un contenedor Node.js en modo interactivo

```bash
docker container run --rm --interactive --tty node node
```

Al ejecutar este comando:

* Se descarga y ejecuta la imagen `node` (si aún no está en tu máquina).
* Se abre el entorno REPL de Node.js (Read-Eval-Print Loop).
* Puedes escribir y ejecutar código JavaScript línea por línea.

**⌨ Ejemplo de uso:**
> ```bash
> docker container run --rm -it node node
> ```
> Resultado:
> ```
> >
> ```
> Ahora puedes escribir:
> ```javascript
> console.log(2 + 3)
> ```
>
> Para salir del modo interactivo: Presiona `Ctrl + D`, o escribe `.exit`

## ✅ Preguntas prácticas

1. Inicia un contenedor Ubuntu en modo interactivo y ejecuta el comando `ls`.
2. ¿Cómo puedes verificar el directorio de trabajo actual dentro del contenedor?
3. ¿Qué muestra el comando `whoami` dentro del contenedor?
4. ¿Qué sucede si omites la opción `-it`?

## ✅ Respuestas

1.

```bash
docker container run -it --rm ubuntu bash
# Dentro del contenedor:
ls
```

2.

```bash
pwd
```

Salida esperada:

```
/
```
3. El comando `whoami` muestra el usuario actual. En la mayoría de los casos dentro de un contenedor Ubuntu, serás el usuario:
```
root
```

4. Si omites `-it`, el contenedor no abrirá una terminal interactiva. No podrás escribir comandos manualmente y el contenedor puede cerrarse inmediatamente.
