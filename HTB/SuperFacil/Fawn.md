1)¿Qué significa la sigla de 3 letras FTP?
	File Transfer Protocol

2) ¿En qué puerto escucha habitualmente el servicio FTP?
	21

3)FTP envía datos en claro, sin cifrar. ¿Qué acrónimo se utiliza para un protocolo posterior diseñado para proporcionar una funcionalidad similar a FTP pero de forma segura, como una extensión del protocolo SSH?
	SFTP, se le agrega la s de secure

4)¿Cuál es el comando que podemos utilizar para enviar una solicitud de eco ICMP para probar nuestra conexión con el objetivo?
	ping

5)Según tus análisis, ¿qué versión de FTP se está ejecutando en el objetivo?
	vsFTPd 3.0.3
	
6)Según tus escaneos, ¿qué tipo de sistema operativo se está ejecutando en el objetivo?
	Unix

7)¿Cuál es el comando que debemos ejecutar para mostrar el menú de ayuda del cliente “ftp”?
	FTP -?

8)¿Cuál es el nombre de usuario que se utiliza a través de FTP cuando se desea iniciar sesión sin tener una cuenta?
	anonymous

9)¿Cuál es el código de respuesta que obtenemos para el mensaje FTP «Inicio de sesión correcto»?
	230
	
- **200** – Comando exitoso.
- **220** – Servicio listo para nuevos usuarios.
- **221** – Cerrando la conexión.
- **230** – Usuario ha iniciado sesión correctamente.
- **331** – Usuario OK, pero necesita contraseña.
- **530** – No se pudo iniciar sesión.

10)Hay un par de comandos que podemos utilizar para listar los archivos y directorios disponibles en el servidor FTP. Uno es dir. Cuál es el otro que es una forma común de listar archivos en un sistema Linux.
	ls

11)¿Cuál es el comando utilizado para descargar el archivo que encontramos en el servidor FTP?
	get
	ftp       | y una vez estamos en la consola interactiva ponemos help

---------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]
![[Fawn1.png]]

![[Fawn2.png]]
El ttl es 63 asi que es una maquina linux y el puerto 21 ftp esta abierto
Aca vamos a responder las preguntas 5 y 6, ya que nos indica que el OS es Unix y la version del servicio FTP es vsFTPd 3.0.3

# Intrusion y flag
Entramos con el comando `ftp 10.129.250.6`, nos logeamos con el usuarios anonymous sin poner contraseña, con ls vimos los archivos que habia en el directorio, con get descargamos el archivo flag.txt y ya teniamos el archivo en nuestra maquina para posteriormente catearlo | [[ftp]]

![[Fawn3.png]]