---
title: Creación de un punto de conexión privado administrado para conectarse a los resultados del origen de datos
description: En este artículo se explica cómo crear un punto de conexión privado administrado para los orígenes de datos desde un área de trabajo de Azure Synapse.
author: ashinMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: seshin
ms.reviewer: jrasnick
ms.openlocfilehash: 6ca00f185c3bfc0a10a949953dfa0e2b1e7a09a4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737443"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source"></a>Creación de un punto de conexión privado administrado al origen de datos

En este artículo se explica cómo crear un punto de conexión privado administrado al origen de datos en Azure. Consulte [Puntos de conexión privados administrados](./synapse-workspace-managed-private-endpoints.md) para obtener más información.

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Paso 1: Abra el área de trabajo de Azure Synapse en Azure Portal

Puede crear un punto de conexión privado administrado para el origen de datos desde Azure Synapse Studio. Seleccione la pestaña **Información general** en Azure Portal y, en la tarjeta Open Synapse Studio (Abrir Synapse Studio), elija **Open** (Abrir).

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>Paso 2: Vaya a la pestaña Redes virtuales administradas en Synapse Studio

En Azure Synapse Studio, seleccione la pestaña **Administrar** en el panel de navegación izquierdo. Seleccione **Managed private endpoints** (Puntos de conexión privados administrados) y, después, elija **+ New** (+ Nuevo).
![Creación de un punto de conexión privado administrado](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>Paso 3: Seleccione el tipo de origen de datos

Seleccione el tipo de origen de datos. En este caso, el origen de datos de destino es una cuenta de ADLS Gen2. Seleccione **Continuar**.
![Seleccionar un tipo de origen de datos de destino](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>Paso 4: Proporcione información sobre el origen de datos

En la ventana siguiente, escriba la información sobre el origen de datos. En este ejemplo, vamos a crear un punto de conexión privado administrado a una cuenta de ADLS Gen2. Escriba el **Nombre** del punto de conexión privado administrado. Seleccione una **suscripción a Azure** y un **nombre de cuenta de almacenamiento**. Seleccione **Crear**.
![Especificar los detalles del origen de datos de destino](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>Paso 5: Compruebe que el punto de conexión privado administrado se creó correctamente

Después de enviar la solicitud, verá el estado. Para comprobar que el punto de conexión privado administrado se ha creado correctamente, compruebe su *Estado de aprovisionamiento*. Quizá deba esperar 1 minuto y seleccionar **Actualizar** para actualizar el estado de aprovisionamiento. Puede ver que el punto de conexión privado administrado a la cuenta de ADLS Gen2 se creó correctamente.

También puede ver que el *Estado de aprobación* es *Pendiente*. El propietario del recurso de destino puede aprobar o denegar la solicitud de conexión del punto de conexión privado. Si el propietario aprueba la solicitud de conexión del punto de conexión privado, se establece el vínculo privado. Si se rechaza, no se establecerá un vínculo privado.
![Estado de solicitud de creación de un punto de conexión privado administrado](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>Pasos siguientes

Más información sobre los [Puntos de conexión privados administrados](./synapse-workspace-managed-private-endpoints.md).