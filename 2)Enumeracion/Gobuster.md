Es parecido al [[FFUF]], sirve para enumerar directorios y subdominios
*Aclaracion sobre diccionarios*
En lo personal uso los diccionarios de [SecLists](https://github.com/danielmiessler/SecLists) y siempre uso primero para la parte de directorios el `directory-list-2.3-small.txt` y si no encuentro nada paso a un diccionario mas grande `directory-list-2.3-big.txt` y en el caso de que estemos enumerando subdominios `subdomains-top1million-5000.txt` y si no encontramos nada pasamos al mas grande `subdomains-top1million-110000.txt`

-------
# Enumeracion de subdominios

```shell
gobuster vhost -u Web -w Diccionario --append-domain -t 20
Ejemplo: gobuster vhost -u http://ignition.htb -w /usr/share/SecLists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain -t 40
```

*Parametros:*
`-u` Sirve para poner la url que queremos eunmerar
`-w` Es para seleccionar el diccionario
`--apend-domain` Concatena el valor de cada palabra del diccionario con el del dominio, si no se pone este parametro no va a detectar subdominios
*Parametros opcionales*
`-t` Especifica la cantidad de hilos que que queremos usar, por defecto usa 10

-------
# Enumeracion de directorios

```shell
gobuster dir -u Web -w Diccionario --add-slash -t 20
Ejemplo: gobuster dir -u http://ignition.htb -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt --add-slash -t 20
```


*Parametros:*
`-u` Sirve para poner la url que queremos eunmerar
`-w` Es para seleccionar el diccionario
`--add-slash` Al final de cada directorio tambien prueba agregar un `/` esto hace que busque tanto directorios como archivos
`-t` Especifica la cantidad de hilos que que queremos usar, por defecto usa 10