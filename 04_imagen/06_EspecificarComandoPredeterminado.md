# Especificar el comando predeterminado con  la instrucción CMD

## 1. ¿Qué es la instrucción CMD?

* Es una instrucción en Dockerfile para **especificar el comando predeterminado que se ejecuta al iniciar el contenedor**.
* Si no se indica un comando diferente al ejecutar `docker run`, se ejecutará el comando definido en CMD.
* CMD funciona como el "punto de entrada" para la ejecución del contenedor.

## 2. Sintaxis básica de CMD
```dockerfile
CMD ["comando", "subcomando1/param1", "subcomando2/param2"....]
```

**Ejemplo**
```dockerfile
CMD ["node", "app.js"]
```
---

## 3. Tutorial para crear una imagen que inicie un servidor Node.js automáticamente

---

### 3-1. Archivo simple de servidor Node.js (`app.js`)

```javascript
const http = require("http");
const port = 3000;

const server = http.createServer((req, res) => {
  res.end("¡Hola desde Node.js en Docker!");
});

server.listen(port, () => {
  console.log(`Servidor escuchando en http://localhost:${port}`);
});
```
---

### 3-2. Archivo simple de `package.json`

```
{
  "name": "mi-app",
  "version": "1.0.0",
  "description": "Ejemplo simple de app Node.js",
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {}
}
```

---

### 3-3. Ejemplo de Dockerfile

```dockerfile
FROM node:18-alpine

WORKDIR /usr/src/app

COPY package*.json ./
RUN npm install

COPY app.js .

EXPOSE 3000

CMD ["node", "app.js"]
```

---

### 3-4. Construcción de la imagen

```bash
docker build -t node-server .
```

---

### 3-5. Ejecución del contenedor

```bash
docker run -p 3000:3000 node-server
```

* Abre tu navegador y visita `http://localhost:3000` para ver el mensaje `"¡Hola desde Node.js en Docker!"`.

---

## 4. Resumen

| Tema                 | Descripción                                                                   |
| -------------------- | ----------------------------------------------------------------------------- |
| CMD                  | Comando predeterminado que se ejecuta al iniciar el contenedor                |
| Formato exec         | CMD se escribe como un arreglo: `["node", "app.js"]`                          |
| Servidor Node.js     | El archivo `app.js` crea un servidor HTTP que se inicia con CMD               |
| Exposición de puerto | Se usa `EXPOSE 3000` y `docker run -p 3000:3000` para permitir acceso externo |
