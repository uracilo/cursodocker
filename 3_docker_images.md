# **Guía Paso a Paso: Dockerfile desde Básico hasta Avanzado**

Este documento explica paso a paso cómo construir y optimizar un **Dockerfile**, desde lo básico hasta configuraciones avanzadas.

---

## **1. Introducción**
Docker permite empaquetar aplicaciones y sus dependencias en contenedores. Para ello, usamos un **Dockerfile**, que es un archivo de texto con instrucciones para construir la imagen del contenedor.

---

## **2. Primer Dockerfile: Imagen Base y Mensaje**

### **Dockerfile**
```dockerfile
# Usa una imagen base mínima
FROM alpine:latest

# Ejecuta un mensaje por defecto
CMD ["echo", "¡Hola, mundo desde Docker!"]
```

### **Pasos:**
1. Construir la imagen:
   ```sh
   docker build -t hola-mundo .
   ```
2. Ejecutar el contenedor:
   ```sh
   docker run --rm hola-mundo
   ```

---

## **3. Agregar Python y Ejecutar un Script**

### **Dockerfile**
```dockerfile
FROM python:3.10
WORKDIR /app
COPY script.py .
CMD ["python", "script.py"]
```

### **Pasos:**
1. Crear un archivo `script.py` con:
   ```python
   print("¡Docker ejecutó este script en Python!")
   ```
2. Construir y ejecutar la imagen.

---

## **4. Servidor Web con Flask**

### **Dockerfile**
```dockerfile
FROM python:3.10
WORKDIR /app
COPY requirements.txt .
COPY script.py .
RUN pip install --no-cache-dir -r requirements.txt
EXPOSE 5000
CMD ["python", "script.py"]
```

### **Pasos:**
1. Crear `requirements.txt` con:
   ```
   flask
   ```
2. Crear `script.py` con Flask.
3. Construir y ejecutar con `docker run -p 5000:5000 imagen`.

---

## **5. Diferencia entre COPY y ADD**

| Comando  | Uso principal  | Extrae `.tar` | Descarga archivos remotos |
|----------|--------------|---------------|---------------------|
| `COPY`   | Copia archivos del host al contenedor  | ❌ No  | ❌ No |
| `ADD`    | Copia archivos y permite extras | ✅ Sí | ✅ Sí (no recomendado) |

```dockerfile
COPY requirements.txt .
# Alternativa:
ADD archivo.tar.gz /app/descomprimido/
```

---

## **6. Agregar Variables de Entorno**
```dockerfile
ENV MENSAJE="¡Hola desde un contenedor!"
CMD ["python", "script.py"]
```
```python
import os
print(os.getenv("MENSAJE"))
```

Ejecutar con:
```sh
docker run --rm -e MENSAJE="Nuevo mensaje" imagen
```

---

## **7. Seguridad: Usuario sin Privilegios**
```dockerfile
RUN useradd -m myuser
USER myuser
```

---

## **8. Construcción Multinivel (Multi-Stage Build)**
```dockerfile
FROM python:3.10 AS builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY script.py .

FROM python:3.10-slim AS final
WORKDIR /app
COPY --from=builder /app /app
CMD ["python", "script.py"]
```

---

## **9. Agregar Healthcheck**
```dockerfile
HEALTHCHECK --interval=10s --timeout=3s --retries=3 \  
  CMD curl -f http://localhost:5000 || exit 1
```

---

## **10. Dockerfile Completo con Todas las Palabras Reservadas**
```dockerfile
FROM python:3.10 AS builder
LABEL maintainer="Tu Nombre <tu@email.com>"
LABEL description="Imagen de ejemplo con todas las palabras reservadas de Dockerfile"
LABEL version="1.0"
WORKDIR /app
COPY requirements.txt .
COPY script.py .
RUN pip install --no-cache-dir -r requirements.txt
ENV MENSAJE="¡Hola desde un contenedor!"
RUN useradd -m myuser
USER myuser
EXPOSE 5000
VOLUME ["/app/data"]
ENTRYPOINT ["python", "script.py"]
CMD []
HEALTHCHECK --interval=10s --timeout=3s --retries=3 \  
  CMD curl -f http://localhost:5000 || exit 1
```

---
