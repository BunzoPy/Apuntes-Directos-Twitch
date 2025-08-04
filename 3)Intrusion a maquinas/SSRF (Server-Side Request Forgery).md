#SSRF

-------
**SSRF (Server-Side Request Forgery)** es una vulnerabilidad donde un atacante engaña al servidor para que haga solicitudes a direcciones internas o externas, accediendo a recursos que normalmente están restringidos. Esto puede usarse para leer datos internos, escanear redes privadas o explotar otros servicios. Se previene validando las URLs de entrada, bloqueando IPs internas y limitando las solicitudes del servidor.

-----
# Ejemplo con [[CVE-2023-27163]]
![[Sau17.png]]
Podemos abajo de todo que nos dice qeu es un request-baskets. Version 1.2.1
Vimos que era el [[CVE-2023-27163]] pero no encontramos ningun proyecto que pueda explotar esta vulnerabilidad que funcione.

Desde la pagina web vamos a crear un basket poniendo el nombre que querramos, entramos con el boton de *Open Basket* 
![[Sau14.png]]

Vamos a apretar sobre la tuerca que esta arriba a la derecha para configurar el basket, vamos a poner la URL a la que queremos redigir, que va a ser la ip local de la maquina victima, y el puerto `80` que vimos filtrado, por lo tanto no sabemos si esta abierto o cerrado. Por lo tanto seria http://127.0.0.1:80  Y tambien vamos a seleccionar las dos casillas de abajo de *Proxy Response* y *Expand Forward Path*

![[Sau13.png]]

Ahora entramos al basket mediante el link http://10.10.11.224:55555/testeo
![[Sau12.png]]
Como podemos ver ya nos redirigue a otra pagina