---
title: 'Aprovisionamiento de dispositivos con un TPM virtual en una máquina virtual Linux: Azure IoT Edge'
description: Usar un TPM simulado en una máquina virtual con Linux para probar Device Provisioning Service en Azure para Azure IoT Edge
author: kgremban
ms.author: kgremban
ms.date: 04/09/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d667b2429c7911353df98795f7116d47f8f15d8a
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397433"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-tpm-on-linux"></a>Creación y aprovisionamiento de un dispositivo IoT Edge con un TPM en Linux

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

En este artículo se muestra cómo probar el aprovisionamiento automático en un dispositivo IoT Edge Linux mediante un Módulo de plataforma segura (TPM). Puede aprovisionar automáticamente los dispositivos Azure IoT Edge con el [Servicio de aprovisionamiento de dispositivos](../iot-dps/index.yml). Si no está familiarizado con el proceso de aprovisionamiento automático, revise la información general sobre el [aprovisionamiento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

Las tareas son las siguientes:

1. Cree una máquina virtual (VM) con Linux en Hyper-V con un Módulo de plataforma segura (TPM) simulado para la seguridad de hardware.
1. Cree una instancia de IoT Hub Device Provisioning Service (DPS).
1. Cree una inscripción individual para el dispositivo.
1. Instale el entorno de ejecución de IoT Edge y conecte el dispositivo a IoT Hub.

> [!TIP]
> En este artículo se describe cómo probar el aprovisionamiento de DPS con un simulador de TPM, pero en gran medida se aplica tanto al hardware físico de TPM como al [TPM Infineon OPTIGA&trade;](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), un dispositivo Azure Certified for IoT.
>
> Si usa un dispositivo físico, puede ir directamente a la sección [Recuperación de la información de aprovisionamiento de un dispositivo físico](#retrieve-provisioning-information-from-a-physical-device) de este artículo.

## <a name="prerequisites"></a>Requisitos previos

* Un equipo de desarrollo de Windows [habilitado para Hyper-V](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). En este artículo se usa Windows 10 que ejecuta una máquina virtual de servidor de Ubuntu.
* Una instancia de IoT Hub activa.

> [!NOTE]
> Se necesita la versión TPM 2.0 al usar la atestación de TPM con DPS y solo se puede usar para crear inscripciones individuales, no de grupo.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Crear una máquina virtual con Linux con TPM virtual

En esta sección, se va a crear una nueva máquina virtual con Linux en Hyper-V. Se ha configurado esta máquina virtual con un TPM simulado para probar cómo funciona el aprovisionamiento automático con IoT Edge.

### <a name="create-a-virtual-switch"></a>Crear un conmutador virtual

Un conmutador virtual permite que la máquina virtual se conecte a una red física.

1. Abra el administrador de Hyper-V en la máquina Windows.

2. En el menú **Acciones**, seleccione **Administrador de conmutadores virtuales**.

3. Elija un conmutador virtual **Externo** y, después, seleccione **Crear conmutador virtual**.

4. Asigne un nombre al nuevo conmutador virtual, por ejemplo **EdgeSwitch**. Asegúrese de que el tipo de conexión esté establecido en **Red externa** y, a continuación, seleccione **Aceptar**.

5. Un mensaje emergente advierte de que es posible que se interrumpa la conectividad de red. Seleccione **Yes** (Sí) para continuar.

Si ve errores al crear el nuevo conmutador virtual, asegúrese de que ningún otro conmutador utilice el adaptador Ethernet y que ningún otro conmutador use el mismo nombre.

### <a name="create-virtual-machine"></a>Crear máquina virtual

1. Descargue un archivo de imagen de disco para usarlo para la máquina virtual y guárdelo localmente. Por ejemplo, [Servidor de Ubuntu 18.04](http://releases.ubuntu.com/18.04/). Para obtener información acerca de los sistemas operativos compatibles para dispositivos IoT Edge, consulte [sistemas compatibles con Azure IoT Edge](support.md).

2. De nuevo en el Administrador de Hyper-V, seleccione **Acción** > **Nuevo** > **Máquina virtual** en el menú **Acciones**.

3. Complete el **Asistente para crear nueva máquina virtual** con las siguientes configuraciones específicas:

   1. **Especificación de la generación**: seleccione **Generación 2**. Las máquinas virtuales de generación 2 tienen la virtualización anidada habilitada, lo cual es necesario para ejecutar IoT Edge en una máquina virtual.
   2. **Configurar funciones de red**: establezca el valor de **Conexión** en el conmutador virtual que creó en la sección anterior.
   3. **Opciones de instalación**: seleccione **Instalar un sistema operativo desde un archivo de imagen de arranque** y busque el archivo de imagen de disco que ha guardado localmente.

4. Seleccione **Finalizar** en el asistente para crear la máquina virtual.

La creación de la máquina virtual puede tardar unos minutos.

### <a name="enable-virtual-tpm"></a>Habilitar TPM virtual

Una vez creada la máquina virtual, abra la configuración para habilitar el módulo de plataforma segura (TPM) virtual, que permite aprovisionar de forma automática el dispositivo.

1. En el Administrador de Hyper-V, haga clic con el botón derecho en la VM y seleccione **Configuración**.

2. Navegue hasta **Seguridad**.

3. Anule la selección de la opción **Habilitar arranque seguro**.

4. Seleccione **Habilitar módulo de plataforma segura**.

5. Haga clic en **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Iniciar la máquina virtual y recopilar datos de TPM

En la máquina virtual, compile una herramienta que pueda utilizar para recuperar el **Id. de registro** y la **clave de aprobación** del dispositivo.

1. En el Administrador de Hyper-V, inicie VM y conéctese a ella.

1. Siga las indicaciones en la máquina virtual para finalizar el proceso de instalación y reinicie la máquina.

1. Inicie sesión en la máquina virtual y luego siga los pasos de [Configurar un entorno de desarrollo con Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) para instalar y compilar el SDK de dispositivo IoT de Azure para C.

   >[!TIP]
   >Durante este artículo, realizará operaciones de copiar y pegar en la máquina virtual y pegará desde esta, cosa que no fácil, mediante la aplicación de conexión de administrador de Hyper-V. Es posible que quiera conectarse a la máquina virtual mediante el administrador de Hyper-V una vez para recuperar su dirección IP. Ejecute `sudo apt install net-tools` primero y, luego, `hostname -I`. Después, puede usar la dirección IP para conectarse a través de SSH: `ssh <username>@<ipaddress>`.

1. Ejecute los comandos siguientes para compilar una herramienta de SDK que recupere la información de aprovisionamiento de dispositivos desde TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. En la ventana de salida se muestra el **Id. de registro** y la **Clave de aprobación**. Copie estos valores para usarlos más tarde al crear una inscripción individual para el dispositivo.

Una vez que tenga el identificador de registro y la clave de aprobación, continúe a la sección [Configurar IoT Hub Device Provisioning Service](#set-up-the-iot-hub-device-provisioning-service)

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Recuperación de la información de aprovisionamiento de un dispositivo físico

Si usa un dispositivo IoT Edge físico en lugar de una VM, cree una herramienta que pueda usar para recuperar la información de aprovisionamiento del dispositivo.

1. Siga los pasos de [Configuración de un entorno de desarrollo en Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) para instalar y compilar el SDK de dispositivo IoT de Azure para C.

1. Ejecute los comandos siguientes para compilar una herramienta de SDK que recupere la información de aprovisionamiento del dispositivo TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Copie los valores de **Id. de registro** y **Clave de aprobación**. Use estos valores para crear una inscripción individual para el dispositivo en DPS.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configuración de IoT Hub Device Provisioning Service

Cree una nueva instancia de IoT Hub Device Provisioning Service en Azure y vincúlela a IoT Hub. Puede seguir las instrucciones de [Configuración de IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Cuando Device Provisioning Service esté en ejecución, copie el valor de **Ámbito de id.** de la página de información general. Use este valor cuando configure el entorno de ejecución de IoT Edge.

## <a name="create-a-dps-enrollment"></a>Crear una inscripción de DPS

Recupere la información de aprovisionamiento de la máquina virtual y úsela para crear una inscripción individual en Device Provisioning Service.

Al crear una inscripción en DPS, tiene la oportunidad de declarar un **Estado inicial de dispositivo gemelo**. En el dispositivo gemelo, puede establecer etiquetas para agrupar dispositivos por cualquier métrica que necesite en su solución, como la región, el entorno, la ubicación o el tipo de dispositivo. Estas etiquetas se usan para crear [implementaciones automáticas](how-to-deploy-at-scale.md).

> [!TIP]
> En la CLI de Azure, puede crear una [inscripción](/cli/azure/iot/dps/enrollment) y usar la marca **edge-enabled** para especificar que se trata de un dispositivo IoT Edge.

1. En [Azure Portal](https://portal.azure.com), navegue hasta la instancia de IoT Hub Device Provisioning Service.

2. En **Configuración**, seleccione **Administrar inscripciones**.

3. Seleccione **Add individual enrollment** (Agregar inscripción individual) y, a continuación, complete los pasos siguientes para configurar la inscripción:  

   1. En **Mecanismo**, seleccione **TPM**.

   2. Proporcione la **Clave de aprobación** y la **Id. de registro** que se ha copiado de la máquina virtual.

      > [!TIP]
      > Si usa un dispositivo de TPM físico, debe determinar la **Clave de aprobación**, que es única para cada chip de TPM y se obtiene del fabricante del chip de TPM asociado. Puede derivar un único **identificador de registro** para el dispositivo TPM, por ejemplo, al aplicar un algoritmo hash SHA-256 a la clave de aprobación.

   3. Si lo desea, proporcione un identificador para el dispositivo. Si no proporciona un id. de dispositivo, se usará el id. de registro.

   4. Seleccione **Verdadero** para declarar que esta máquina virtual es un dispositivo IoT Edge.

   5. Elija la instancia de IoT Hub vinculada que quiere conectar al dispositivo o seleccione **Link to new IoT Hub** (Vincular a nueva instancia de IoT Hub). Puede elegir varios centros y el dispositivo se asignará a uno de ellos según la directiva de asignación seleccionada.

   6. Agregue un valor de etiqueta a **Estado inicial de dispositivo gemelo**, si lo desea. Puede usar etiquetas para los grupos de dispositivos de destino para la implementación del módulo. Para más información, consulte [Implementación de módulos IoT Edge a escala](how-to-deploy-at-scale.md).

   7. Seleccione **Guardar**.

Ahora que existe una inscripción para este dispositivo, el entorno de ejecución de Azure IoT Edge puede aprovisionar automáticamente el dispositivo durante la instalación.

## <a name="install-the-iot-edge-runtime"></a>Instalación del entorno de ejecución de IoT Edge

El runtime de IoT Edge se implementa en todos los dispositivos de IoT Edge. Sus componentes se ejecutan en contenedores y permiten implementar contenedores adicionales en el dispositivo para que pueda ejecutar código en Edge. Instale el entorno de ejecución de IoT Edge en la máquina virtual.

Siga los pasos descritos en [Instalación del entorno de ejecución de Azure IoT Edge](how-to-install-iot-edge.md) y, luego, vuelva a este artículo para aprovisionar el dispositivo.

## <a name="configure-the-device-with-provisioning-information"></a>Configuración del dispositivo con la información de aprovisionamiento

Una vez que el entorno de ejecución está instalado en el dispositivo, configure el dispositivo con la información que usa para conectarse al servicio Device Provisioning y a IoT Hub.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Conozca el **Ámbito de identificador** de DPS y el **Identificador de registro** del dispositivo que se recopilaron en las secciones anteriores.

1. Abra el archivo de configuración en el dispositivo IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Busque la sección de configuraciones de aprovisionamiento del archivo. Quite las marcas de comentario del aprovisionamiento TPM y asegúrese de que cualquier otra línea de aprovisionamiento esté comentada.

   La línea `provisioning:` no debe ir precedida por espacios en blanco y se debe aplicar una sangría de dos espacios a los elementos anidados.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "tpm"
       registration_id: "<REGISTRATION_ID>"
   # always_reprovision_on_startup: true
   # dynamic_reprovisioning: false
   ```

1. Actualice los valores de `scope_id` y `registration_id` con la información de DPS y del dispositivo.

1. También puede usar las líneas `always_reprovision_on_startup` o `dynamic_reprovisioning` para configurar el comportamiento de reaprovisionamiento del dispositivo. Si un dispositivo se establece para que se vuelva a aprovisionar en el inicio, siempre intentará aprovisionar con DPS primero y, a continuación, revertir a la copia de seguridad de aprovisionamiento si se produce un error. Si un dispositivo se establece para que se vuelva a aprovisionar dinámicamente, IoT Edge se reiniciará y volverá a aprovisionar si se detecta un evento de reaprovisionamiento. Para más información, consulte [Conceptos sobre el reaprovisionamiento de dispositivos de IoT Hub](../iot-dps/concepts-device-reprovision.md).

1. Guarde y cierre el archivo.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Conozca el **Ámbito de identificador** de DPS y el **Identificador de registro** del dispositivo que se recopilaron en las secciones anteriores.

1. Cree un archivo de configuración para el dispositivo basándose en un archivo de plantilla que se proporciona como parte de la instalación de IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Abra el archivo de configuración en el dispositivo IoT Edge.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Busque la sección de configuraciones de aprovisionamiento del archivo. Quite las marcas de comentario del aprovisionamiento TPM y asegúrese de que cualquier otra línea de aprovisionamiento esté comentada.

   ```toml
   # DPS provisioning with TPM
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "tpm"
   registration_id = "<REGISTRATION_ID>"
   ```

1. Actualice los valores de `id_scope` y `registration_id` con la información de DPS y del dispositivo.

1. Opcionalmente, busque la sección del modo de reaprovisionamiento automático del archivo. Use el parámetro `auto_reprovisioning_mode` para configurar el comportamiento de reaprovisionamiento del dispositivo en `Dynamic`, `AlwaysOnStartup` o `OnErrorOnly`. Para más información, consulte [Conceptos sobre el reaprovisionamiento de dispositivos de IoT Hub](../iot-dps/concepts-device-reprovision.md).

1. Guarde y cierre el archivo.
:::moniker-end
<!-- end 1.2 -->

## <a name="give-iot-edge-access-to-the-tpm"></a>Conceder acceso a IoT Edge en el TPM

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Para que el entorno de ejecución de IoT Edge aprovisione automáticamente el dispositivo, necesita acceso al TPM.

Puede conceder acceso TPM al entorno de ejecución de Azure IoT Edge mediante la invalidación de la configuración de systemd para que el servicio `iotedge` tenga privilegios raíz. Si no desea elevar los privilegios de servicio, también puede usar los pasos siguientes para proporcionar manualmente el acceso TPM.

1. Cree una nueva regla que conceda al entorno de ejecución de IoT Edge acceso a tpm0 y tpmrm0. 

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

2. Abra el archivo de reglas.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

3. Copie la siguiente información de acceso en el archivo de reglas. Es posible que `tpmrm0` no esté presente en los dispositivos que usan un kernel anterior a 4.12. Los dispositivos que no tengan tpmrm0 omitirán esa regla de forma segura.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
   KERNEL=="tpmrm0", SUBSYSTEM=="tpmrm", OWNER="iotedge", MODE="0600"
   ```

4. Guarde y cierre el archivo.

5. Desencadene el sistema udev para evaluar la nueva regla.

   ```bash
   /bin/udevadm trigger --subsystem-match=tpm --subsystem-match=tpmrm
   ```

6. Compruebe que la regla se haya aplicado correctamente.

   ```bash
   ls -l /dev/tpm*
   ```

   Una salida correcta tiene el siguiente aspecto:

   ```output
   crw------- 1 iotedge root 10, 224 Jul 20 16:27 /dev/tpm0
   crw------- 1 iotedge root 10, 224 Jul 20 16:27 /dev/tpmrm0
   ```

   Si no ve que se hayan aplicado los permisos correctos, intente reiniciar la máquina para actualizar udev.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
El entorno de ejecución de Azure IoT Edge se basa en un servicio TPM que accede mediante agente al TPM de un dispositivo. Para que este servicio aprovisione automáticamente el dispositivo, necesita acceso al TPM.

Puede conceder acceso al TPM mediante la invalidación de la configuración de systemd para que el servicio `aziottpm` tenga privilegios raíz. Si no desea elevar los privilegios de servicio, también puede usar los pasos siguientes para proporcionar manualmente el acceso TPM.

1. Cree una nueva regla que conceda al entorno de ejecución de IoT Edge acceso a tpm0 y tpmrm0. 

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

2. Abra el archivo de reglas.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

3. Copie la siguiente información de acceso en el archivo de reglas. Es posible que `tpmrm0` no esté presente en los dispositivos que usan un kernel anterior a 4.12. Los dispositivos que no tengan tpmrm0 omitirán esa regla de forma segura.

   ```input
   # allow aziottpm access to tpm0 and tpmrm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="aziottpm", MODE="0660"
   KERNEL=="tpmrm0", SUBSYSTEM=="tpmrm", OWNER="aziottpm", MODE="0660"
   ```

4. Guarde y cierre el archivo.

5. Desencadene el sistema udev para evaluar la nueva regla.

   ```bash
   /bin/udevadm trigger --subsystem-match=tpm --subsystem-match=tpmrm
   ```

6. Compruebe que la regla se haya aplicado correctamente.

   ```bash
   ls -l /dev/tpm*
   ```

   Una salida correcta tiene el siguiente aspecto:

   ```output
   crw-rw---- 1 aziottpm root 10, 224 Jul 20 16:27 /dev/tpm0
   crw-rw---- 1 aziottpm root 10, 224 Jul 20 16:27 /dev/tpmrm0
   ```

   Si no ve que se hayan aplicado los permisos correctos, intente reiniciar la máquina para actualizar udev. 
:::moniker-end
<!-- end 1.2 -->

## <a name="restart-iot-edge-and-verify-successful-installation"></a>Reinicie IoT Edge y compruebe que la instalación se ha realizado correctamente

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Reinicie el entorno de ejecución de IoT Edge para que aplique todos los cambios de configuración realizados en el dispositivo.

   ```bash
   sudo systemctl restart iotedge
   ```

Compruebe que el entorno de ejecución de IoT Edge esté en ejecución.

   ```bash
   sudo systemctl status iotedge
   ```

Examine los registros del daemon.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Si ve errores de aprovisionamiento, es posible que los cambios de configuración no hayan surtido efecto todavía. Pruebe a reiniciar de nuevo el demonio de IoT Edge.

   ```bash
   sudo systemctl daemon-reload
   ```

O bien, pruebe a reiniciar la máquina virtual para ver si los cambios surten efecto con un inicio nuevo.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Aplique los cambios de configuración realizados en el dispositivo.

   ```bash
   sudo iotedge config apply
   ```

Compruebe que el entorno de ejecución de IoT Edge esté en ejecución.

   ```bash
   sudo iotedge system status
   ```

Examine los registros del daemon.

   ```cmd/sh
   sudo iotedge system logs
   ```

Si ve errores de aprovisionamiento, es posible que los cambios de configuración no hayan surtido efecto todavía. Pruebe a reiniciar el demonio de IoT Edge.

   ```bash
   sudo systemctl daemon-reload
   ```

O bien, pruebe a reiniciar la máquina virtual para ver si los cambios surten efecto con un inicio nuevo.
:::moniker-end
<!-- end 1.2 -->

Si el entorno de ejecución se inició correctamente, puede entrar en su instancia de IoT Hub y ver que el nuevo dispositivo se aprovisionó automáticamente. Ahora el dispositivo está listo para ejecutar módulos de IoT Edge.

Enumere los módulos en ejecución.

```cmd/sh
iotedge list
```

Puede comprobar que la inscripción individual que ha creado se ha utilizado en el servicio Device Provisioning. En Azure Portal, vaya a la instancia del servicio Device Provisioning. Abra los detalles de la inscripción para la inscripción individual que ha creado. Tenga en cuenta que el estado de la inscripción está **asignado** y se muestra el id. de dispositivo.

## <a name="next-steps"></a>Pasos siguientes

El proceso de inscripción en DPS permite establecer el id. de dispositivo y las etiquetas del dispositivo gemelo al mismo tiempo que aprovisiona el nuevo dispositivo. Puede usar esos valores para dirigirse a dispositivos individuales o grupos de dispositivos con la administración automática de dispositivos. Aprenda a [implementar y supervisar los módulos de IoT Edge a escala mediante Azure Portal](how-to-deploy-at-scale.md) o la [CLI de Azure](how-to-deploy-cli-at-scale.md).
