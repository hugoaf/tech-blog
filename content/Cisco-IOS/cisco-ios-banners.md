+++
title = "Banners en Cisco IOS"
description = ""
date = 2020-02-17T14:00:00Z
author = "Hugo Avila"
+++


## Tipos de Banners

Los Banners son textos informativos que se muestran al usuario al realizar ciertas acciones, los principales son:

* [Banner de Mensaje del Dia (MOTD)](#mensaje-del-dia-motd)
* [Banner de Login](#banner-de-login)
* [Banner de Exec Process](#banner-de-exec-process)



## Mensaje del dia (MOTD)

Este mensaje aparecerá para todos los usuarios que se conecten al router, por cualquier medio.

Para grabar el mensaje del día, entrar a modo privilegiado y escribir el comando `banner motd #` donde el `#` marca el inicio del texto, posteriormente se escribe o pega el texto completo incluyendo saltos de linea, al finalizar poner otro `#` para terminar el comando.

> Nota: MOTD son las siglas en ingles de "Message Of The Day".


```console
Sw(config)#banner motd #
Enter TEXT message.  End with the character '#'
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!! MOTD !!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!  RESTRICTED ACCESS  !!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
#
```
Si no se utilizan saltos de linea, se puede introducir el mensaje en un solo renglón, como: `Sw(config)#banner motd x MOTD: Restricted Access! x `

> Es importante anotar que el caracter delimitador no es reservado, se puede utilizar cualquier otro, como el siguiente ejemplo donde se ha utilizado `!` como caracter delimitador: `Sw(config)#banner motd ! This is the message of the day: Restricted Access. !`


Para probar el banner MOTD, salir del modo configuración y del usuario actual escribiendo `exit`, al estar completamente fuera del equipo aparecerá el mensaje del día (MOTD):

```console
Press RETURN to get started.


!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!! MOTD !!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!  RESTRICTED ACCESS  !!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

```
Para **deshabilitar** un Banner de Exec utilizar:

```
Sw(config)#no banner motd

```

## Banner de Login

Aparecerá antes del acceso del usuario e inmediatamente después del MOTD. Para establecer el banner escribir el comando `banner login #` en modo privilegiado, posteriormente introducir el texto y al finalizar poner otro `#` para marcar el final.

```console
Sw(config)#banner login #
Enter TEXT message.  End with the character '#'
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!! LOGIN BANNER !!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!  BE CAREFUL  !!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
#
Sw(config)#
```

Para probar el Banner de login, sera necesario salir del usuario actual, ejemplo:

```console
Press RETURN to get started.



!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!! MOTD !!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!  RESTRICTED ACCESS  !!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!! LOGIN BANNER !!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!  BE CAREFUL  !!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!


User Access Verification

Username:

```

Para **deshabilitar** un Banner de Login utilizar:

```
Sw(config)#no banner login

```


## Banner de Exec Process

Este se debe mostrar cuando el usuario entra al router en modo EXEC.

```
Sw(config)#banner exec #
Enter TEXT message.  End with the character '#'
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!! EXEC BANNER !!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!! YOU ARE IN  !!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
#
Sw(config)#

```

Para probarlo solo salir del usuario y acceder nuevamente:

```console
User Access Verification

Username: hugo
Password:
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!! EXEC BANNER !!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!! YOU ARE IN  !!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

Sw#

``` 

Para **deshabilitar** un Banner de Exec utilizar:

```
Sw(config)#no banner exec

```


>>> Pro Tip: Es mas sencillo crear el contenido del banner en un editor de texto como notepad o notepad++ y solo pegarlo en la consola.