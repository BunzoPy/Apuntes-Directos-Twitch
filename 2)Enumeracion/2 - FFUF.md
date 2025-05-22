# Resumen de comandos a usar
```shell
ffuf -u http://10.10.11.130/FUZZ -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -e .php,.txt,.html
ffuf -u http://10.10.11.130/FUZZ/ -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -e .php,.txt,.html
ffuf -u http://FUZZ.10.10.11.130 -w /usr/share/SecLists/Discovery/DNS/subdomains-top1million-5000.txt 
```

----

En el caso de que el script http-enum de nmap no de ningun resultado relevante vamos a usar FFUF
Es una herramienta que esta diseñada en Go, también se podría usar gobuster, pero en lo personal me gusta mas ffuf
	**

*Aclaracion sobre diccionarios*
En lo personal uso los diccionarios de [SecLists](https://github.com/danielmiessler/SecLists) y siempre uso primero para la parte de directorios el `directory-list-2.3-small.txt` y si no encuentro nada paso a un diccionario mas grande `directory-list-2.3-big.txt` y en el caso de que estemos enumerando subdominios `subdomains-top1million-5000.txt` y si no encontramos nada pasamos al mas grande `subdomains-top1million-110000.txt`

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
ffuf -u FUZZ.Web -w RutaDiccionario
ffuf -u http://FUZZ.10.10.11.130 -w /usr/share/SecLists/Discovery/DNS/subdomains-top1million-5000.txt 
Ejemplo si usamos el dicionario mas grande: ffuf -u http://FUZZ.10.10.11.130 -w /usr/share/SecLists/Discovery/DNS/subdomains-top1million-110000.txt 
```
	`FUZZ` La palabra FUZZ  va donde el diccionario va a ir cambiando de palabras, para hacer su trabajo de enumeración
	`-u` Es para poner la url que vamos a enumerar
	`-w` La ruta del diccionario a usar
*Parametros opcionales*
	`-t` Sirve para poner la cantidad de hilos, por default usa 40, en lo personal yo no modifico la cantidad de hilos
