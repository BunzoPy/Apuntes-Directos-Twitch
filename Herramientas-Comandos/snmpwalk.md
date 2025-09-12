#snmpwalk

-----

SNMP (Protocolo Simple de Administración de Red) es un protocolo de capa de aplicación estándar que permite la monitorización y gestión de dispositivos de red como routers, switches y servidores==
Es parecido a [[snmpget]], nada mas que [[snmpget]] dumpea solo los datos del OID que le pasemos, encambio [[snmpwalk]] dumpea todos 

----
# Como instalar

```shell
sudo apt install snmp 
sudo apt install snmp-mibs-downloader
```

----------
# Como usarlo

```shell
snmpwalk -Version -c ComunityString Ip 
Ejemplo: snmpwalk -v1 -c public 10.10.11.107
```

*Parametros:*
	`-Version` Es la version del smnp, que lo podemos ver con NMAP
	`-c`“community string” (es como una **contraseña de solo lectura**). Por defecto en muchos dispositivos inseguros es `"public"`.
	`Ip` Ip de la victima victima

-----
# Notas
ObjectIdentifier este es el **OID** (Object Identifier), que apunta a una variable concreta dentro del árbol MIB del dispositivo