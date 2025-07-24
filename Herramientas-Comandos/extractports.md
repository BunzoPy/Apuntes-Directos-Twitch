Es una función de bash (de S4vitar) que **extrae automáticamente los puertos abiertos** desde un archivo de salida de Nmap en formato **grepeable (`-oG`)**, y los **copia al portapapeles**, listos para usar en un segundo escaneo más profundo (por ejemplo, con `-p`).
Para saber como usarlo, se puede ver [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

------
# Como instalar
Poner el contenido dentro del archivo ``~/.zshrc``. Yo lo hago con el comando `nvim ~/.zshrc`

```
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

