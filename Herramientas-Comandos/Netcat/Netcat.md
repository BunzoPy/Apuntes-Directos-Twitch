Netcat (o `nc`) es una herramienta de red que permite enviar y recibir datos a través de conexiones TCP o UDP.  
Se usa para crear [[Reverse shell]], transferir archivos, o escanear puertos.  
Es muy versátil y está presente en muchas distribuciones Linux y Windows.

-------
# Descargar archivo para mandar una [[Reverse shell]]
[Link de netcat para 64bits](https://github.com/int0x33/nc.exe/blob/master/nc64.exe?source=post_page-----a2ddc3557403----------------------) 
Vamos a levantar un server de [[python3 -m http.server]] desde nuestra maquina, para que por wget lo descargue la maquina victima
Esto normalmente lo usamos para ponernos en escucha con  [[rlwrap nc -lvnp 443]] para la reverseshell