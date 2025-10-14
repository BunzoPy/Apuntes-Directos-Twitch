
---
title: "Writeup fawn - Hack The Box - Resolución y Análisis"
published: true
tags:
  - hackthebox
  - writeup
  - fawn
  - ciberseguridad
  - pentesting
description: "Writeup y resolución de la máquina fawn en Hack The Box."
keywords:
  - writeup fawn
  - hack the box fawn
  - resolución máquina fawn
  - fawn hack the box
  - htb fawn
---

### 🔗 Accesos rápidos

- 📄 **Writeup online**: 
- 📺 **Resolución en vivo (completa)**: 
- 🧠 **Explicación resumida**: 

---

#nmap #ping 

-----
# Guided Mode





-----
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

Se puede utilizar la version automatizada que tenemos creada con [[startlab]] o si no de forma manual

```shell
ping -c 1 IP
nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn IP -oG allPortsTCP
extractPorts allPortsTCP
nmap -sCV -pPuertos IP -oN targetTCP
nmap -sU -n -Pn --top-ports 200 IP -oG allPortsUDP
extractPorts allPortsUDP
nmap -sU -sCV -pPuertos IP -oN targetUDP
```


-------




------
# Notas