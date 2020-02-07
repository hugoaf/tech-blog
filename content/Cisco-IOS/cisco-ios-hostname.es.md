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
myNewSwitch(config)#hostname FitstFloorSw
FitstFloorSw(config)#

```

Siempre es conveniente que el nombre indique la ubicación del dispositivo y proyecto (Notar que es sensible a mayusculas), un buen nombre podria ser: `ACME-SecondFloor-SW5-ABC2019`