Es una funcion basada en el mkt de s4vitar, que crea una carpeta con el nombre que le pongamos, y adentro hace una subcarpeta llamada content, y otra nmap y nos deja dentro de la carpeta nmap para ya comenzar con nuestro escaneos de [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]


-------
# Como instalar
Poner el contenido dentro del archivo ``~/.zshrc``. Yo lo hago con el comando `nvim ~/.zshrc`

```
function vm() {
	if [ -z "$1" ]; then
		echo "Uso: vm <nombre_de_maquina>"
		return 1
	fi

	mkdir -p "$1"/{content,nmap}
	cd "$1/nmap" || return
}
```