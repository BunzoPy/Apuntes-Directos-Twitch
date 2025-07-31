#nmapscripts 

----

Este apartado es para nombrar scripts de nmap, que nos pueden ayudar a enumerar vulnerabilidades

```
nmap --script NombreDelScript -pPuertos Ip -oN NombreDelArchivoParaGuardarOutput
Ejemplo:nmap --script hearthbleed -p80,22 10.10.11.130 -oN smbScan
```

Nombres de scripts:
`ssl-heathbleed` [[Hearthbleed]]
`http-shellshock` [[Shellshock]] y aparte hay que añadir al comando este parametro `--script-args uri=/cgi-bin/user.sh` donde especifica el archivo al que vamos a enumerar
``"smb-vuln*"``Dice si hay vulnerabilidades para [[smb]]              | Poner las "" por que si no el  \* en la zsh
`http-enum` Hace una enumeracion de directorios con un pequeño diccionario como si fuera [[Gobuster]]           | Ejemplo: nmap --script http-enum -pPuerto Ip -oN webScan