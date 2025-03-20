
# Dockerfile para Aplicación Segura

Este documento detalla los pasos y el código necesario para construir una imagen Docker para una aplicación utilizando buenas prácticas de seguridad y optimización.

## Paso 1: Elegir una Imagen Base

Se utiliza Alpine por ser una imagen ligera y segura.

```dockerfile
# Definir una imagen base ligera
FROM alpine:latest
```

## Paso 2: Configurar el Entorno y Herramientas Necesarias

Configuración del directorio de trabajo y instalación de herramientas esenciales como Python y pip.

```dockerfile
# Establecer un directorio de trabajo seguro
WORKDIR /app

# Instalar dependencias necesarias
RUN apk add --no-cache python3 py3-pip
```

## Paso 3: Copiar Archivos de la Aplicación

Copiar los archivos necesarios al contenedor, asegurándose de usar `.dockerignore` para evitar incluir archivos innecesarios.

```dockerfile
# Copiar archivos al contenedor
COPY app/ /app/
```

## Paso 4: Establecer Usuario No Root

Utilizar un usuario sin privilegios para ejecutar el contenedor, mejorando así la seguridad del mismo.

```dockerfile
# Crear un usuario sin privilegios y usarlo
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser
```

## Paso 5: Configurar Variables de Entorno y Exponer Puertos

Definir variables de entorno para hacer la configuración más flexible y exponer el puerto necesario.

```dockerfile
# Configurar variables de entorno
ENV APP_ENV=production

# Exponer el puerto en el que se ejecuta la app
EXPOSE 8080
```

## Paso 6: Definir el Comando de Ejecución

Uso de `exec` en el comando CMD para asegurar que los procesos se manejen adecuadamente.

```dockerfile
# Usar exec para ejecutar el proceso correctamente
CMD ["python3", "-m", "http.server", "8080"]
```

## Paso 7: Optimizar Tamaño y Seguridad

Optimización mediante la instalación de paquetes sin caché y la limpieza de archivos temporales.

```dockerfile
# Optimización de imagen
RUN apk add --no-cache --update bash && rm -rf /var/cache/apk/*
```

## Paso 8: Firmar la Imagen y Escanear Vulnerabilidades

Proceso de construcción y escaneo de la imagen para asegurarse de que esté libre de vulnerabilidades conocidas.

```sh
# Construcción de la imagen
docker build -t secure-app .

# Escaneo de vulnerabilidades (con Trivy, por ejemplo)
trivy image secure-app
```



```sh
# Definir una imagen base ligera
FROM alpine:latest

# Establecer un directorio de trabajo seguro
WORKDIR /app

# Instalar dependencias necesarias
RUN apk add --no-cache python3 py3-pip

# Copiar archivos al contenedor
COPY app/ /app/

# Crear un usuario sin privilegios y usarlo
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

# Configurar variables de entorno
ENV APP_ENV=production

# Exponer el puerto en el que se ejecuta la app
EXPOSE 8080

# Usar exec para ejecutar el proceso correctamente
CMD ["python3", "-m", "http.server", "8080"]

# Optimización de imagen
RUN apk add --no-cache --update bash && rm -rf /var/cache/apk/*
```
