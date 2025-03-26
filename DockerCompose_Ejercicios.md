# Docker Compose - De Básico a Complejo

Este repositorio contiene tres ejercicios progresivos usando **Docker Compose**, empezando con un contenedor simple y terminando con un stack completo con Nginx, Node.js y MySQL.

---

## Ejercicio 1: Básico - Nginx

### Objetivo
Levantar un servidor web básico con Nginx.

### Archivos
```yaml
# docker-compose.yml
version: '3'
services:
  web:
    image: nginx:alpine
    ports:
      - "8080:80"
```

### Uso
```bash
docker-compose up
```
Abre `http://localhost:8080`

---

## Ejercicio 2: Intermedio - Flask + PostgreSQL

### Objetivo
Correr una app Flask conectada a una base de datos PostgreSQL.

### Estructura
```
/flask-postgres/
├── app/
│   └── app.py
│   └── requirements.txt
├── Dockerfile
├── docker-compose.yml
```

### Archivos

#### app/app.py
```python
from flask import Flask
import psycopg2

app = Flask(__name__)

@app.route('/')
def index():
    return "¡Hola desde Flask + PostgreSQL!"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

#### app/requirements.txt
```
flask
psycopg2-binary
```

#### Dockerfile
```Dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY app/ .
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```

#### docker-compose.yml
```yaml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "5000:5000"
    depends_on:
      - db

  db:
    image: postgres:13
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: example
```

### Uso
```bash
docker-compose up --build
```
Abre `http://localhost:5000`

---

## Ejercicio 3: Complejo - Nginx + Node.js + MySQL

### Objetivo
Construir un stack completo con backend Node.js, base de datos MySQL y Nginx como proxy inverso.

### Estructura
```
/fullstack-nginx-node-mysql/
├── backend/
│   ├── app.js
│   ├── package.json
├── nginx/
│   └── default.conf
├── Dockerfile
├── docker-compose.yml
```

### Archivos

#### backend/app.js
```js
const express = require('express');
const mysql = require('mysql2');

const app = express();
const port = 3000;

const db = mysql.createConnection({
  host: 'db',
  user: 'root',
  password: 'rootpass',
  database: 'testdb'
});

db.connect(err => {
  if (err) {
    console.error('DB connection error:', err);
  } else {
    console.log('Connected to MySQL DB!');
  }
});

app.get('/', (req, res) => {
  res.send('¡Hola desde Node.js + MySQL!');
});

app.listen(port, () => {
  console.log(`Servidor escuchando en puerto ${port}`);
});
```

#### backend/package.json
```json
{
  "name": "node-mysql-app",
  "version": "1.0.0",
  "main": "app.js",
  "dependencies": {
    "express": "^4.17.1",
    "mysql2": "^3.1.0"
  }
}
```

#### nginx/default.conf
```nginx
server {
    listen 80;
    
    location / {
        proxy_pass http://web:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

#### Dockerfile
```Dockerfile
FROM node:18
WORKDIR /app
COPY backend/ .
RUN npm install
CMD ["node", "app.js"]
```

#### docker-compose.yml
```yaml
version: '3.8'

services:
  web:
    build: .
    depends_on:
      - db
    networks:
      - app-net

  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: rootpass
      MYSQL_DATABASE: testdb
    ports:
      - "3306:3306"
    networks:
      - app-net

  nginx:
    image: nginx:alpine
    ports:
      - "8080:80"
    volumes:
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
    depends_on:
      - web
    networks:
      - app-net

networks:
  app-net:
```

### Uso
```bash
docker-compose up --build
```
Visita: [http://localhost:8080](http://localhost:8080)

---

¡Y listo! Con estos tres ejercicios tienes una base sólida para desarrollar y orquestar aplicaciones con Docker Compose.
