1)¿Cuántos puertos TCP abiertos escucha Blue? No incluya ningún puerto de 5 dígitos.
	3
	135,139,445
	
2)¿Cuál es el nombre de host de Blue?
	haris-pc

3)¿Qué sistema operativo se está ejecutando en la máquina de destino? Dé una respuesta de dos palabras con un nombre y una versión de alto nivel.
	windows 7

4)¿Cuántas acciones SMB están disponibles en Blue?
	5

5)Qué número de boletín de seguridad de Microsoft de 2017 describe una vulnerabilidad de ejecución remota de código en SMB?
	MS17-010

6)En mayo de 2017 se soltó en Internet un gusano que se propagaba principalmente a través de MS17-010. Cuál es el famoso nombre de ese malware?
	WannaCry

7)¿Con qué usuario se ejecuta el exploit MS17-010? Incluya el nombre completo, incluyendo cualquier cosa antes de un .
	

-------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.10.10.40
nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 10.10.10.40 -oG allPorts
extractPorts allPorts
nmap -sCV -p135,139,445,49152,49153,49154,49155,49156,49157 10.10.10.40 -oN target
```

![[Blue1.png]]
![[Blue2.png]]
![[Blue3.png]]
![[Blue4.png]]
*TTL:* Maquina windows
*Puertos:*
`445`[[smb]]
*Otra informacion relevante:*
Para smb nos podemos conectar con el usuario guest, con permisos de user
Usuario:Harris
Computadora windows 7

--------
# Intrusion por [[smb]]

```shell
smbclient -L 10.10.10.40 -p 445 -N
```
![[Blue5.png]]
Listando los contenidos de las carpetas vemos que no encontramos nada

### Despeus de probar muchas cosas no podiamos ejecutar por problemas de compatibilidad, dejamos la maquina para un futuro, lo que pude hacer fuera de stream 
Instale el impacket desde python 2.7 y hice que andara el pip2

cd /usr/src
sudo wget https://www.python.org/ftp/python/2.7.18/Python-2.7.18.tgz
sudo tar xzf Python-2.7.18.tgz
cd Python-2.7.18
sudo ./configure --enable-optimizations
sudo make altinstall

pip2 install impacket==0.9.24

----------

# Notas

WannaCry fue un ransomware que se propagó en mayo de 2017 aprovechando la vulnerabilidad MS17-010 (EternalBlue). Infectaba computadoras con Windows y cifraba los archivos, exigiendo un rescate en Bitcoin. Se expandió rápidamente como un gusano, afectando miles de organizaciones en todo el mundo. Fue detenido parcialmente gracias al descubrimiento de un kill switch por un investigador.
Un **kill switch** (interruptor de apagado) es un mecanismo oculto en un malware que permite **detener su funcionamiento** si se cumple cierta condición. En el caso de WannaCry, era un dominio web que, si estaba registrado y respondía, hacía que el malware se detuviera. Un investigador lo descubrió por accidente, registró ese dominio, y sin querer **frenó la propagación global** del ransomware.



El exploit espera un archivo **binario puro** porque solo necesita el código que se va a ejecutar directamente en memoria, sin ningún extra.
Un `.exe` tiene toda la estructura de un programa (encabezados, secciones, etc.) que no sirve para inyección directa; el exploit no puede procesar ni entender ese formato completo.
Además, el `.exe` es para cuando querés que un usuario lo ejecute manualmente, no para inyectarlo directamente en memoria como hace el exploit. Por eso, para exploits se usa el `.bin` con solo el shellcode.