
**Hashcat** es una herramienta avanzada para **crackear hashes de contraseñas**. Es muy rápida y soporta ataques usando CPU y GPU. Es ampliamente usada en ciberseguridad para pruebas de fuerza bruta, diccionario, combinaciones, y ataques híbridos sobre hashes como:

----------

```shell
hashcat -a Mododeataque -m Tipodehash Archivo/TextoACrackear Diccionario
Ejemplo para crackear contraseña en MD5: hashcat -a 0 -m 20 hash /usr/share/wordlists/rockyou.txt
```
*Parametros*
`-a` Modo de ataque
`-m` Tipo de hash (En este caso es 20, por que es md5(pass.salt))


![[Writeup7.png]]
Resultado:

```
62def4866937f08cc13bab43bb14e6f7:5a599ef579066807:raykayjay9
```


- Contraseña: `raykayjay9`



# Tips para identificar hashes manualmente y pagina web para hacerlo automatico
## Identificación de hashes
[Pagina hashes.com para identificar el tipo de hash](https://hashes.com/en/tools/hash_identifier)

| Tipo de Hash | Longitud | Ejemplo                                               |
| ------------ | -------- | ----------------------------------------------------- |
| **MD5**      | 32       | `5f4dcc3b5aa765d61d8327deb882cf99`                    |
| **SHA-1**    | 40       | `5baa61e4c9b93f3f0682250b6cf8331b7ee68fd8`            |
| **SHA-256**  | 64       | `5e884898da28047151d0e56f8dc6292773603d0d6aabbdd7...` |
| **SHA-512**  | 128      | `cf83e1357eefb8bd...`                                 |
| **NTLM**     | 32       | `32ed87bdb5fdc5e9cba88547376818d4`                    |
| **bcrypt**   | 60       | `$2y$10$e0NRW5B8...`                                  |
| **LM hash**  | 32       | Solo letras mayúsculas y números                      |

# 📂 Ubicacion donde se guardan hashes crackeados

Buscar archivo:

```bash
find / -name "hashcat.potfile" 2>/dev/null
```

Ver contenido:

```bash
cat /root/.local/share/hashcat/hashcat.potfile