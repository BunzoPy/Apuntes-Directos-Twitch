# SSH con encriptación antigua

```bash
ssh -o PubkeyAcceptedKeyTypes=+ssh-rsa -o HostKeyAlgorithms=+ssh-rsa -i Archivo_RSA Usuario@Ip
Ejemplo: ssh -o PubkeyAcceptedKeyTypes=+ssh-rsa -o HostKeyAlgorithms=+ssh-rsa -i id_rsa hype@10.10.10.79
```
*Parametros*
`-o PubkeyAcceptedKeyTypes=+ssh-rsa` Le dice al cliente que acepte claves publicas tipo ssh-rsa, que ya estan obsoletas y no se usan
`-o HostKeyAlgorithms=+ssh-rsa` Permite aceptar claves del tipo ssh-rsa
`-i` Especifica el archivo que contiene la clave RSA



![[Valentine14.png]]

