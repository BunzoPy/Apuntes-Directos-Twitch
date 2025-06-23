Es una herramienta de la suite **Impacket** que permite ejecutar comandos remotamente en sistemas Windows usando el protocolo SMB. Emula el comportamiento de la herramienta oficial **PsExec** de Sysinternals, creando un servicio temporal para lanzar comandos y obtener una shell remota. Es muy útil en pentesting para controlar máquinas Windows sin acceso físico.


## Como conectarse
```shell
impacket-psexec Usuario@Ip -port Puerto
Ejemplo: impacket-psexec Administrator@10.129.139.81 -port 445
```
*Parametros*
`-port` Es para especificar el puerto

![[Tactics7.png]]
