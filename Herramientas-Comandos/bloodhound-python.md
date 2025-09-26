#bloodhound-python

Es una herramienta de recolección de información (ingesta de datos) que se usa en entornos de **Active Directory (AD)** para facilitar la identificación de relaciones de confianza y posibles rutas de ataque.. Para luego pasarla por el [[bloodhound]]

-----
# Comando

```shell
bloodhound-python -u 'Usuario' -p 'Contraseña' -ns Ip -d Dominio -c all
Ejemplo: bloodhound-python -u 'Olivia' -p 'ichliebedich' -ns 10.129.113.25 -d administrator.htb -c all
```

*Parametros:*
	`-u` Es para poner el usuario del dominio
	`-p` Para la contraseña del usuario
	`-ns` Es el servidor dns en este caso la IP
	`-d` Nombre del dominio
	`-c` Es para poner los collectors, que seria la informacion que vamos a recolectar. Lo normal es poner all para recaudar la mayor informacion posible

