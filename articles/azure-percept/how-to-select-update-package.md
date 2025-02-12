---
title: Selección del paquete de actualización más adecuado para Azure Percept DK
description: Obtenga información sobre cómo identificar la versión de Azure Percept DK y seleccionar el paquete de actualización más adecuado.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 07/23/2021
ms.custom: template-how-to
ms.openlocfilehash: e2256ba6ad1f2f125b21e7cb59ab74b44864bce6
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122070778"
---
# <a name="how-to-determine-and-download-the-best-update-package-for-ota-and-usb-updates"></a>Determinación y descarga del paquete de actualización más adecuado para las actualizaciones mediante OTA y USB

En esta página se proporcionan instrucciones sobre cómo seleccionar el paquete de actualización que mejor se adapte a su kit de desarrollo y las ubicaciones de descarga de los paquetes de actualización.

Para más información sobre cómo actualizar el dispositivo, consulte estos artículos:
- [Actualización de Azure Percept DK de forma inalámbrica](./how-to-update-over-the-air.md)
- [Actualización de Azure Percept DK mediante USB](./how-to-update-via-usb.md)


## <a name="prerequisites"></a>Requisitos previos

- Un kit de desarrollo [Azure Percept DK](https://go.microsoft.com/fwlink/?linkid=2155270) que se haya [configurado y conectado a Azure Percept Studio e IoT Hub](./quickstart-percept-dk-set-up.md).

## <a name="identify-the-model-name-and-software-version-of-your-dev-kit"></a>Identificación del nombre del modelo y la versión de software del kit de desarrollo
Para asegurarse de aplicar el paquete de actualización correcto al kit de desarrollo, primero debe determinar qué versión de software se está ejecutando actualmente.

> [!WARNING]
> La aplicación de un paquete de actualización incorrecto podría dar lugar a que el kit de desarrollo se vuelva inoperable. Es importante que siga estos pasos para asegurarse de que aplica el paquete de actualización correcto.

Opción 1:
1. Inicie sesión en [Azure Percept Studio](./overview-azure-percept-studio.md).
2. En **Dispositivos**, elija el dispositivo devkit.
3. En la pestaña **General**, busque la información del **modelo** y la **versión de SW**.

Opción 2:
1. Consulte el **dispositivo IoT Edge** del servicio **IoT Hub** en Microsoft Azure Portal.
2. Elija el dispositivo devkit en la lista de dispositivos.
3. Seleccione **Device twin** (Dispositivo gemelo).
4. Desplácese por las propiedades del dispositivo gemelo, busque los elementos **"model"** (modelo) y **"swVersion"** (versión de software) en **"deviceInformation"** (información del dispositivo) y anote sus valores.

## <a name="determine-the-correct-update-package"></a>Determinación del paquete de actualización correcto
Con los elementos **model** y **swVersion** identificados en la sección anterior, compruebe la tabla siguiente para determinar qué paquete de actualización va a descargar.


|model  |swVersion  |Método de actualización  |Vínculos de descarga  |Nota  |
|---------|---------|---------|---------|---------|
|PE-101     |Todas las versiones de software       |**Solo USB**         |[Paquete de actualización mediante USB 2021.107.129.116](https://go.microsoft.com/fwlink/?linkid=2169086)         |Versión de julio (2107)         |
|APDK-101     |Cualquier versión de software anterior a 2021.106.111.115 |**Solo USB**         |[Paquete de actualización mediante USB 2021.107.129.116](https://go.microsoft.com/fwlink/?linkid=2169086)         |Versión de julio (2107)         |
|APDK-101     |2021.106.111.115        |OTA o USB       |[Paquete de actualización mediante OTA 2021.107.129.116](https://go.microsoft.com/fwlink/?linkid=2169245)<br>[Paquete de actualización mediante USB 2021.107.129.116](https://go.microsoft.com/fwlink/?linkid=2169086)        |Versión de julio (2107)         |


## <a name="next-steps"></a>Pasos siguientes
Actualice los kits de desarrollo mediante los métodos y los paquetes de actualización determinados en la sección anterior.
- [Actualización de Azure Percept DK de forma inalámbrica](./how-to-update-over-the-air.md)
- [Actualización de Azure Percept DK mediante USB](./how-to-update-via-usb.md)
