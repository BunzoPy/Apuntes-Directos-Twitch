Server Message Block (bloque de mensajes del servidor). Es un **protocolo de red** que permite el intercambio de archivos, impresoras, puertos serie y otros recursos entre computadoras en una red
Funciona por el puerto 445 normalmente, y aparece como microsoft-ds que esto indicaria que esta activo el SMB
El puerto 139 corriendo el servicio netbios-ssn tambien esta vinculado a smb

Si un recurso en `smbclient` **no tiene `$`**, es **visible públicamente** en la red.  
	Si **tiene `$`**, es un **recurso oculto o administrativo**.
	![[Dancing5.png]]

##### Comandos
`get` Sirve para descargar archivos
`ls` Para listar los contenidos de cada carpeta

--------
## Enumerar vulnerabilidad con [[NMAP Scripts]]

```
nmap --script="smb-vuln*"  -pPuerto Ip -oN NombreDelArchivo
Ejemplo:nmap --script="smb-vuln*"  -p445 10.10.10.4 -oN smbScan
```
Va a ejecutar los scripts que tiene nmap para buscar vulnerabilidades en [[smb]]

------
# Posible vulnerabilidades

##### Usuario Administrator
 Por default, podemos conectarnos con el usuario Administrator sin proporcionar ninguna contraseña

##### Usuario guest, aparece en escaneo de nmap
![[Archetype9.png]]
 Si sale esto nos podemos conectar sin brindar usuario ni contraseña
```shell
smbclient -L Ip -p Puerto -N
Ejemplo: smbclient -L 10.129.96.14 -p 445 -N
```

---
# Vulnerabilidades de CVE
[[CVE-2008-4250]]
[[CVE-2017-0143]]

----
### Como listar directorios
```shell
smbclient -L Ip -p Puerto -N
Ejemplo: smbclient -L 10.129.96.14 -p 445 -N
```
*Parametros:*
	`-L` para listar los recursos compartidos
	`-p` Especificar el puerto
	`-N` No solicita contraseña
![[Dancing5.png]]

--------
### Como conectarnos
```shell
smbclient //Ip/Directorio -p Puerto -N
Ejemplo: smbclient //10.129.96.14/WorkShares -p 445 -N
```

![[Dancing7.png]]

-------
# Pipes

✅ **Pipes en Windows**: son canales que permiten comunicarse con servicios del sistema (como usuarios, red o impresoras) a través de SMB.

✅ **lsarpc** es el pipe más completo: permite ver políticas de seguridad, usuarios, grupos, permisos y trusts.

🎯 **En pentesting**, es clave porque da mucha información útil (a veces sin credenciales), ideal para reconocer el sistema y planear ataques.
    

---
