En esta maquina vemos [[rsync]]

-----
1)¿Cuál es el puerto por defecto de rsync?
	873

2)¿Cuántos puertos TCP están abiertos en el host remoto?
	1

3)¿Cuál es la versión del protocolo utilizado por rsync en la máquina remota?
	31

4)¿Cuál es el nombre de comando más común en Linux para interactuar con rsync?
	rsync

5)¿Qué credenciales debe pasar a rsync para utilizar la autenticación anónima? anonymous:anonymous, anonymous, None, rsync:rsync
	none
	Viene de no pasar ninguna credencial para el logeo, no es que se pone none

6)¿Cuál es la opción para listar sólo recursos compartidos y archivos en rsync? (No es necesario incluir los caracteres -- iniciales)
	list-only
	Cuando usás `--list-only` (o solo `list-only` en este caso), `rsync` muestra los archivos y carpetas disponibles **sin descargarlos**, útil para explorar lo que hay en un recurso remoto.

---

# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.187.112    
nmap -p- --open -sS -vvv --min-rate 5000 -n -Pn 10.129.187.112 -oG allports
extractPorts allports       
nmap -sCV -p873 10.129.187.112 -oN target
```

![[Synced1.png]]

![[Synced3.png]]

![[Synced4.png]]

![[Synced2.png]]

Por el ttl sabemos que es una maquina linux, y que esta el puerto 873 abierto corriendo el servicio rsync



# Instrusion a la maquina por [[rsync]]
```shell
rsync rsync://10.129.187.112:873
rsync rsync://10.129.187.112:873/public
rsync rsync://10.129.187.112:873/public/flag.txt .
```
Nos conectamos por la herramienta rsyc a la maquina y viendo el contenido de la carpeta public, vemos el archivo flag.txt, asi que lo descargamos en nuestro escritorio y lo cateamos

![[Synced5.png]]