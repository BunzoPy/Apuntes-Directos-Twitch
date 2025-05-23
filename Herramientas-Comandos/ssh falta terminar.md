## 🔐 Acceso SSH

```bash
ssh -o PubkeyAcceptedKeyTypes=+ssh-rsa \
    -o HostKeyAlgorithms=+ssh-rsa \
    -i id_rsa hype@10.10.10.79
```
- **Passphrase:** `heartbleedbelievethehype`

![[Valentine14.png]]



TERMINAR, EXPLICAR LA DIFERENCIA DE EL COMANDO CON ENCRIPTACION VIEJA Y POR QUE USAR ESOS PARAMETROS