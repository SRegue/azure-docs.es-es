---
title: Configuración de alta disponibilidad con STONITH para SAP HANA en Azure (instancias grandes) | Microsoft Docs
description: Vea cómo establecer la alta disponibilidad para SAP HANA en Azure (instancias grandes) en SUSE mediante el dispositivo STONITH.
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/21/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2c387658b163cdbaf3d318ed03a53205e8a286ba
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112455255"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith-device"></a>Configuración de la alta disponibilidad en SUSE mediante el dispositivo STONITH

Este artículo proporciona instrucciones paso a paso para configurar la alta disponibilidad de HANA (instancias grandes) en el sistema operativo SUSE mediante el dispositivo STONITH.

**Declinación de responsabilidades:** *Esta guía se obtiene tras probar la configuración satisfactoriamente en el entorno de HANA (instancias grandes) de Microsoft. El equipo de Microsoft Service Management para HANA (instancias grandes) no ofrece soporte técnico para el sistema operativo, por lo que deberá ponerse en contacto con SUSE para la solución de cualquier problema o aclaración posterior de la capa del sistema operativo. El equipo de Microsoft Service Management sí configura el dispositivo STONITH, para el que ofrece soporte técnico completo y asistencia para la solución de problemas.*

## <a name="pre-requisites"></a>Requisitos previos

Para configurar la alta disponibilidad (HA) usando clústeres SUSE, deben cumplirse los siguientes requisitos previos.

- Aprovisionamiento de las instancias grandes de HANA
- Sistema operativo (OS) registrado
- Servidores de instancias grandes de HANA conectados al servidor SMT para obtener las revisiones/los paquetes
- Revisiones más recientes del sistema operativo instaladas
- Protocolo NTP (Network Time Protocol) configurado
- Haber leído y comprendido la versión más reciente de la documentación de SUSE sobre la configuración de la alta disponibilidad

## <a name="setup-details"></a>Detalles de la configuración
Esta guía utiliza la siguiente configuración:
- Sistema operativo: SLES 12 SP1 para SAP
- Instancias grandes de HANA: 2xS192 (cuatro sockets, 2 TB)
- Versión de HANA: HANA 2.0 SP1
- Nombres de servidor: sapprdhdb95 (nodo 1) y sapprdhdb96 (nodo 2)
- Dispositivo STONITH: dispositivo STONITH basado en iSCSI
- Configuración de NTP en uno de los nodos de instancia grande de HANA

Cuando configure HANA (instancias grandes) con la replicación del sistema de HANA (HSR), puede solicitar al equipo de Microsoft Service Management que configure el dispositivo STONITH. Debe hacer esto justo en el momento del aprovisionamiento. 

Si ya es un cliente con HANA (instancias grandes) aprovisionado, también puede solicitar la configuración del dispositivo STONITH. Proporcione la siguiente información al equipo de Microsoft Service Management en el formulario de solicitud de servicio (SRF). Puede solicitar el formulario SRF a través del director técnico de su cuenta o de su contacto de Microsoft para la incorporación de HANA (instancias grandes).

- Nombre y dirección IP del servidor (por ejemplo, myhanaserver1, 10.35.0.1)
- Ubicación (por ejemplo, Este de EE. UU.)
- Nombre del cliente (por ejemplo, Microsoft)
- SID: identificador del sistema HANA (por ejemplo, H11)

Una vez configurado el dispositivo STONITH, el equipo de Microsoft Service Management le proporcionará el nombre del dispositivo de bloques STONITH (SBD) y la dirección IP del almacenamiento iSCSI. Puede usar esta información para la configuración de STONITH. 

Siga estos pasos para configurar la alta disponibilidad con STONITH:

1.  Identificación del dispositivo SBD.
2.  Inicialización del dispositivo SBD.
3.  Configuración del clúster.
4.  Configuración del guardián Softdog.
5.  Unión del nodo al clúster.
6.  Validación del clúster.
7.  Configuración de los recursos en el clúster.
8.  Prueba del proceso de conmutación por error.

## <a name="1---identify-the-sbd-device"></a>1.   Identificación del dispositivo SBD
En esta sección, se explica cómo determinar el dispositivo SBD para la configuración después de que el equipo de Microsoft Service Management haya configurado el dispositivo STONITH. **Esta sección se aplica únicamente a clientes actuales**. Si es un cliente nuevo, el equipo de Microsoft Service Management le dará el nombre del dispositivo SBD, así que puede omitir esta sección.

1.1 Cambie */etc/iscsi/initiatorname.isci* por 

``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Microsoft Service Management proporciona esta cadena. Modifique el archivo en **ambos** nodos, aunque el número de nodo sea distinto en cada uno.

![Captura de pantalla que muestra el archivo initiatorname con los valores de InitiatorName de un nodo.](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2 Cambie */etc/iscsi/iscsid.conf*: establezca *node.session.timeo.replacement_timeout=5* y *node.startup = automatic*. Modifique el archivo en **ambos** nodos.

1.3 Ejecute el comando discovery. Muestra cuatro sesiones. Ejecútelo en ambos nodos.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![Captura de pantalla que muestra una ventana de consola con los resultados del comando isciadm con la opción discovery.](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4 Ejecute el comando para iniciar sesión en el dispositivo iSCSI. Muestra cuatro sesiones. Ejecútelo en **ambos** nodos.

```
iscsiadm -m node -l
```
![Captura de pantalla que muestra una ventana de consola con los resultados del comando isciadm con la opción node.](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5 Ejecute el script para volver a examinar: *rescan-scsi-bus.sh*. Este script muestra los nuevos discos creados automáticamente.  Ejecútelo en ambos nodos. Debería ver un número LUN superior a cero (por ejemplo: 1, 2, etc.).

```
rescan-scsi-bus.sh
```
![Captura de pantalla que muestra una ventana de consola con los resultados del script.](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1.6 Para obtener el nombre de dispositivo, ejecute el comando *fdisk –l*. Ejecútelo en ambos nodos. Elija el dispositivo con el tamaño de **178MiB**.

```
  fdisk –l
```

![Captura de pantalla que muestra una ventana de consola con los resultados del comando f disk.](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2.   Inicialización del dispositivo SBD

2.1 Inicialice el dispositivo SBD en **ambos** nodos.

```
sbd -d <SBD Device Name> create
```
![Captura de pantalla que muestra una ventana de consola con los resultados del comando s b d create.](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2 Compruebe lo que se ha escrito en el dispositivo. Hágalo en **ambos** nodos.

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3. Configuración del clúster
En esta sección se describen los pasos para configurar el clúster de alta disponibilidad de SUSE.

### <a name="31-package-installation"></a>3.1 Instalación del paquete
3.1.1 Compruebe si los patrones ha_sles y SAPHanaSR-doc están instalados. En caso negativo, instálelos. Instálelos en **ambos** nodos.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![Captura de pantalla que muestra una ventana de consola con el resultado del comando pattern.](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![Captura de pantalla que muestra una ventana de consola con el resultado del comando SAPHanaSR-doc.](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2 Configuración del clúster
3.2.1 Puede usar el comando *ha-cluster-init* o usar el asistente yast2 para configurar el clúster. En este caso, se utiliza el asistente de yast2. Realice este paso **solamente en el nodo principal**.

Siga yast2> Alta disponibilidad > Clúster

![Captura de pantalla que muestra el centro de control de YaST con las opciones Alta disponibilidad y Clúster seleccionadas.](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

![Captura de pantalla que muestra un cuadro de diálogo con las opciones Instalar y Cancelar.](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Seleccione **Cancel** (Cancelar) porque el paquete halk2 ya está instalado.

![Captura de pantalla que muestra un mensaje sobre la opción de cancelación.](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Seleccione **Continuar**.

Valor esperado: número de nodos implementados (en este caso, 2).

![La captura de pantalla muestra la opción Cluster Security (Seguridad del clúster) con la casilla de verificación Enable Security Auth (Habilitar autenticación de seguridad) activada.](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png)

Seleccione **Next** (Siguiente).

![Captura de pantalla que muestra la ventana Cluster Configure (Configuración del clúster) con las listas Sync Host (Host de sincronización) y Sync File (Archivo de sincronización).](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png)

Agregue los nombres de nodo y, a continuación, seleccione Add suggested files (Agregar archivos sugeridos).

Seleccione **Turn csync2 ON** (Activar csync2).

Si selecciona **Generate Pre-Shared-Keys** (Generar claves compartidas previamente), se muestra el siguiente mensaje emergente.

![Captura de pantalla que muestra un mensaje que indica que se ha generado la clave.](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Seleccione **Aceptar**.

La autenticación se realiza con las direcciones IP y las claves compartidas previamente en csync 2. El archivo de clave se genera con csync2 -k /etc/csync2/key_hagroup. El archivo key_hagroup debe copiarse en todos los miembros del clúster manualmente después de su creación. **Asegúrese de copiar el archivo del nodo 1 en el nodo 2**.

![Captura de pantalla que muestra un cuadro de diálogo de configuración del clúster con las opciones necesarias para copiar la clave a todos los miembros del clúster.](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Seleccione **Siguiente**.
![Captura de pantalla que muestra la ventana del servicio de clúster.](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

En la opción predeterminada, el arranque está desactivado. Actívelo para que se inicie Pacemaker en el arranque. Puede elegir según los requisitos de configuración.

Seleccione **Siguiente** para completar la configuración del clúster.

## <a name="4---setting-up-the-softdog-watchdog"></a>4. Configuración del guardián Softdog
En esta sección se describe la configuración del guardián (Softdog).

4.1 Agregue la siguiente línea a */etc/init.d/boot.local* en **ambos** nodos.

```
modprobe softdog
```
![Captura de pantalla que muestra un archivo de arranque con la línea de softdog agregada.](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2 Actualice el archivo */etc/sysconfig/sbd* en **ambos** nodos de la siguiente manera:

```
SBD_DEVICE="<SBD Device Name>"
```
![Captura de pantalla que muestra el archivo s b d con el valor de S B D_DEVICE agregado.](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 Cargue el módulo de kernel en **ambos** nodos ejecutando el siguiente comando:

```
modprobe softdog
```
![Captura de pantalla que muestra parte de una ventana de consola con el comando modprobe softdog.](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4.4 Asegúrese de que Softdog se esté ejecutando de la siguiente manera en **ambos** nodos:

```
lsmod | grep dog
```
![Captura de pantalla que muestra parte de una ventana de consola con el resultado de la ejecución del comando l s mod.](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5 Inicie el dispositivo SBD en **ambos** nodos:

```
/usr/share/sbd/sbd.sh start
```
![Captura de pantalla que muestra parte de una ventana de consola con el resultado de la ejecución del comando start.](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 Pruebe el demonio SBD en **ambos** nodos. Verá dos entradas después de configurarlo en ambos nodos.

```
sbd -d <SBD Device Name> list
```
![Captura de pantalla que muestra parte de una ventana de consola que muestra dos entradas.](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7 Envíe un mensaje de prueba a **uno** de los nodos.

```
sbd  -d <SBD Device Name> message <node2> <message>
```
![Captura de pantalla que muestra parte de una ventana de consola que muestra dos entradas.](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8 En el **segundo** nodo (nodo 2), puede comprobar el estado del mensaje.

```
sbd  -d <SBD Device Name> list
```
![Captura de pantalla que muestra parte de una ventana de consola con uno de los miembros que muestra un valor de prueba para el otro miembro.](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9 Para adoptar la configuración de SBD, actualice el archivo */etc/sysconfig/sbd* de la siguiente forma. Actualice el archivo en **ambos** nodos.

```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10 Inicie el servicio Pacemaker en el **nodo principal** (nodo 1).

```
systemctl start pacemaker
```
![Captura de pantalla que muestra una ventana de consola que muestra el estado después de iniciar pacemaker.](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Si se produce un *error* en el servicio Pacemaker, vea *Escenario 5: Error del servicio Pacemaker*.

## <a name="5---joining-the-cluster"></a>5.   Unión al clúster
En esta sección, se explica cómo unir el nodo al clúster.

### <a name="51-add-the-node"></a>5.1 Adición del nodo
Ejecute el siguiente comando en el **nodo 2** para que dicho nodo se una al clúster.

```
ha-cluster-join
```
Si recibe un *error* durante la unión al clúster, vea *Escenario 6: El nodo 2 no puede unirse al clúster*.

## <a name="6---validating-the-cluster"></a>6.   Validación del clúster

### <a name="61-start-the-cluster-service"></a>6.1 Inicio del servicio de clúster
Para comprobar e iniciar opcionalmente el clúster por primera vez en **ambos** nodos.

```
systemctl status pacemaker
systemctl start pacemaker
```
![Captura de pantalla que muestra una ventana de consola con el estado de pacemaker.](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)

### <a name="62-monitor-the-status"></a>6.2 Supervisión del estado

Ejecute el comando *crm_mon* para asegurarse de que **ambos** nodos están en línea. También puede ejecutarlo en **cualquiera de los nodos** del clúster.

```
crm_mon
```
![Captura de pantalla que muestra una ventana de consola con los resultados de c r m_mon.](media/HowToHLI/HASetupWithStonith/crm-mon.png)

También puede iniciar sesión en hawk para comprobar el estado del clúster *https://\<node IP>:7630*. El usuario predeterminado es hacluster y la contraseña es linux. Si es necesario, puede cambiar la contraseña con el comando *passwd*.

## <a name="7-configure-cluster-properties-and-resources"></a>7. Configuración de las propiedades y los recursos del clúster

Esta sección describe los pasos para configurar los recursos del clúster.
En este ejemplo, configure los siguientes recursos. El resto se puede configurar (si es necesario) tomando como referencia la guía de alta disponibilidad de SUSE. Realice la configuración en **uno de los nodos** solamente. Hágalo en el nodo principal.

- Arranque del clúster
- Dispositivo STONITH
- Dirección IP virtual

### <a name="71-cluster-bootstrap-and-more"></a>7.1 Arranque del clúster y mucho más
Agregue el arranque del clúster. Cree el archivo y agregue el texto de la siguiente forma:

```
sapprdhdb95:~ # vi crm-bs.txt
# enter the following to crm-bs.txt
property $id="cib-bootstrap-options" \
no-quorum-policy="ignore" \
stonith-enabled="true" \
stonith-action="reboot" \
stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
resource-stickiness="1000" \
migration-threshold="5000"
op_defaults $id="op-options" \
timeout="600"
```
Agregue la configuración al clúster.

```
crm configure load update crm-bs.txt
```
![Captura de pantalla que muestra parte de una ventana de consola con la ejecución del comando c r m.](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7.2 Dispositivo STONITH
Agregue el recurso STONITH. Cree el archivo y agregue el siguiente texto.

```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15"
```
Agregue la configuración al clúster.

```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7.3 Dirección IP virtual
Agregue la dirección IP virtual de los recursos. Cree el archivo y agregue el texto como aparece a continuación.

```
# vi crm-vip.txt
primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
operations $id="rsc_ip_HA1_HDB10-operations" \
op monitor interval="10s" timeout="20s" \
params ip="10.35.0.197"
```
Agregue la configuración al clúster.

```
crm configure load update crm-vip.txt
```

### <a name="74-validate-the-resources"></a>7.4 Validación de los recursos

Cuando ejecuta el comando *crm_mon*, puede ver los dos recursos.
![Captura de pantalla que muestra una ventana de consola con dos recursos.](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

También puede ver el estado en *https://\<node IP address>:7630/cib/live/state*.

![Captura de pantalla que muestra el estado de los dos recursos.](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. Prueba del proceso de conmutación por error
Para realizar la prueba del proceso de conmutación por error, detenga el servicio Pacemaker en el nodo 1 y la conmutación por error de recursos en el nodo 2.

```
Service pacemaker stop
```
Ahora, detenga el servicio Pacemaker en el **nodo 2**. Los recursos conmutan por error al **nodo 1**.

**Antes de la conmutación por error**  
![Captura de pantalla que muestra el estado de los dos recursos antes de la conmutación por error.](media/HowToHLI/HASetupWithStonith/Before-failover.png)  

**Después de la conmutación por error**  
![Captura de pantalla que muestra el estado de los dos recursos después de la conmutación por error.](media/HowToHLI/HASetupWithStonith/after-failover.png)

![Captura de pantalla que muestra una ventana de consola con el estado de los recursos después de la conmutación por error.](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  


## <a name="9-troubleshooting"></a>9. Solución de problemas
En esta sección, se describen algunos escenarios de error que pueden producirse durante la configuración. No tendrá que enfrentarse necesariamente a estos problemas.

### <a name="scenario-1-cluster-node-not-online"></a>Escenario 1: Nodo de clúster no en línea
Si alguno de los nodos no se muestra en línea en el administrador de clústeres, puede intentar lo siguiente para ponerlo en línea.

Inicie el servicio iSCSI:

```
service iscsid start
```

Ahora puede iniciar sesión en ese nodo iSCSI.

```
iscsiadm -m node -l
```
La salida esperada tiene el siguiente aspecto:

```
sapprdhdb45:~ # iscsiadm -m node -l
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
```
### <a name="scenario-2-yast2-doesnt-show-graphical-view"></a>Escenario 2: yast2 no muestra la vista gráfica

En este documento, se usa la pantalla gráfica de yast2 para configurar el clúster de alta disponibilidad. Si yast2 no se abre con la ventana gráfica como se muestra y devuelve un error Qt, siga estos pasos. Si se abre con la ventana gráfica, puede omitir los pasos.

**Error**

![Captura de pantalla que muestra parte de una ventana de consola con un mensaje de error.](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Resultado esperado**

![Captura de pantalla que muestra el centro de control de YaST con la opción de alta disponibilidad y el clúster resaltados.](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Si yast2 no se abre con la vista gráfica, siga estos pasos:

Instale los paquetes requeridos. Debe iniciar sesión como usuario "raíz" y contar con la configuración SMT para descargar e instalar los paquetes.

Para instalar los paquetes, use yast>Software>Software Management (Administración de software)>Dependencies (Dependencias)> opción "Install recommended packages…" (Instalar paquetes recomendados). La siguiente captura de pantalla muestra las pantallas esperadas.

>[!NOTE]
>Debe realizar los pasos en ambos nodos para poder acceder a la vista gráfica de yast2 desde los dos nodos.

![Captura de pantalla que muestra una ventana de consola que muestra el centro de control de YaST.](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

En Dependencias, seleccione “Instalar paquetes recomendados”.

![La captura de pantalla muestra una ventana de consola con la opción Instalar paquetes recomendados seleccionada.](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Revise los cambios y seleccione **Aceptar**.

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

La instalación de paquetes continúa.

![Captura de pantalla de una ventana de consola que muestra el progreso de la instalación.](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Seleccione **Siguiente**.

![Captura de pantalla que muestra una ventana de consola con un mensaje de operación correcta.](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Seleccione **Finalizar**.

También tiene que instalar los paquetes libqt4 y libyui-qt.

```
zypper -n install libqt4
```
![Captura de pantalla que muestra una ventana de la consola que instala el paquete libqt4.](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)

```
zypper -n install libyui-qt
```
![Captura de pantalla que muestra una ventana de consola que instala el paquete libyui-qt.](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)

![Captura de pantalla que muestra una ventana de consola que instala el paquete libyui-qt (continuación).](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png)

Ahora Yast2 puede abrir la vista gráfica, como se muestra aquí.

![Captura de pantalla que muestra el centro de control de YaST con las opciones de software y la actualización en línea seleccionadas.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-doesnt-show-the-high-availability-option"></a>Escenario 3: yast2 no muestra la opción de alta disponibilidad

Para que la opción de alta disponibilidad esté visible en el centro de control de yast2, debe instalar los otros paquetes.

Utilice Yast2>Software>Software management (Administración de software) para seleccionar los siguientes patrones:

- Base del servidor SAP HANA
- Compilador C/C++ y herramientas
- Alta disponibilidad
- Base de SAP Application Server

La siguiente pantalla muestra los pasos para instalar los patrones.

Uso de yast2 > Software > Software Management (Administración de software)

![Captura de pantalla que muestra el centro de control de YaST con las opciones de software y la actualización en línea seleccionadas para comenzar la instalación.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Seleccione los patrones.

![Captura de pantalla que muestra la selección del primer patrón en el elemento C/C++ Compiler and Tools (Compilador y herramientas de C/C++).](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)

![Captura de pantalla que muestra la selección del segundo patrón en el elemento C/C++ Compiler and Tools (Compilador y herramientas de C/C++).](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Seleccione **Aceptar**.

![Captura de pantalla que muestra el cuadro de diálogo Changed Packages (Paquetes modificados) con los paquetes modificados para resolver las dependencias.](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Seleccione **Continuar**.

![Captura de pantalla que muestra la página estado de la instalación en ejecución.](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Cuando la instalación finalice, seleccione **Siguiente**.

![Captura de pantalla que muestra el informe de instalación.](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Escenario 4: HANA no se instala debido a un error en los ensamblados de GCC
HANA no se instala debido al siguiente error.

![Captura de pantalla que muestra un mensaje de error que indica que el sistema operativo no está preparado para realizar ensamblados de GCC 5.](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Para corregir el problema, tiene que instalar bibliotecas (libgcc_sl y libstdc++6) de la siguiente forma.

![Captura de pantalla que muestra una ventana de la consola que instala las bibliotecas necesarias.](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Escenario 5: Error del servicio Pacemaker

Se ha producido el siguiente error durante el arranque del servicio Pacemaker.

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: https://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

Para corregirlo, elimine la siguiente línea del archivo */usr/lib/systemd/system/fstrim.timer*.

```
Persistent=true
```

![Captura de pantalla que muestra el archivo f s trim con el valor Persistent = true que se va a eliminar.](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Escenario 6: El nodo 2 no puede unirse al clúster

Cuando se une el nodo 2 al clúster con el comando *ha-cluster-join*, se produce el siguiente error.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![Captura de pantalla que muestra una ventana de la consola con un mensaje de error que indica que no se pueden recuperar las claves de S S H de una dirección I P.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Para corregirlo, ejecute el siguiente código en ambos nodos:

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![Captura de pantalla que muestra parte de una ventana de la consola que ejecuta el comando en el primer nodo.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![Captura de pantalla que muestra parte de una ventana de la consola que ejecuta el comando en el segundo nodo.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Después de la corrección anterior, el nodo 2 debe agregarse al clúster.

![Captura de pantalla que muestra una ventana de la consola con una ejecución correcta del comando ha-cluster-join.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="next-steps"></a>Pasos siguientes

Puede encontrar más información sobre la configuración de alta disponibilidad de SUSE en los siguientes artículos: 

- [Escenario de rendimiento optimizado para la replicación del sistema de SAP HANA](https://www.suse.com/support/kb/doc/?id=000019450 )
- [Storage based fencing](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html) (Vallado basado en el almacenamiento)
- [Blog: Uso del clúster de Pacemaker para SAP HANA: parte 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blog: Uso del clúster de Pacemaker para SAP HANA: parte 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)

Vea cómo realizar una copia de seguridad y una restauración a nivel de archivo del sistema operativo.

> [!div class="nextstepaction"]
> [Copia de seguridad y restauración del sistema operativo](large-instance-os-backup.md)
