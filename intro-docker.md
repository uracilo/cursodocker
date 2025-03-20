

```dockerfile

# Construir la imagen
docker build -t secure-app .

# Etiquetar la imagen
docker tag secure-app miusuario/secure-app:latest

# Iniciar sesión en Docker Hub
docker login

# Subir la imagen
docker push miusuario/secure-app:latest
```
