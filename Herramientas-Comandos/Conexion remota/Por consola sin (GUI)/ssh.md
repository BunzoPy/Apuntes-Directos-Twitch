
# SSH 

```shell
ssh Usuario@Ip -p Puerto
Ejemplo:ssh christine@10.129.243.9 -p 22
```
*Parametros:*
`-p` Especifica el puerto

![[Funnel7.png]]

---------

# Tunneling

El tunneling es una técnica que permite redirigir el tráfico de red de una máquina víctima hacia nuestra máquina, creando un canal seguro y cifrado. Así, todo el tráfico que pasa por el túnel va directo a nuestra computadora, permitiendo controlar o inspeccionar la comunicación.

- **Si solo querés conectar un puerto específico** (como PostgreSQL en el puerto 5432 remoto) —> Usás **reenvío local (-L)**.
- **Si querés enrutar tráfico de múltiples puertos o aplicaciones** a través de un proxy, sin configurar puertos específicos —> Usás **túnel dinámico (-D)**.

```
Ejemplo: ssh -L NuestroPuertoAUsar:DestinoEnNuestraMaquina:PuertoVictimaQueQueremos Usuario@IpMaquinaVictima
Ejemplo: ssh -L 1234:localhost:5432 christine@10.129.243.9
```
*Parametros*
`-L` Es para redireccionar un puerto local
	`NuestroPuertoAUsar` Es el puerto de nuestra maquina al que queremos desviar el trafico de la maquina victima
	`DestinoEnNuestraMaquina` Normalmente se pone localhost, por que queremos desviar el trafico hacia nuestra maquina/ip
	`PuertoVictimaQueQueremos` Es el puerto al que queremos robarle el trafico de la maquina victima



------------------

# SSH archivo con id_rsa

```
ssh -i Archivo Usuario@Ip -p Puerto
ssh -i id_rsa daniel@10.129.127.52 -p 22
```
*Parametros*
`-i` Hay que poner el archivo de identificación con el cual nos vamos a conectar
#### Si esta activo el servicio ssh, podemos buscar las credenciales en su directorio c:/users/daniel/.ssh/id_rsa
Para las id_rsa dar el permiso 400 `chmod 400 id_rsa`, si tiene mas permisos de lo indicado va a tirar error de *are too open*

------
# SSH con encriptación antigua

```bash
ssh -o PubkeyAcceptedKeyTypes=+ssh-rsa -o HostKeyAlgorithms=+ssh-rsa -i Archivo_RSA Usuario@Ip
Ejemplo: ssh -o PubkeyAcceptedKeyTypes=+ssh-rsa -o HostKeyAlgorithms=+ssh-rsa -i id_rsa hype@10.10.10.79
```
*Parametros*
`-o PubkeyAcceptedKeyTypes=+ssh-rsa` Le dice al cliente que acepte claves publicas tipo ssh-rsa, que ya estan obsoletas y no se usan
`-o HostKeyAlgorithms=+ssh-rsa` Permite aceptar claves del tipo ssh-rsa
`-i` Especifica el archivo que contiene la clave RSA



![[Valentine14.png]]

