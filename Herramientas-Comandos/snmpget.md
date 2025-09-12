#snmp

-----

SNMP (Protocolo Simple de Administración de Red) es un protocolo de capa de aplicación estándar que permite la monitorización y gestión de dispositivos de red como routers, switches y servidores==

----
# Como instalar

```shell
sudo apt install snmp 
sudo apt install snmp-mibs-downloader
```

----------
# Como usarlo

```shell
snmpget -Version -c ComunityString Ip ObjectIdentifier
Ejemplo: snmpget -v1 -c public 10.10.11.107 .1.3.6.1.4.1.11.2.3.9.1.1.13.0
```

*Parametros:*
	`-Version` Es la version del smnp, que lo podemos ver con NMAP
	`-c`“community string” (es como una **contraseña de solo lectura**). Por defecto en muchos dispositivos inseguros es `"public"`.
	`Ip` Ip de la victima victima
	`ObjectIdentifier`este es el **OID** (Object Identifier), que apunta a una variable concreta dentro del árbol MIB del dispositivo.
	