**Searchsploit** es una herramienta de línea de comandos que permite **buscar vulnerabilidades y exploits conocidos** en la base de datos de Exploit Database (exploit-db.com), **sin conexión a internet**.


📦 Sirve para:

- Buscar exploits para software, servicios, CMS, etc.
    
- Consultar rápidamente qué vulnerabilidades públicas existen.
    
- Extraer o ver ejemplos de exploits listos para usar.

-------

#### Como usar searchsploit
```shell
searchsploit software/servicio/cms/etc
Ejemplo: searchsploit CMS MADE SIMPLE
```
![[Writeup5.png]]
Nos va a arrojar todas las vulnerabilidades disponibles y ya nosotros seleccionamos cual vamos a probar

### Descargar el exploit
```shell
searchsploit -m Ubicacion
Ejemplo: searchsploit -m php/webapps/46635.py
```
La ubicacion es la que nos aparece a la derecha del nombre del exploit. Y esta va a ser descargada en nuestro directorio actual de trabajo