#docker #dockerexec

---

Esto nos puede servir para [[Binario docker]]
Sirve para abrir un proceso nuevo dentro de un contenedor ya en ejecucion

---
# Comando para abrir una consola en el contenedor

```
Ejemplo: sudo docker exec -it --privileged --user root e6ff5b1cbc85cdb2157879161e42a08c1062da655f5a6b7e24488342339d4b81 bash
```
*Parametros:*
	`sudo` Ejecuta el coamdno con permisos de sudo
	`-it` Crean una sesion intactiva usable con bash o otra shell
		`-i` Es de interactivo. Permite que la shell (o cualquier comando que ejecutes dentro del contenedor) reciba entrada del teclado mientras la sesión está activa.
		`-t` Da formato de terminal
	`--privileged` Otorga capacidades adicionales del kernel al proceso dentro del contenedor: acceso a todos los dispositivos, levantamiento de restricciones de AppArmor/SELinux, etc. Esto elimina buena parte del aislamiento normal de Docker.
	`--user root` Es para iniciar la sesion como root
	`bash` es el comando que se va a ejecutar dentro del contenedor 