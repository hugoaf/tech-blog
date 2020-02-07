+++
title = "Cisco CLI Access modes and prompts"
description = "What are the different prompts of Cisco IOS"
date = 2020-01-09T19:00:00Z
author = "Hugo Avila"
+++


## User mode (EXEC)

Automatically enters in user mode when device (router or switch) boot is done.

```
  Router>
```
User mode gives access to only basic monitoring commands.


## Privileged user exec mode

From the user mode `>`, enter privileged exec user mode typing `enable` command. `disable` will send you back to user mode.

```
  Router> enable
  Router#
```
Note the `#`, privileged user gives you access to all router commands.
Be aware that `enable` could be password protected.


## Global Configuration Mode

Enter *Global configuration mode* by typing:

```
  Router# configure terminal
  Router(config)#
```
Global configuration mode will give you access to configure global router parameters, commands that will affect the entire system. Like changing the running-config.


## Specific Configuration Modes

Specific configuration modes allow access to sub-commands each to the specific interfaces or proccesses. Next an example of how to enter `interface`, `router` and `line`.

```
  Router(config)# interface GigabitEthernet 0/1
  Router(config-if)#
  Router(config-if)# exit
  Router(config)# router rip
  Router(config-router)#
  Router(config-router)# exit
  Router(config)# line console 0
  Router(config-line)#
```

  If `router rip` trows an `IP routing not enabled` then it should activated first with `#(config)ip routing`

## Setup Mode (bootloader)

```
Router:
```

Will enter in setup mode if a startup-config is not found in NVRAM. Or manually enter setup mode by typing setup from config.
```
Router(config)# setup
Router:
```
