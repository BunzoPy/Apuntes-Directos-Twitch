**NTLM** significa **NT LAN Manager**.
Es un **protocolo de autenticación** desarrollado por Microsoft para sistemas Windows, y se utiliza para autenticar usuarios y proteger datos en redes locales.

-----
### 📌 ¿Qué hace NTLM?

NTLM permite que un cliente (por ejemplo, una computadora con Windows) se autentique ante un servidor o servicio sin enviar su contraseña en texto plano. Utiliza un sistema de **desafío-respuesta (challenge-response)** con hashes para verificar la identidad del usuario.

---
### 🔒 ¿Cómo funciona a grandes rasgos?

1. El cliente solicita autenticarse.
2. El servidor responde con un _challenge_ (un valor aleatorio).
3. El cliente cifra ese challenge con un hash derivado de la contraseña del usuario.
4. El servidor compara el resultado con su propia versión y decide si es válido.
    
---

### ⚠️ Problemas de seguridad
- NTLM es **antiguo y vulnerable** a ataques como:
    - **Pass-the-Hash**
    - **Relay attacks**
    - **Cracking offline con hashdump**
- Microsoft lo ha ido reemplazando por **Kerberos**, que es más seguro.

---
### 🧪 En pentesting
En entornos Windows, es muy común ver NTLM en uso (especialmente en redes con Active Directory). Herramientas como **Responder**, **Impacket**, o **Hashcat** permiten:
- Capturar hashes NTLM
- Reenviarlos (NTLM Relay)
- Crakearlos