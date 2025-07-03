# Fundamentos de Docker (Parte Conceptual)

## Objetivo del aprendizaje

Al finalizar este capítulo, podrás:

* Comprender qué es Docker y por qué se utiliza
* Diferenciar Docker de una máquina virtual tradicional
* Entender los componentes clave: imagen, contenedor, Dockerfile y registro

## 1. ¿Por qué es necesario Docker?

### ⚠️ Problemas comunes en el desarrollo

| Problema                              | Descripción                                                                  |
| ------------------------------------- | ---------------------------------------------------------------------------- |
| "¡Funcionaba en mi máquina!"          | Diferencias en versiones de librerías, sistemas operativos o configuraciones |
| Entornos inconsistentes entre equipos | Cada desarrollador instala las cosas a su manera                             |
| Pérdida de tiempo en configuración    | Se repiten tareas de instalación y ajustes cada vez                          |

✅ **Docker soluciona estos problemas creando entornos portables, repetibles y automatizables.**

## 2. ¿Qué es Docker?

### 🐳 Definición

> Docker es una plataforma de virtualización ligera que permite **empaquetar una aplicación con todas sus dependencias** para que funcione **igual en cualquier entorno**.


## 3. Conceptos clave

| Término        | Definición                                                          |
| -------------- | ------------------------------------------------------------------- |
| **Imagen**     | Plantilla (snapshot) con todo lo necesario para ejecutar una app    |
| **Contenedor** | Instancia en ejecución de una imagen (es decir, un proceso aislado) |
| **Dockerfile** | Archivo de instrucciones para construir una imagen personalizada    |
| **Registro**   | Repositorio donde se almacenan imágenes (por ejemplo: Docker Hub)   |

> ![image](https://github.com/user-attachments/assets/caf06c80-351d-4e44-8029-3390beec98f6)

## 4. ¿Cómo funciona Docker?

```
+------------------------+
| Aplicación             |
+------------------------+
| Librerías/Dependencias |
+------------------------+
| Sistema base (Linux)   |
+------------------------+
| Contenedor de Docker   |
```

🔎 El contenedor es **una "caja cerrada" que contiene todo lo necesario** para que la aplicación funcione.

## 5. Docker vs Máquina Virtual

| Elemento           | Máquina Virtual                   | Docker                          |
| ------------------ | --------------------------------- | ------------------------------- |
| Tiempo de arranque | Lento (decenas de segundos o más) | Rápido (en segundos)            |
| Sistema operativo  | Requiere SO completo (Guest OS)   | Usa el SO del host (más ligero) |
| Uso de recursos    | Alto (RAM, disco)                 | Bajo                            |
| Portabilidad       | Limitada                          | Muy alta                        |

📌 Docker es **más liviano, rápido y portable** que una máquina virtual tradicional.

![image](https://github.com/user-attachments/assets/fc73b853-1b2e-496f-a8f3-235b0e55a615)


## 6. Beneficios de usar Docker

| Área           | Ventaja                                                             |
| -------------- | ------------------------------------------------------------------- |
| Desarrollo     | Configuración rápida y estandarizada entre todos los miembros       |
| Pruebas        | Permite probar en múltiples entornos fácilmente                     |
| Producción     | Misma imagen que en desarrollo, sin "sorpresas"                     |

## 7. Casos de uso comunes

* Desarrollo de aplicaciones web (Laravel, Node.js, etc.)
* Entornos de base de datos (MySQL, PostgreSQL, MongoDB)
* Reproducción de entornos de producción en local
* Integración con GitHub Actions y despliegue automático

## 8. Errores comunes al comenzar

| Malentendido                                    | Aclaración                                                                     |
| ----------------------------------------------- | ------------------------------------------------------------------------------ |
| “Docker es como una máquina virtual”            | No exactamente; es **virtualización a nivel de proceso**, no de hardware       |
| “Docker contiene un sistema operativo completo” | No; comparte el kernel con el sistema host                                     |

## 9. Resumen del capítulo

* Docker permite **empaquetar y ejecutar aplicaciones de forma estandarizada**
* Es **rápido, liviano y reproducible**, ideal para entornos modernos de desarrollo y despliegue
* Comprender los conceptos de imagen, contenedor, Dockerfile y registro es clave
