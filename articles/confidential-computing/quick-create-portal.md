---
title: 'Inicio rápido: Creación de una máquina virtual de computación confidencial de Azure en Azure Portal'
description: Para empezar a trabajar con sus implementaciones, aprenda a crear rápidamente una máquina virtual de computación confidencial en Azure Portal.
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 06/13/2021
ms.author: JenCook
ms.openlocfilehash: 8fb93b7697e2dd9077995572fc91b6e82a7d8512
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113107225"
---
# <a name="quickstart-deploy-an-azure-confidential-computing-vm-in-the-azure-portal"></a>Inicio rápido: Implementación de una máquina virtual de computación confidencial de Azure en Azure Portal

Empiece a trabajar con la computación confidencial de Azure mediante Azure Portal para crear una máquina virtual con el respaldo de Intel SGX. A continuación, podrá ejecutar aplicaciones de enclave.

Se recomienda este tutorial si está interesado en implementar una máquina virtual de computación confidencial con una configuración personalizada. En caso contrario, le recomendamos que siga los [pasos de implementación de la máquina virtual de computación confidencial del marketplace comercial de Microsoft](quick-create-marketplace.md).


## <a name="prerequisites"></a>Requisitos previos

Si no tiene una suscripción a Azure, [cree una cuenta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de empezar.

> [!NOTE]
> Las cuentas de evaluación gratuita no tienen acceso a las máquinas virtuales que se usan en este tutorial. Actualice a una suscripción de pago por uso.


## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. En la parte superior, seleccione **Crear un recurso**.

1. En el panel de **Marketplace**, seleccione **Compute** a la izquierda.

1. Busque y seleccione **Máquina virtual**.

    ![Implementación de una máquina virtual](media/quick-create-portal/compute-virtual-machine.png)

1. En la página de aterrizaje de la máquina virtual, seleccione **Crear**.


## <a name="configure-a-confidential-computing-virtual-machine"></a>Configuración de una máquina virtual de computación confidencial

1. En la pestaña **Fundamentos**, seleccione la **suscripción** y el **grupo de recursos**.

1. En **Nombre de la máquina virtual**, escriba un nombre para la nueva máquina virtual.

1. Escriba o seleccione los valores siguientes:

   * **Región**: Seleccione la región de Azure adecuada para usted.

        > [!NOTE]
        > Las máquinas virtuales de computación confidencial solo se ejecutan en hardware especializado disponible en regiones específicas. Para ver las regiones más recientes disponibles para las máquinas virtuales de la serie DCsv2, consulte las [regiones disponibles](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

1. Configure la imagen del sistema operativo que le gustaría usar para la máquina virtual.

    * **Elija una imagen**: en este tutorial, seleccione Ubuntu 18.04 LTS. También puede seleccionar Windows Server 2019, Windows Server 2016 y Ubuntu 16.04 LTS. Si decide hacerlo, se le proporcionará la información adecuada en este tutorial.
    
    * **Alterne la imagen de Gen 2**: las máquinas virtuales de computación confidencial solo se ejecutan en imágenes de la [Generación 2](../virtual-machines/generation-2.md). Asegúrese de que la imagen que selecciona es una imagen de esta generación. Haga clic en la pestaña **Avanzado** que está encima de donde está configurando la máquina virtual. Desplácese hacia abajo hasta que encuentre la sección denominada "Generación de VM". Seleccione Gen 2 y, a continuación, vuelva a la pestaña **Básico**.
    

        ![Pestaña Avanzadas](media/quick-create-portal/advanced-tab-virtual-machine.png)


        ![Generación de VM](media/quick-create-portal/gen2-virtual-machine.png)

    * **Vuelva a la configuración básica**: Vuelva a la pestaña **Básico** con los controles de navegación de la parte superior.

1. Elija una máquina virtual con funcionalidades de computación confidencial en el selector de tamaño. Para ello, seleccione **cambiar tamaño**. En el selector de tamaño de la máquina virtual, haga clic en **Borrar todos los filtros**. Elija **Agregar filtro**, seleccione **Familia** como tipo de filtro y, a continuación, seleccione solo **Proceso confidencial**.

    ![Máquinas virtuales de la serie DCsv2](media/quick-create-portal/dcsv2-virtual-machines.png)

    > [!TIP]
    > Debería ver los tamaños **DC1s_v2**, **DC2s_v2**, **DC4s_V2** y **DC8_v2**. Estos son los únicos tamaños de máquina virtual que admiten actualmente la computación confidencial. [Más información](virtual-machine-solutions.md).

1. Rellene la información siguiente:

   * **Tipo de autenticación**: Seleccione **Clave pública SSH** si va a crear una máquina virtual Linux. 

        > [!NOTE]
        > Para la autenticación, puede una clave pública SSH o una contraseña. La opción de SSH es más segura. Para obtener instrucciones acerca de cómo generar una clave SSH, consulte [Creación y uso de un par de claves SSH pública y privada para máquinas virtuales Linux en Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

    * **Nombre de usuario**: Escriba el nombre del administrador de la máquina virtual.

    * **Clave pública SSH**: Si es aplicable, escriba la clave pública RSA.
    
    * **Contraseña**: Si procede, escriba la contraseña para la autenticación.

    * **Puertos de entrada públicos**: Elija **Permitir los puertos seleccionados** y seleccione **SSH (22)** y **HTTP (80)** en la lista **Seleccionar puertos de entrada públicos**. Si va a implementar una máquina virtual Windows, seleccione **HTTP (80)** y **RDP (3389)** .  

    >[!Note]
    > No se recomienda permitir puertos RDP/SSH para implementaciones de producción.  

     ![Puertos de entrada](media/quick-create-portal/inbound-port-virtual-machine.png)


1. Realice los cambios en la pestaña **Discos**.

   * Si ha elegido una máquina virtual **DC1s_v2**, **DC2s_v2**, **DC4s_V2**, elija un tipo de disco que sea **SSD estándar** o **SSD Premium**. 
   * Si ha elegido una máquina virtual **DC8_v2**, elija **SSD estándar** como tipo de disco.

1. Haga los cambios que quiera en la configuración en las pestañas siguientes o conserve la configuración predeterminada.

    * **Redes**
    * **Administración**
    * **Configuración de invitado**
    * **Etiquetas**

1. Seleccione **Revisar + crear**.

1. En el panel **Revisar + crear**, seleccione **Crear**.

> [!NOTE]
> Vaya a la sección siguiente y siga en este tutorial si ha implementado una máquina virtual Linux. Si ha implementado una máquina virtual Windows, [siga estos pasos para conectarse a ella](../virtual-machines/windows/connect-logon.md) y, a continuación, [instale el SDK de Open Enclave en Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md).


## <a name="connect-to-the-linux-vm"></a>Conexión a la máquina virtual Linux

Si ya utiliza un shell de BASH, conéctese a la máquina virtual de Azure mediante el comando **ssh**. En el siguiente comando, reemplace el nombre de usuario y la dirección IP de la máquina virtual para conectarse a su máquina virtual Linux.

```bash
ssh azureadmin@40.55.55.555
```

Puede buscar la dirección IP pública de la máquina virtual en la sección Información general de la máquina virtual en Azure Portal.

:::image type="content" source="media/quick-create-portal/public-ip-virtual-machine.png" alt-text="Dirección IP en Azure Portal":::

Si utiliza Windows y no tiene un shell de BASH, instale un cliente de SSH, como PuTTY.

1. [Descargue e instale PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Ejecute PuTTY.

1. En la pantalla de configuración de PuTTY, escriba la dirección IP pública de la máquina virtual.

1. Seleccione **Abrir** y escriba el nombre de usuario y la contraseña en los cuadros.

Para más información acerca de cómo conectarse a máquinas virtuales Linux, consulte [Creación de máquinas virtuales Linux con Azure Portal](../virtual-machines/linux/quick-create-portal.md).

> [!NOTE]
> Si ve una alerta de seguridad de PuTTY que indique que la clave de host del servidor no se almacena en la caché del registro, elija entre las opciones siguientes. Si confía en este host, seleccione **Sí** para agregar la clave a la caché de PuTTY y siga conectándose. Si quiere conectarse solo una vez, sin agregar la clave a la caché, seleccione **No**. Si no confía en este host, seleccione **Cancelar** para abandonar la conexión.

## <a name="intel-sgx-drivers"></a>Controladores de Intel SGX

> [!NOTE]
> Los controladores de Intel SGX que ya forman parte de las imágenes de la galería de Microsoft Azure y Ubuntu. No se requiere ninguna instalación especial de los controladores. Opcionalmente, también puede actualizar los controladores existentes incluidos en las imágenes mediante el examen de la [lista de controladores DCAP de Intel SGX](https://01.org/intel-software-guard-extensions/downloads).

## <a name="optional-testing-enclave-apps-built-with-open-enclave-sdk-oe-sdk"></a>Opcional: prueba de aplicaciones de enclave creadas con el SDK de Open Enclave (SDK de OE) <a id="Install"></a>

Siga las instrucciones detalladas para instalar el [SDK de OE](https://github.com/openenclave/openenclave) en la máquina virtual de la serie DCsv2 con una imagen de Ubuntu 18.04 LTS Gen 2. 

Si la máquina virtual se ejecuta en Ubuntu 18.04 LTS Gen 2, deberá seguir las [instrucciones de instalación de Ubuntu 18.04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md).

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no los necesite, puede eliminar el grupo de recursos, la máquina virtual y todos los recursos relacionados. 

Seleccione el grupo de recursos de la máquina virtual y haga clic en **Eliminar**. Confirme el nombre del grupo de recursos para terminar de eliminar los recursos.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha implementado una máquina virtual de computación confidencial y ha instalado el SDK de Open enclave. Para más información sobre las máquinas virtuales de computación confidencial en Azure, consulte [Soluciones en Virtual Machines](virtual-machine-solutions.md). 

Descubra cómo puede crear aplicaciones de computación confidencial. Para ello, continúe con los ejemplos del SDK de Open Enclave en GitHub. 

> [!div class="nextstepaction"]
> [Compilación de ejemplos del SDK de Open Enclave](https://github.com/openenclave/openenclave/blob/master/samples/README.md)
