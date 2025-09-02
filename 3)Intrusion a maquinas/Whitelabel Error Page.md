#whitelabelerrorpage

----

Una "Whitelabel Error Page" o "Página de Error de Marca Blanca" es una página de error genérica y predeterminada que muestra una aplicación, especialmente en Spring Boot, cuando ocurre un problema y no se encuentra un controlador adecuado o una página personalizada para manejar el error

-------
# Enumeracion

Enumerando con [[Gobuster]] usamos el comando `gobuster dir -u http://cozyhosting.htb/ -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -t 64`

![[CozyHosting9.png]]
Encontramos el directorio */error* http://cozyhosting.htb/error

Que si entramos nos da esta este error
![[CozyHosting14.png]]

---
# Explotacion

Y ahora vamos con [[Gobuster]] a usar el diccionario de spring-boot, ya que al ver la pagina con el /error podemos asumir que esta corriendo tecnologia spring-boot `gobuster dir -u http://cozyhosting.htb/ -w /usr/share/SecLists/Discovery/Web-Content/spring-boot.txt -t 64`

![[CozyHosting15.png]]
Nos da el directorio */actuator* http://cozyhosting.htb/actuator

Si entramos, vemos que nos da otros posibles directorios, asi que vamos a ir probando y una vez que entramos al
![[CozyHosting11.png]]
http://cozyhosting.htb/actuator/sessions
![[CozyHosting10.png]]
Nos da lo que serian unas cookies de sesion para el usuario kanderson

*66284B32C890C19F3225FFBA929C57A0	*
*8619210BF73587A8503EF4AD0398883E*	
Usuario: *kanderson*