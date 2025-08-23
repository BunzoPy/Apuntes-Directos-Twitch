#gobuster

--------

Es parecido al [[FFUF]], sirve para enumerar directorios y subdominios
*Aclaracion sobre diccionarios*
En lo personal uso los diccionarios de [SecLists](https://github.com/danielmiessler/SecLists) y en este apunte esta la ubicacion de cada diccionario que usamos[[Diccionarios para fuzzing]]

#### Comando de ayuda
```shell
gobuster dir -h
```

-------
# Enumeracion de subdominios

```shell
gobuster vhost -u Web -w Diccionario --append-domain -t 64
Ejemplo: gobuster vhost -u http://ignition.htb -w /usr/share/SecLists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain -t 64
```

*Parametros:*
`-u` Sirve para poner la url que queremos enumerar
`-w` Es para seleccionar el diccionario
`--apend-domain` Concatena el valor de cada palabra del diccionario con el del dominio, si no se pone este parametro no va a detectar subdominios
*Parametros opcionales*
`-t` Especifica la cantidad de hilos que que queremos usar, por defecto usa 10

*Dato Importante:*
Solamente podemos enumerar subdominios si conocemos el dominio

-------
# Enumeracion de directorios

```shell
gobuster dir -u Web -w Diccionario --add-slash -t 64
Ejemplo: gobuster dir -u http://ignition.htb -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt --add-slash -t 64

// Sin barra al final

gobuster dir -u Web -w Diccionario -t 64
Ejemplo: gobuster dir -u http://ignition.htb -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -t 64

//Para buscar archivos con extensiones especificas

gobuster dir -u Web -w Diccionario -t 64 -x Extensiones
Ejemplo: gobuster dir -u http://ignition.htb -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -t 64 -x php,txt,bak,html
```


*Parametros:*
`-u` Sirve para poner la url que queremos enumerar
`-w` Es para seleccionar el diccionario
`--add-slash` Al final de cada directorio tambien prueba agregar un `/` esto hace que busque tanto directorios como archivos
`-t` Especifica la cantidad de hilos que que queremos usar, por defecto usa 10
`-b` Excluye codigos de respuesta que no querramos por ejemplo 302
`-x` Busca archivos con la extension que pongamos              Ejemplo: -x php,txt,bak,html 
	*Importante:* Cuando usás `--add-slash` **junto con `-x` (extensiones)**, **gobuster ya no busca archivos como `admin.php`** porque las rutas terminan en `/`, lo cual rompe la lógica de archivo.
