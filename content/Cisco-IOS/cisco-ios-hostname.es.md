+++
title = "Hostname Cisco IOS"
description = "Como establecer el nombre del dispositivo"
date = 2020-01-30T18:48:00Z
author = "Hugo Avila"
+++


## Estableciendo el nombre

El nombre del dispositivo siempre es mostrado como la primera parte del prompt como `nombre#`. Para establecer el nombre del dispositivo, utilizar el comando `hostname` desde el modo privilegiado.

```
Switch#conf t
Switch(config)#
Switch(config)#hostname myNewSwitch
myNewSwitch(config)#hostname FirstFlorSw
FirstFlorSw(config)#

```



## Requerimientos

* Las condiciones del nombre como caracteres aceptados y longitud varían de acuerdo al dispositivo.
* Generalmente el nombre debe iniciar con una letra y puede contener números y algunos caracteres especiales como `-` y `_`. En algunos dispositivos debe iniciar y terminar en una letra.
* La longitud máxima varia dependiendo el dispositivo, para SW Catalist el máximo es de 20 caracteres.
* Algunos caracteres especiales no son aceptados como ´? *,´\ !"#$%&/()= ´ o espacios en blanco.

## Recomendaciones

* Debido a que el hostname se utiliza en la red para identificar el dispositivo por medio de DNS inverso, es recomendable que el hostname cumpla con las reglas de establecimiento de nombres FQDN (Fully Qualified Domain Name). Ver [RFC-1034](https://www.rfc-es.org/rfc/rfc1034-es.txt).

* Siempre es conveniente que el nombre indique la ubicación y el número del dispositivo (Notar que es sensible a mayúsculas en el Prompt, pero no necesariamente para identificar el dispositivo en la red, ya que para ello se utilizarán solo minúsculas), un buen nombre podría ser: `ACME_2NDF_5`
