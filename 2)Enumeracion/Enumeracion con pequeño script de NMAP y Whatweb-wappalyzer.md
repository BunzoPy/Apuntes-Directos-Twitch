
# Resumen de comandos a usar

```shell
nmap --script http-enum -pPuerto Ip -oN webScan
whatweb PaginaWeb
```

------

# Script NMAP

Antes de utilizar herramientas más avanzadas como **ffuf** o diccionarios más extensos, podemos aprovechar un pequeño script incluido en **Nmap**, el cual realiza una enumeración básica utilizando una lista predefinida de los aproximadamente **1000 nombres de directorios más comunes**. Esta técnica es útil para obtener una primera visión rápida del contenido expuesto en el servidor.

```shell
nmap --script http-enum -pPuerto Ip -oN webScan
Ejemplo: nmap --script http-enum -p80,22 10.10.10.130 -oN webScan
```

*Parametros*
	`--script` Permite usar scripts NSE como `http-enum` para enumerar rutas o `ssl-heartbleed` para detectar vulnerabilidades.
	`-p` Es para indicar los puertos puede ser la cantidad que querramos ejemplo -p80,22,443
	`Ip` Solamente va la ip de la victima
	`-oN webScan` Es para exportar en un archivo con formato nmap con nombre webScan, se le puede poner el nombre que quieras al archivo no hace falta que sea el mismo que le ponemos nosotros

![[Valentine6.png]]
En este ejemplo practico, nos muestra como están disponibles los directorios /dev/ y /index/
##### En el caso de que no encontremos nada, podemos pasar a enumerar con [[FFUF]] que vamos a usar diccionarios mas extensos

------

# Whatweb-wappalyzer
Estas dos herramientas nos permiten identificar las tecnologías que corren en la pagina web de la victima en caso de que tuviera una. 
*whatweb* es una herramienta a nivel de consola y *wappalyzer* esta disponible como extension del navegador

`Whatweb`
```shell
whatweb Paginaweb
//Ejemplo whatweb http://10.10.11.130 en el caso de que sea una ip, pero tambien se podria usar por ejemplo en google. whatweb https://google.com
```
![[whatweb.png]]

`Whappalyzer`
Es una extension que se puede descargar para cualquier navegador aca les dejo el [Link de la pagina oficial](https://www.wappalyzer.com/apps/).
Solamente apretando el icono de la extension les va a salir todas las tecnologias que tiene
![[whappalyzer.png]]