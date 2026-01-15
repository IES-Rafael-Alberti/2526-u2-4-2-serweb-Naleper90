# Práctica Nginx + SFTP + HTTPS

## Fase 1: Instalación y configuración de Nginx

### 1. Servicio Nginx activo
Se ha levantado la infraestructura con `docker compose up -d` y se ha comprobado el estado de los contenedores con `docker compose ps`.
> **Captura:** ![Servicio Nginx activo](evidencias/docker_compose_ps.png)

### 2. Configuración cargada
Dentro del contenedor de Nginx se ha listado `/etc/nginx/conf.d` para comprobar que se carga `default.conf`.
> **Captura:** ![default.conf en conf.d](evidencias/reloj2.png)

### 3. Resolución de nombres
Se ha añadido en el archivo `hosts` de Windows la entrada `127.0.0.1  cloud.local` y se ha configurado `server_name cloud.local` en los bloques `server` de Nginx.
> **Captura:** ![Navegador usando cloud.local](evidencias/evidencia_cloudlocal.png)

### 4. Contenido web de CloudAcademy
En la carpeta `html` del proyecto se ha clonado la web estática de CloudAcademy, que se monta en `/usr/share/nginx/html` dentro del contenedor y se sirve por HTTPS.
> **Captura:** ![Contenido de CloudAcademy](evidencias/contenido_cloudacademy.png)

---

## Fase 2: Transferencia SFTP (FileZilla)

### 5. Conexión SFTP exitosa
Se ha configurado FileZilla para conectar mediante SFTP a `localhost:2222` con el usuario `alumno`, verificando que el servidor acepta la conexión.
> **Captura:** ![Conexión SFTP correcta](evidencias/evidencia-cruzada.png)

### 6. Permisos de escritura
Se ha subido la web a través de SFTP a la carpeta `/upload`, que comparte volumen con Nginx, comprobando que las transferencias terminan correctamente.
> **Captura:** ![Subida de archivos por SFTP](evidencias/evidencia-permisos.png)

---

## Fase 3: Infraestructura Docker

### 7. Contenedores activos
Ambos servicios de Docker (Nginx y SFTP) están activos simultáneamente y exponiendo los puertos configurados.
> **Captura:** ![Contenedores Nginx y SFTP](evidencias/docker_ps_sftp_nginx.png)

### 8. Persistencia y volumen compartido
Nginx y SFTP comparten el volumen `./html`, de modo que los ficheros subidos por SFTP se sirven desde el contenedor web.
> **Captura:** ![Volumen compartido entre SFTP y Nginx](evidencias/evidencia-cruzada.png)

### 9. Despliegue multi-sitio – Web del reloj en `/reloj`

La segunda web (reloj) se ha desplegado en una subcarpeta del mismo sitio:

- Estructura local: `html/reloj/index.html`, `script.js`, `style.css`.
  > **Captura:** ![Captura reloj 1](evidencias/reloj1.png)

- Ese directorio se monta en `/usr/share/nginx/html/reloj` dentro del contenedor de Nginx y se comprueba que Nginx lo reconoce.
  > **Capturas:**  
  > ![Captura reloj 2](evidencias/reloj2.png)  
  > ![Captura reloj 3](evidencias/reloj3.png)  
  > ![Captura reloj 4](evidencias/reloj4.png)

Aunque el despliegue está correctamente configurado y verificado desde dentro del contenedor, al acceder desde el navegador del host a `https://cloud.local:8443/reloj` (o URLs equivalentes) se obtiene un error `ERR_CONNECTION_REFUSED`, lo que apunta a un problema de la capa de red local ajeno a la configuración de Nginx.

---

## Fase 4: Seguridad HTTPS

### 10. Cifrado SSL
Se ha generado un certificado autofirmado y se ha montado en el contenedor mediante el volumen `./certs`, configurando Nginx para servir el sitio por HTTPS.
> **Captura:** ![Ficheros del certificado en el proyecto](evidencias/certs.png)

### 11. Redirección forzada de HTTP a HTTPS
Se ha configurado Nginx para que cualquier petición HTTP en el puerto 8080 sea redirigida automáticamente a HTTPS en el puerto 8443 mediante un código 301.
> **Captura:** ![Redirección 301 de HTTP a HTTPS](evidencias/evidencia_certificado2.png)

---

## Autora

Natalia Alejo Pérez - 2º DAW
