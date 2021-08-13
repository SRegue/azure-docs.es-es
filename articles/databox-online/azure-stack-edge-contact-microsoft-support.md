---
title: Registro de una incidencia de soporte técnico de Azure Stack Edge y Azure Data Box Gateway | Microsoft Docs
description: Aprenda registrar una solicitud de soporte técnico para problemas relacionados con los pedidos de Azure Stack Edge o Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/09/2021
ms.author: alkohli
ms.openlocfilehash: 1ad5475078c515d36a57b7608ab9d363c6f678aa
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111983964"
---
# <a name="open-a-support-ticket-for-azure-stack-edge-and-azure-data-box-gateway"></a>Apertura de una incidencia de soporte técnico relacionada con Azure Stack Edge y Azure Data Box Gateway

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-fpga-databox-gateway-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-fpga-databox-gateway-sku.md)]

Este artículo es aplicable a Azure Stack Edge y Azure Data Box Gateway, que están administrados por el servicio Azure Stack Edge o Azure Data Box Gateway. Si tiene algún problema con el servicio, puede crear una solicitud de servicio de soporte técnico. Este artículo le enseñará a:

* Crear una solicitud de soporte.
* Administrar el ciclo de vida de una solicitud de soporte técnico desde dentro del portal.

## <a name="create-a-support-request"></a>Crear una solicitud de soporte

Realice los pasos siguientes para crear una solicitud de soporte técnico:

1. Vaya al pedido de Azure Stack Edge o Data Box Gateway. Vaya a la sección **Soporte técnico y solución de problemas** y, luego, seleccione **Nueva solicitud de soporte técnico**.

2. En **Nueva solicitud de soporte técnico**, en la pestaña **Aspectos básicos**, siga estos pasos:

    1. En la lista desplegable **Tipo de problema**, seleccione **Técnico**.
    2. Elija la **suscripción**.
    3. En **Servicio**, compruebe **Mis servicios**. En la lista desplegable, seleccione **Azure Stack Edge y Data Box Gateway**.
    4. Seleccione el **recurso**. Este corresponde al nombre del pedido.
    5. Escriba un **Resumen** breve del problema que experimenta. 
    6. Seleccione el **tipo de problema**.
    7. Según el tipo de problema que seleccionó, elija un **subtipo de problema** correspondiente.
    8. Seleccione **Siguiente: Soluciones >>** .

        ![Aspectos básicos](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)

3. En la pestaña **Detalles**, siga estos pasos:

    1. Especifique la fecha y hora de inicio del problema.
    2. Proporcione una **descripción** del problema.
    3. En **Carga de archivos**, seleccione el icono de la carpeta para navegar a cualquier otro archivo que quiera cargar.
    4. Seleccione **Compartir información de diagnóstico**.
    5. En función de la suscripción, un **plan de soporte técnico** se rellena de manera automática.
    6. En la lista desplegable, seleccione la **gravedad**.
    7. Especifique un **método de contacto preferido**.
    8. Las **horas de respuesta** se seleccionan automáticamente en función del plan de suscripción.
    9. Indique el idioma que prefiere para el soporte técnico.
    10. En la **información de contacto**, proporcione su nombre, correo electrónico, teléfono, contacto opcional, país o región. El Soporte técnico de Microsoft utiliza esta información para ponerse en contacto con usted para solicitar más información y comunicar el diagnóstico y la resolución. 
    11. Seleccione **Siguiente: Revisar y crear >>** .

        ![Problema](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-2.png)

4. En la pestaña **Revisar y crear**, revise la información relacionada con la incidencia de soporte técnico. Seleccione **Crear**. 

    ![Problema 2](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-3.png)

    Una vez que se crea la incidencia de soporte técnico, un ingeniero de soporte técnico se pondrá en contacto con usted tan pronto como sea posible para procesar la solicitud.

## <a name="get-hardware-support"></a>Obtención de soporte técnico para hardware

Esta información solo es aplicable a los dispositivos de Azure Stack. El proceso para informar sobre problemas de hardware es el siguiente:

1. Abra una incidencia de soporte técnico en Azure Portal para un problema de hardware. En **Tipo de problema**, seleccione **Azure Stack Hardware** (Hardware de Azure Stack). Como **Subtipo de problema**, elija **Error de hardware**.

    ![Problema de hardware](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-hardware-issue-1.png)

    Una vez que se crea una incidencia de soporte técnico, un ingeniero de soporte técnico se pondrá en contacto con usted tan pronto como sea posible para procesar la solicitud.

2. Si Soporte técnico de Microsoft determina que se trata de un problema de hardware, se produce una de las siguientes acciones:

    * Se envía una unidad reemplazable de campo (FRU) para la pieza de hardware con errores. Actualmente, las unidades de fuente de alimentación y las unidades de estado sólido son las únicas FRU que se admiten.
    * Solo se sustituyen las FRU en el plazo del siguiente día laborable, todo lo demás requiere un reemplazo completo del sistema (FSR).

3. Si se determina la necesidad de un reemplazo de FRU antes de la 1 p.m. hora local (de lunes a viernes), se envía un técnico presencial el siguiente día laborable a la ubicación para que lo realice. Normalmente, un reemplazo completo del sistema tardará mucho más tiempo, ya que las piezas se envían desde fábrica y pueden estar sujetas a retrasos de transporte y aduanas.

## <a name="manage-a-support-request"></a>Administración de una solicitud de soporte técnico

Después de crear una incidencia de soporte técnico, puede administrar el ciclo de vida de la incidencia desde el portal.

### <a name="to-manage-your-support-requests"></a>Para administrar las solicitudes de soporte técnico

1. Para tener acceso a la página de ayuda y soporte técnico, vaya a **Examinar > Ayuda y soporte técnico**.

    ![Administrar solicitudes de soporte técnico](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-manage-support-request-1.png)

2. Se muestra una lista tabular de **Solicitudes de soporte técnico recientes** en **Ayuda y soporte técnico**.

    <!--[Manage support requests](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)--> 

3. Seleccione una solicitud de soporte técnico y haga clic en ella. Puede ver el estado y los detalles de esta solicitud. Haga clic en **+ Nuevo mensaje** si desea realizar un seguimiento de esta solicitud.

## <a name="next-steps"></a>Pasos siguientes

- [Solucionar problemas relacionados con la matriz de puertas programables de Azure Stack Edge](azure-stack-edge-troubleshoot.md).
- [Solucionar problemas del dispositivo de Azure Stack Edge Pro GPU](azure-stack-edge-gpu-troubleshoot.md).
- [Solucionar problemas relacionados con Data Box Gateway](../databox-gateway/data-box-gateway-troubleshoot.md).