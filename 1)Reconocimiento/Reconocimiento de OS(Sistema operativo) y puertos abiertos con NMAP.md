---

---
#ping #nmap 

# Resumen de comandos en orden para hacer reconocimiento
```shell
ping -c 1 IP
nmap -p- --open -sS -n -pN --min-rate 5000 -vvv IP -oG allports
extractPorts allports
nmap -sCV -pPuertos IP -oN extractPorts
```



-----------
# 1)Ping
Enviamos un ping para ver si nos responde la maquina y ver el ttl, que eso nos indica que OS tiene
```shell
ping -c 1 IP
// Ejemplo: ping -c 1 10.10.10.130
```

*Parametros*
`-c 1` Esto hace que envie solo un paquete, si quisieramos enviar mas, cambiamos le 1 por la cantidad que querramos

![[Shocker3.png]]
Esto nos va a indicar el ttl, que en este caso es 63, asi que es linux, si fuera 127 seria windows

-----------
# 2)NMAP: Reconocimiento de puertos
```shell
nmap -p- --open -sS -n -pN --min-rate 5000 -vvv IP -oG allports
// Ejemplo: nmap -p- --open -sS -n -Pn --min-rate 5000 -vvv 10.10.10.79 -oG allports
```
   ![[Valentine2.png]]


*Parametros:*
	-p- Escanea todo los puertos 65535
	--open para que nos muestre solamente los puertos que estan abiertos, ya que no nos interesa ver cuales estan cerrados
	-sS es stealth scan, es un escaneo mas rapido y silencioso, solo envia paquetes syn para verificar la conexion
		manda conexion SYN, recibe un SYN-ACK por lo tanto sabe que el puerto abierto, pero no contesta con un ACK, sino que no responde o manda un RST(es una flag que cierra la conexion o la rechaza) para cerrar la conexion
	-n no hace resolucion DNS, no intenta convertir la ip en dominios
	-Pn no hace ping antes de escanear, asume que todos los hosts estan activos. Cuando los paquetes ICMP (ping) están bloqueados por el cortafuegos de Windows, Nmap no puede determinar si el host está activo usando el método de ping tradicional. Para evitar esto y aun así intentar escanear el host se usa este parametro
	--min-rate 5000 manda al menos 5000 paquetes por segundo para acelerar el escaneo
	-vvv es para ir mostrando los puertos que estan abiertos a medida que se produce el escaneo
	-oG es para exportar en un archivo en formato grepeable, que seria facil de buscar con el comando grep
	

### Escaneo por UDP
```
nmap -sU -Pn -n -vvv --top-ports 200 10.129.207.145 -oN udp-scan
```
*Parametros:*
	`-sU` Indica que va a ser udp
	`-Pn`Asume que el host esta activo
	`-n` No hace resolucion DNS
	`--top-ports 200`Escanea los 200 puertos mas usados, este numero se puede cambiar
	`-oN` Es para exportar el resultado en un archivo con extension nmap

-----------

# 3)NMAP: Lanzamiento de scrips basicos de reconocimiento y analizar el programa y version que corren
#### Previo al escaneo usar el a funcion de extractPorts de s4vitar para extraer los puertos abiertos
```shell
extractPorts allports
```

*Script:*

```zshrc
function extractPorts(){
	ports="$(cat $1 | grep -oP '\d{1,5}/open' | awk '{print $1}' FS='/' | xargs | tr ' ' ',')"
	ip_address="$(cat $1 | grep -oP '\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}' | sort -u | head -n 1)"
	echo -e "\n[*] Extracting information...\n" > extractPorts.tmp
	echo -e "\t[*] IP Address: $ip_address"  >> extractPorts.tmp
	echo -e "\t[*] Open ports: $ports\n"  >> extractPorts.tmp
	echo $ports | tr -d '\n' | xclip -sel clip
	echo -e "[*] Ports copied to clipboard\n"  >> extractPorts.tmp
	cat extractPorts.tmp; rm extractPorts.tmp
}
```


## Ahora si realizamos el escaneo con puertos ya pegados en la clipboard

```shell
nmap -sCV -pPuertos IP -oN extractPorts
//Ejemplo: nmap -sCV -p80,22,4443 10.10.10.130 -oN extractPorts
```

 ![[Valentine3.png]]

*Parametros*
	-sCV Es la junta de los parametros -sC -sV
		-sC manda los 100 scripts mas populares de reconocimiento
		-sV Averigua que servicio y version estan corriendo
	oN para exportar en un archivo con formato NMAP todo el output

-----------




# Notas

**TTL (Time To Live)** es un número que indica cuántos **saltos (hops)** puede hacer un paquete IP antes de ser descartado.

- Cada vez que un paquete pasa por un router, el TTL **se reduce en 1**.
- Cuando el TTL llega a **0**, el paquete se descarta. Esto evita que los paquetes queden dando vueltas infinitamente si hay un bucle en la red.
-  Si el ttl esta cerca de 64 puede ser una maquina linux y si esta cerca de 128 puede ser windows. Estos valores son manipulables
- HackTheBox: Como e lpaquete pasa por un router que seria el de HTB antes de llegar a nosotros, pierde un paquete, por lo tanto el numero se disminuye en 1 
