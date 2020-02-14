+++
title = "Modos de Acceso a Cisco IOS"
description = ""
date = 2020-01-09T19:00:00Z
author = "Hugo Avila"
url = "/cisco-ios/modos-de-acceso/"
+++


## Modo Usuario (EXEC)

El modo usuario da acceso solo a comandos básicos de monitoreo. El dispositivo entrará automáticamente en este modo cuando termina el inicio.

```
  Router>
```

> Nota: si el usuario tiene el nivel de privilegios correspondiente, puede entrar directamente al modo de Usuario Privilegiado, sin pasar por el modo de usuario. Ver [Usuarios Cisco IOS](/cisco-ios-usuarios)


## Modo de Usuario Privilegiado

El usuario privilegiado permite el acceso a todos los comandos del dispositivo.

Desde el modo usuario entrar al modo privilegiado escribiendo el comando `enable`, y escribir `disable` para salir y regresar al modo usuario.

```
Router> enable
Router#
```
Notar el `#`.
El modo privilegiado puede estar protegido con contraseña.


## Modo de Configuración Global

El modo de Configuración Global permite el acceso a parámetros de configuración del dispositivo, son comandos que afectarán todo el sistema, como cambiar el `running-config`.

Entrar al modo de configuración global escribiendo:

```
Router# configure terminal
Router(config)#
```
Salir de este modo escribiendo `exit` o `Ctr+Z`


## Modos de Configuración Específica

El modo de configuración específica permite el acceso a sub-comandos, correspondientes a procesos o interfaces específicas. A continuación un ejemplo de como entrar a configurar la `interface`, `router`, `line` y `access-list`.

```
Router(config)# interface GigabitEthernet 0/1
Router(config-if)#
Router(config-if)# exit
Router(config)# router rip
Router(config-router)#
Router(config-router)# exit
Router(config)# line console 0
Router(config-line)#
Router(config-line)# exit
Router(config)# ip access-list standard hugo
Router(config-std-nacl)#
```
* `(config-if)` se utiliza para configurar las interfaces.
* `(config-router)` se utiliza para configurar los protocolos de ruteo. 
* `(config-line)` se utiliza para configurar los password de modo usuario. 
* `(config-std-nacl)` se utiliza para configurar las listas de acceso.

> Nota: Si `router rip` arroja un error como: `IP routing not enabled`, entonces debe ser activado previamente con `#(config)ip routing`.



## Modo de Setup (bootloader)

El modo Setup se utiliza para configurar el dispositivo (generalmente de inicio) por medio de un proceso interactivo. El dispositivo entrará en este modo si el archivo `startup-config` no se encuentra en la NVRAM. 

```
Router:
```

En algunos dispositivos se puede forzar el modo Setup presionando el botón *Setup* durante el inicio.
