#bloodhound

------

Es una herramienta de **análisis de seguridad para entornos de Active Directory (AD)** que permite **visualizar y explorar las relaciones de confianza entre usuarios, grupos, equipos y permisos** dentro de una red corporativa. Su finalidad principal es **descubrir rutas de escalada de privilegios** que podrían permitir a un atacante (o a un auditor) llegar, por ejemplo, de una cuenta de bajo nivel a **Domain Admin**.


---
# Instalacion

```
apt install bloodhound
```


--------
# Como abrirlo

Para abrirlo usamos el comando `bloodhound & disown`

![[Administrator19.png]]

Despues vamos aponer en upload data y seleccionamos todos los archivos que se crearon con [[bloodhound-python]] previamente

![[Administrator20.png]]
Ya tenemos todos los datos cargados


------

# Como empezar

Ahora vamos a buscar el usuario de olivia, ya que solamente tenemos las credenciales de este usuario
![[Administrator21.png]]

![[Administrator22.png]]
Al usuario de olivia le hago click derecho y apreto en *Mark User as Owned*. Nos va a poner una calaverita para distingir que somos nosotros

