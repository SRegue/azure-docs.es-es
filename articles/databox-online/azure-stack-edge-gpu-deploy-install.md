---
title: 'Tutorial: Instalación, desempaquetamiento, montaje en el bastidor y cableado del dispositivo físico de Azure Stack Edge Pro con GPU | Microsoft Docs'
description: El segundo tutorial sobre la instalación de Azure Stack Edge Pro con GPU incluye cómo desempaquetar el dispositivo físico, montarlo en el bastidor y cablearlo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 07/07/2021
ms.author: alkohli
ms.openlocfilehash: 488b6d791afe477bb1aecacd0ecb15d54dcb43da
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114221325"
---
# <a name="tutorial-install-azure-stack-edge-pro-with-gpu"></a>Tutorial: Instalación de Azure Stack Edge Pro con GPU

Este tutorial describe cómo instalar un dispositivo físico de Azure Stack Edge Pro con una GPU. El procedimiento de instalación incluye el desempaquetado, el montaje en el bastidor y el cableado del dispositivo. 

La instalación puede tardar aproximadamente dos horas en completarse.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Desempaquetado del dispositivo
> * Montaje en bastidor del dispositivo
> * Cableado del dispositivo

## <a name="prerequisites"></a>Requisitos previos

Los requisitos previos para instalar un dispositivo físico son los siguientes:

### <a name="for-the-azure-stack-edge-resource"></a>Para el recurso de Azure Stack Edge

Antes de comenzar, asegúrese de que:

* Ha completado todos los pasos del tutorial [Preparación de la implementación de Azure Stack Edge Pro con GPU](azure-stack-edge-gpu-deploy-prep.md).
    * Ha creado un recurso de Azure Stack Edge para implementar el dispositivo.
    * Ha generado la clave de activación para activar el dispositivo con el recurso de Azure Stack Edge.

 
### <a name="for-the-azure-stack-edge-pro-physical-device"></a>Para el dispositivo físico de Azure Stack Edge Pro

Antes de implementar un dispositivo:

- Asegúrese de colocar el dispositivo de forma segura sobre una superficie de trabajo plana, estable y nivelada.
- Compruebe que el sitio donde desea realizar la instalación tiene:
    - Corriente alterna estándar de una fuente independiente.

        O
    - Una unidad de distribución de energía (PDU) en bastidor con un sistema de alimentación ininterrumpida (UPS).
    - Una ranura 1U disponible en el bastidor en el que se va a montar el dispositivo.

### <a name="for-the-network-in-the-datacenter"></a>Para la red del centro de datos

Antes de empezar:

- Revise los requisitos de red para implementar Azure Stack Edge Pro y configure la red del centro de datos según dichos requisitos. Para más información, consulte los [Requisitos de red de Azure Stack Edge Pro](azure-stack-edge-system-requirements.md#networking-port-requirements).

- Asegúrese de que el ancho de banda mínimo de Internet sea de 20 Mbps para que el dispositivo funcione de forma óptima.


## <a name="unpack-the-device"></a>Desempaquetado del dispositivo

Este dispositivo se suministra en una única caja. Realice los pasos siguientes para desempaquetar el dispositivo. 

1. Coloque la caja en una superficie plana y nivelada.
2. Compruebe si la caja y la espuma del embalaje presentan golpes, cortes, daños por agua o cualquier otro daño evidente. Si la caja o el embalaje están muy dañados, no los abra. Póngase en contacto con el soporte técnico de Microsoft para ayudarle a determinar si el dispositivo está en buen estado.
3. Desempaquete la caja. Después de desempaquetar la caja, asegúrese de que dispone de:
    - Un solo dispositivo contenedor de Azure Stack Edge Pro
    - Dos cables de alimentación
    - Un ensamblaje de kit de raíl
    - Un folleto informativo sobre la seguridad, el entorno y las normativas

Si no recibió alguno de los elementos enumerados aquí, [Póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). El paso siguiente es el montaje en bastidor del dispositivo.


## <a name="rack-the-device"></a>Montaje del dispositivo en el bastidor

El dispositivo debe instalarse en un bastidor estándar de 19 pulgadas. Use el siguiente procedimiento para montar el dispositivo en un bastidor estándar de 19 pulgadas.

> [!IMPORTANT]
> Los dispositivos de Azure Stack Edge Pro tienen que estar montados en un bastidor para funcionar correctamente.


### <a name="prerequisites"></a>Requisitos previos

- Antes de comenzar, lea las instrucciones de seguridad en el folleto sobre la seguridad, el entorno y las normativas. Este folleto se envió junto con el dispositivo.
- Comience la instalación de los raíles en el espacio asignado más próximo a la parte inferior del armario del bastidor.
- Para la configuración de montaje del raíl con herramientas:
    -  deberá suministrar ocho tornillos: #10-32, #12-24, #M5 o #M6. El diámetro de los cabezales de los tornillos debe ser inferior a 10 mm (0,4 pulgadas).
    -  Necesita un destornillador de punta plana.

### <a name="identify-the-rail-kit-contents"></a>Identificación del contenido del kit de raíl

Ubique los componentes de instalación de ensamblaje del kit de raíl:
- Dos ensamblajes de raíl deslizantes A7 Dell ReadyRails II
- Dos correas anchas con cierre de velcro

    ![Identificación del contenido del kit de raíl](./media/azure-stack-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Instalación y retirada de los raíles no mecanizados (bastidores de agujeros cuadrados y redondos)

> [!TIP]
> Esta opción es sin herramientas, ya no requiere herramientas para instalar y quitar los raíles en los orificios cuadrados o redondos lisos de los bastidores.

1. Coloque las piezas del extremo de los raíles izquierdo y derecho con la etiqueta **FRONT** orientadas hacia dentro y fijadas en los agujeros anteriores de las pestañas del bastidor en vertical.
2. Alinee cada pieza del extremo en los agujeros inferiores y superiores de los espacios en U preferidos.
3. Ajuste el extremo posterior del raíl hasta que quede fijado en la pestaña del bastidor en vertical y el pestillo haga clic. Repita estos pasos para colocar y fijar la pieza del extremo anterior en la pestaña del bastidor vertical.
4. Para quitar los carriles, suelte el botón de desbloqueo del pestillo de la parte central de la pieza del extremo y levante los raíles.

    ![Instalación y retirada de los raíles no mecanizados](./media/azure-stack-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Instalación y retirada de raíles mecanizados (bastidores con agujeros a rosca)

> [!TIP]
> Esta opción tiene herramientas porque requiere una herramienta (_un destornillador de punta plana_) para instalar y quitar los raíles en los orificios redondos con rosca de los bastidores.

1. Quite las fijaciones de los soportes de montaje anterior y posterior con un destornillador plano.
2. Tire de los submódulos de pestillo del raíl y gírelos para sacarlos de los soportes de montaje.
3. Encaje los raíles de montaje izquierdo y derecho a las pestañas anteriores del bastidor en vertical con 4 tornillos.
4. Deslice los soportes posteriores izquierdo y derecho hacia delante contra las pestañas posteriores del bastidor en vertical y encájelos con 4 tornillos.

    ![Instalación y retirada de raíles no mecanizados 2](./media/azure-stack-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>Instalación del sistema en un bastidor

1. Tire de los raíles deslizantes interiores hacia fuera del bastidor hasta que se bloqueen en su sitio.
2. Ubique los separadores de los raíles posteriores laterales del sistema y encájelos en las ranuras en J de los ensamblajes deslizantes. Gire el sistema hacia abajo hasta que todos los separadores de los raíles queden fijos en las ranuras en J.
3. Inserte el sistema hasta que las pestañas de cierre hagan clic.
4. Presione los botones de bloqueo deslizantes de los dos raíles y deslice el sistema para que encaje en el bastidor.

    ![Instalación del sistema en un bastidor](./media/azure-stack-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>Extracción del sistema del bastidor

1. Ubique las pestañas de cierre laterales en los raíles internos.
2. Desbloquee las pestañas; para ello, gírelas hasta la posición de desbloqueo.
3. Sujete firmemente los laterales del sistema e inserte este hasta que los separadores de los raíles queden frente a las ranuras en J. Levante el sistema y sáquelo del bastidor para colocarlo en una superficie nivelada.

    ![Extracción del sistema del bastidor](./media/azure-stack-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>Bloqueo y desbloqueo del pestillo de golpe

> [!NOTE]
> Si su sistema no dispone de pestillo de golpe, fíjelo con tornillos tal y como se describe en el paso 3 de este procedimiento.

1. De cara a la parte anterior, ubique el pestillo de golpe de alguno de los laterales del sistema.
2. Los pestillos se encajan automáticamente al insertar el sistema en el bastidor y se desbloquean al tirar de ellos.
3. Para fijar el sistema para el transporte en el bastidor o en otros entornos inestables, ubique el tornillo de montaje rígido bajo cada pestillo y apriételo con un destornillador Phillips del número 2.

    ![Bloqueo y desbloqueo del pestillo de golpe](./media/azure-stack-edge-deploy-install/engaging-releasing-slam-latch.png)


## <a name="cable-the-device"></a>Cableado del dispositivo

Disponga los cables y, a continuación, conéctelos al dispositivo. En los siguientes procedimientos se describe cómo realizar el cableado del dispositivo de Azure Stack Edge Pro para la conexión a la corriente eléctrica y a la red.

Antes de empezar el cableado del dispositivo, necesita lo siguiente:

- El dispositivo físico de Azure Stack Edge Pro, desempaquetado y montado en el bastidor.
- Dos cables de alimentación.
- Al menos un cable de red RJ-45 de 1-GbE cable para conectarse a la interfaz de administración de red. Hay dos interfaces de red de 1-GbE, uno de administración y otro de datos, en el dispositivo.
- Un cable de cobre SFP+ de 25/10 GbE para cada interfaz de red de datos que se va a configurar. Al menos una interfaz de red de datos de entre los PUERTOS 2, 3, 4, 5 o 6, debe estar conectada a Internet (para la conectividad a Azure).  
- Acceso a dos unidades de distribución de energía (recomendable).
- Al menos un conmutador de red de 1 GbE para conectar una interfaz de red de 1 GbE a Internet para los datos. La interfaz de usuario web local no será accesible si el conmutador conectado no es de al menos 1 GbE. Si usa la interfaz de 25/10 GbE para los datos, necesitará un conmutador de 25 GbE o 10 GbE.

> [!NOTE]
> - Si va a conectar solo una interfaz de red de datos, es recomendable que use una interfaz de red de 25/10 GbE como la de los PUERTOS 3, 4, 5 o 6 para enviar datos a Azure. 
> - Para obtener el mejor rendimiento y controlar grandes volúmenes de datos, considere la posibilidad de conectar todos los puertos de datos.
> - El dispositivo de Azure Stack Edge Pro debe estar conectado a la red del centro de datos para que pueda realizar la ingesta de datos desde los servidores de origen de datos.

En el dispositivo de Azure Stack Edge Pro:

- El panel frontal tiene unidades de disco y un botón de encendido.

    - Hay 10 ranuras de disco en el frontal del dispositivo.
    - La ranura 0 tiene una unidad SATA de 240 GB que se usa como disco del sistema operativo. La ranura 1 está vacía y las ranuras 2 a 6 son unidades de estado sólido de NVMe que se usan como discos de datos. Las ranuras 7 a 9 también están vacías.
- El backplane incluye fuentes de alimentación (PSU) redundantes.
- El backplane tiene seis interfaces de red:

    - Dos interfaces de 1 Gbps.
    - Cuatro interfaces de 25 Gbps que también sirven como interfaces de 10 Gbps.
    - Un controlador de administración de placa base (BMC).

- El backplane tiene dos tarjetas de red correspondientes a los seis puertos:

    - **Adaptador personalizado Microsoft `Qlogic` Cavium 25 G NDC**: puertos 1 a 4.
    - **Adaptador de red de 4 canales, doble puerto 25G ConnectX de Mellanox**: puertos 5 y 6.

Para una lista completa de cables, enchufes y transceptores compatibles para estas tarjetas de adaptador de red, consulte:

- [Matriz de interoperabilidad del adaptador `Qlogic` Cavium 25 G NDC](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
- Cables y módulos de 25 GbE y 10 GbE en [Productos compatibles con el adaptador de red de 4 canales, doble puerto 25G ConnectX de Mellanox](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).  

 
Realice los pasos siguientes para realizar el cableado de los cables de alimentación y de red del dispositivo.

1. Identifique los distintos puertos del backplane del dispositivo. Es posible que haya recibido uno de los siguientes dispositivos de la factoría en función del número de GPU que haya en el dispositivo.

    - Dispositivo con dos ranuras de interconexión de componentes periféricos (PCI) y una GPU

        ![Backplane de un dispositivo cableado](./media/azure-stack-edge-gpu-deploy-install/ase-two-pci-slots.png)

    - Dispositivo con tres ranuras PCI y una GPU

        ![Backplane de un dispositivo cableado 2](./media/azure-stack-edge-gpu-deploy-install/ase-three-pci-slots-one-gpu.png)

    - Dispositivo con tres ranuras PCI y dos GPU

        ![Backplane de un dispositivo cableado 3](./media/azure-stack-edge-gpu-deploy-install/ase-three-pci-slots-two-gpu.png)

2. Busque las ranuras de los discos y el botón de encendido en el frontal del dispositivo.

    ![Plano delantero de un dispositivo](./media/azure-stack-edge-gpu-deploy-install/ase-gpu-device-front-plane-labeled.png)

3. Conecte los cables de alimentación a cada una de las fuentes de alimentación del receptáculo. Para garantizar una alta disponibilidad, instale y conecte ambas fuentes de alimentación a distintas tomas de alimentación.
4. Conecte los cables de alimentación a las unidades de distribución de energía (PDU) del bastidor. Asegúrese de que las dos PSU usen tomas de alimentación independientes.
5. Presione el botón de encendido para encender el dispositivo.
6. Conecte el PUERTO 1 de la interfaz de red de 1-GbE al equipo que se usa para configurar el dispositivo físico. El PUERTO 1 actúa como la interfaz de administración.
    
    > [!NOTE]
    > Si conecta el equipo directamente al dispositivo (sin pasar por un conmutador), use un cable cruzado o un adaptador Ethernet USB.

7. Conecte uno o varios de los PUERTOS 2, 3, 4, 5 o 6 a la red del centro de datos o Internet.

    - Si conecta el PUERTO 2, utilice el cable de red RJ-45 de 1 GbE.
    - Para las interfaces de red de 10/25 GbE, use cables de cobre SFP+ o fibra. Si usa fibra, utilice un adaptador de óptico a SFP.
    - Para las implementaciones de Network Function Manager, asegúrese de que los puertos 5 y 6 están conectados. Para obtener más información, vea [Tutorial: Implementación de funciones de red en Azure Stack Edge (versión preliminar)](../network-function-manager/deploy-functions.md).

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha obtenido información acerca de varios temas relacionados con Azure Stack Edge Pro, como:

> [!div class="checklist"]
> * Desempaquetado del dispositivo
> * Montaje del dispositivo en el bastidor
> * Cableado del dispositivo

Continúe con el siguiente tutorial para aprender a conectar el dispositivo.

> [!div class="nextstepaction"]
> [Conexión de Azure Stack Edge Pro](./azure-stack-edge-gpu-deploy-connect.md)
