#impacket-lookupsid

-----

Es una **herramienta de Impacket** que se utiliza para **enumerar usuarios, grupos y SIDs en un dominio Windows/Active Directory**.
Enumera cuando no tenemos contraseña, ya que en el netexec si o si tenemos que proporcionar una ya que no podemos dejar el parametro sin contenido

------
# Comando

```shell
impacket-lookupsid 'dominio/Usuario'@Host -no-pass
Ejemplo:impacket-lookupsid 'cicada.htb/guest'@cicada.htb -no-pass
```
*Parametros*
	`Dominio/Usuario` En el dominio vamos a poner el mismo que agregamos en el /etc/hosts. Despues el nombre de usuario con el que nos vamos a conectar, se puede probar con *guest* que es el mas comun
		Tener en cuenta que se podria probar poniendo una ip enves de un dominio pero puede tirar errores
	`@host` Es para poner el dominio o la IP
	`-no-pass` Es que no voy a proporcionar contraseña

# Ejemplo si queremos una regex para solamente sacar los usuarios

```
impacket-lookupsid 'cicada.htb/guest'@10.10.11.35 -no-pass | grep -i "sidtypeuser" | sed 's/.*\\//' | sed 's/ .*//' > users.txt
```

Esto me sirvio para tener la lista de usuarios y luego aplicarle fuerza bruta