# Sitio de tutoriales

Sin dependencias: solo Node 22.13+.

## Probar en tu PC
    ADMIN_USER=admin ADMIN_PASS='TuClaveSegura' node server.js
    # abre http://localhost:3000

Si no defines ADMIN_PASS se genera una y se guarda en data/admin-credentials.txt.
El administrador solo se crea la PRIMERA vez (cuando la base de datos está vacía).

## Publicarlo en internet (necesitas un servidor con disco propio)
Los archivos se guardan en la carpeta DATA_DIR (por defecto ./data). Debe ser un disco PERSISTENTE y con espacio suficiente.

### Opción VPS (recomendada para archivos grandes)
    docker build -t tutoriales .
    docker run -d --restart=always -p 3000:3000 -v /srv/tutoriales:/data \
      -e ADMIN_USER=admin -e ADMIN_PASS='TuClaveSegura' tutoriales
Pon delante Caddy para tener HTTPS (Caddyfile de 3 líneas):
    tu-dominio.com {
      reverse_proxy localhost:3000
    }
(Caddy no limita el tamaño de subida. Con nginx añade `client_max_body_size 0;` y `proxy_request_buffering off;`.)

### Ojo
- Cloudflare (plan gratis) limita a 100 MB por petición si el tráfico pasa por su proxy: para archivos grandes desactiva el proxy (nube gris) en ese dominio.
- Hostings gratis tipo Render/Heroku/Vercel/Netlify NO sirven: disco efímero y límites de subida.
- Haz copias de seguridad de la carpeta de datos (site.db + uploads/).

## Qué incluye
- Registro / login / cambio de contraseña. Sesiones de 30 días, freno de intentos fallidos.
- Panel /admin: crear/editar/borrar tutoriales, subir archivos de cualquier tipo y tamaño (con barra de progreso), ajustes del sitio (nombre, textos, color, pie), abrir o cerrar registros, exigir cuenta para descargar, borrar usuarios.
- Imágenes, vídeos y audios se muestran en la página (con adelantar/retroceder); APK, ZIP y demás se descargan.

## Instalación en un solo paso (VPS Ubuntu)
    sudo bash setup.sh tu-dominio.duckdns.org 'TuClave' https://github.com/USUARIO/REPO.git
Instala Docker, abre puertos 80/443, levanta el sitio y Caddy (HTTPS automático). Requiere que el dominio ya apunte al IP del servidor.