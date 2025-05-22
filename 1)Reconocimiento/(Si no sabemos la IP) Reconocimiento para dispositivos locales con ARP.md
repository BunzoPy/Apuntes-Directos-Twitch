
# Resumen de comando a usar

```shell
arp-scan --localnet -I InterfazDeRed --ignoredups
```

-------
##### Si estamos montando una VM(Virtual Machine) a nivel local, vamos a necesitar sacar la ip para poder explotar el laboratorio. La forma mas normal de conseguir la ip es con `arp-scan`

En el caso de no saber que interfaz de red tenemos. Se puede usar el comando `ìfconfig`. Te tendria que aparecer algo asi, puede ser que tengas mas interfaces pero no hay problema.
Los nombres mas normales para las interfaces de ethernet o wifi son enp0s3, enp2s0, ens33, eth0, wlp3s0, wlan
![[arp-scan1.png]]

-----------
*Ahora vamos a hacer el escaneo con arp-scan*

```shell
arp-scan --localnet -I InterfazDeRed --ignoredups
Ejemplo: --localnet -I enp0s3 --ignoredups
```

*Parametros*
	`--localnet` es para escanear todo a nivel local
	`-I` es para poner la interface
	`--ignoredups` es para ignorar los resultados duplicados
![[arp-scan2.png]]
Normalmente si estas levantando la vm en el programa Vmware te va a aparecer de nombre algun dispositivo con el nombre de Vmware que va a ser muy distintivo para que lo puedas ubicar, encambio si estas usando Virtualbox, va a aparecer de nombre PCS Systemtechnik GmbH. Esta empresa es propiertaria de Virtualbox y por eso usan su nombre

------
##### Una vez tengamos la ip de nuestra maquina victima. Ya podemos pasar a hacer [[1 - Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]
