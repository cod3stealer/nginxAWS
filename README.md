# nginxAWS

## Bloqueo del acceso a las URL

Para llevar a cabo esto hay que entrar al archivo de configuración de sitios Web de Nginx llamado `/etc/nginx/sites-available/default` y escribiré la siguiente **línea de código**:
```
if ($request_iro ~* (elmundo|donar|humanitaria|nigeria) ){
    return 403;
}
```
De esta forma el archivo de configuración ha de ser así:
```
server {
        listen 80 default_server;
        listen [::]:80 default_server;
        root /var/www/html;
        index index.html index.htm index.nginx-debian.html;
        server_name _;
        location / {
if ($request_iro ~* (elmundo|donar|humanitaria|nigeria) ){
    return 403;
}
                try_files $uri $uri/ =404;
        }
}
```
Ahora guarda el archivo ***CTRL + S*** y reinicia el servicio Nginx con **service nginx restart**.

Ahora desde un equipo remoto intento acceder a URLs que contengan estas palabras:

elmundo
donar
humanitaria
nigeria

Debería aparecer el siguiente output:
```
HTTP/1.1 403 Forbidden
Server: nginx/1.18.0 (Ubuntu)
Date: Fri, 16 Apr 2021 00:53:09 GMT
Content-Type: text/html
Content-Length: 162
Connection: keep-alive
```

## Redirección HTTP a HTTPS

En el archivo de configuración escribo lo siguiente:
```
server {
        listen 80 default_server;
        listen [::]:80 default_server;
        root /home/ec2-user;
        index index.html index.htm index.nginx-debian.html;
        server_name _;
        return 301 https://$host$request_uri;
        location / {
                try_files $uri $uri/ =404;
        }
}
```
Ahora al hacer `curl -I http://example` el output será:
```
HTTP/1.1 301 Moved Permanently
Server: nginx/1.18.0 (Ubuntu)
Date: Mon, 12 Apr 2021 02:02:43 GMT
Content-Type: text/html
Content-Length: 178
Connection: keep-alive
Location: https://www.example/
```

## Redirigir error 404 a una página

En el archivo de configuración escribo lo siguiente:
```
server {
        listen 80 default_server;
        listen [::]:80 default_server;
        root /var/www/html;
        index index.html index.htm index.nginx-debian.html;
        server_name _;
        location / {
error_page 404 http://www.gameking.tips/error-404;
                try_files $uri $uri/ =404;
        }
}
```
Al hacer `curl` a una página que no existe salta lo siguiente:
```
HTTP/1.1 302 Moved Temporarily
Server: nginx/1.18.0 (Ubuntu)
Date: Sun, 11 Apr 2021 15:37:52 GMT
Content-Type: text/html
Content-Length: 154
Connection: keep-alive
Location: http://.tips/error-404
```


