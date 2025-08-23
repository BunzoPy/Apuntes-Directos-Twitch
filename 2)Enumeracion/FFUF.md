#ffuf

------
# Resumen de comandos a usar
```shell
ffuf -u http://10.10.11.130/FUZZ -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -e .php,.txt,.html
ffuf -u http://10.10.11.130/FUZZ/ -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -e .php,.txt,.html
ffuf -u http://FUZZ.10.10.11.130 -w /usr/share/SecLists/Discovery/DNS/subdomains-top1million-5000.txt 
```

*Diccionarios*
Uso los diccionarios de [SecLists](https://github.com/danielmiessler/SecLists) y en este apunte esta la ubicacion de cada diccionario que usamos [[Diccionarios para fuzzing]]

------
## Enumeracion de directorios

##### 1) /FUZZ
La diferencia entre /FUZZ/ y /FUZZ es que con la / al final solo enumera directorios y sin la / al final, busca directorios como archivos, los dos pueden dar resultados distintos. asi que recomiendo probar de las dos formas
```shell
ffuf -u Web/FUZZ -u RutaDiccionario -e ExtensionesDeArchivos
Ejemplo: ffuf -u http://10.10.11.130/FUZZ -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -e .php,.txt,.html
Ejemplo si usamos un diccionario mas grande: ffuf -u http://10.10.11.130/FUZZ -w /usr/share/SecLists/Discovery/Web-Content/`directory-list-2.3-big.txt` -e .php,.txt,.html
```
*Parametros*
	`FUZZ` La palabra FUZZ  va donde el diccionario va a ir cambiando de palabras, para hacer su trabajo de enumeración
	`-u` Es para poner la url que vamos a enumerar
	`-w` La ruta del diccionario a usar
	`-e` Las extensiones de los archivos que queremos buscar
*Parametros opcionales*
	`-t` Sirve para poner la cantidad de hilos, por default usa 40, en lo personal yo no modifico la cantidad de hilos
##### 2)/FUZZ/

```shell
ffuf -u Web/FUZZ/ -u RutaDiccionario -e ExtensionesDeArchivos
Ejemplo: ffuf -u http://10.10.11.130/FUZZ/ -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -e .php,.txt,.html
Ejemplo si usamos el direccionario mas grande: ffuf -u http://10.10.11.130/FUZZ/ -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-big.txt -e .php,.txt,.html
```
	`FUZZ` La palabra FUZZ  va donde el diccionario va a ir cambiando de palabras, para hacer su trabajo de enumeración
	`-u` Es para poner la url que vamos a enumerar
	`-w` La ruta del diccionario a usar
	`-e` Las extensiones de los archivos que queremos buscar
*Parametros opcionales*
	`-t` Sirve para poner la cantidad de hilos, por default usa 40, en lo personal yo no modifico la cantidad de hilos
##### 3)Subdominios
```shell
ffuf -u FUZZ.Web -w RutaDiccionario -H "Host: FUZZ.Web"
ffuf -u http://thetoppers.htb/ -w /usr/share/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.thetoppers.htb"
Ejemplo si usamos el dicionario mas grande: ffuf -u http://FUZZ.10.10.11.130 -w /usr/share/SecLists/Discovery/DNS/subdomains-top1million-110000.txt 
```
*Parametros*
	`FUZZ` La palabra FUZZ  va donde el diccionario va a ir cambiando de palabras, para hacer su trabajo de enumeración
	`-u` Es para poner la url que vamos a enumerar
	`-w` La ruta del diccionario a usar
*Parametros opcionales*
	`-t` Sirve para poner la cantidad de hilos, por default usa 40, en lo personal yo no modifico la cantidad de hilos
*Dato Importante:*
Solamente podemos enumerar subdominios si conocemos el dominio