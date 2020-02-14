+++
title = "Conexión a Equipo Cisco"
description = "Como conectarse a equipo Cisco"
date = 2020-02-12T18:40:00Z
author = "Hugo Avila"
+++

## Requerimientos 

**Cable Serial**

* DB-9 a RJ45.
* USB Mini-B a USB-A.


**Software emulador de Terminal**

* [Putty](https://www.putty.org/) (Recomendado)
* Hyperterminal _(Pre-instalado en Windows)_


## Procedimiento

Se requiere conectar un cable serial entre la computadora y el puerto de consola del dispositivo, este puerto serial puede ser interface RJ-45 o USB Mini-B. En la computadora se debe determinar cual es el número de puerto asignado y realizar una conexión serial utilizando un programa emulador de terminal como Putty. 

### Tipo de Cable 

**A) Cable Serial DB-9**

Si se cuenta con un puerto serial fijo en la computadora, no se requiere mas configuración. Sin embargo la mayoría de las computadoras no cuentan con un puerto serial en DB-9 hembra, por lo que se debe emplear un dispositivo convertidor serial de USB a DB-9 y contar con el software controlador correspondiente.

**B) Cable USB.**

Para la opción de USB A a USB Mini-B, se requiere instalar el controlador, [obtener controlador USB de pagina de descarga de Cisco](https://software.cisco.com/download/home/282774228/type/282855122/release/3.1).


### Número de Puerto.

**Windows**

Si no se cuenta con un puerto serial fijo, se conectará por USB y el sistema operativo asignará un número de puerto serial. Para obtener el número de puerto, en Windows abrir el *Explorador de Archivos* dar click derecho al icono de la computadora y seleccionar *Administrar*, después al lado izquierdo seleccionar la opción de *Administrador de Dispositivos.* Buscar el dispositivo **CiscoUsbConsoleWindowsDriver** y 

**En MAC (Pendiente)**


### Configuración Serial

| Parámetro  |Valor   |
|----------- |:-------|
| Velocidad  | 1900   |
| Paridad    | None   |
| Bits       | 8      |
