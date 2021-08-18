---
title: Conexión de datos de Alcide kAudit con Azure Sentinel | Microsoft Docs
description: Aprenda a conectar los datos de Alcide kAudit a Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: 418a1289b6f584e58b88e2fb0f571f724ba5d800
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253189"
---
# <a name="connect-your-alcide-kaudit-to-azure-sentinel"></a>Conexión de Alcide kAudit a Azure Sentinel

> [!IMPORTANT]
> El conector de datos de Alcide kAudit en Azure Sentinel se encuentra actualmente en versión preliminar pública.
> Esta característica se ofrece sin contrato de nivel de servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

[Alcide kAudit](https://www.alcide.io/kaudit-K8s-forensics/) le ayuda a identificar los comportamientos anómalos de Kubernetes y a centrarse en las infracciones e incidentes de Kubernetes a la vez que reduce el tiempo de detección. En este artículo se explica cómo conectar la solución de Alcide kAudit a Azure Sentinel. El conector de datos de Alcide kAudit permite incorporar fácilmente los datos de registro de kAudit a Azure Sentinel, de modo que pueda verlos en los libros, usarlos para crear alertas personalizadas e incorporarlos para mejorar la investigación. La integración entre Alcide kAudit y Azure Sentinel usa la API de REST.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="prerequisites"></a>Requisitos previos

- Debe tener permisos de lectura y escritura en el área de trabajo de Azure Sentinel.

- Debe tener permisos de lectura para las claves compartidas del área de trabajo.

## <a name="configure-and-connect-alcide-kaudit"></a>Configuración y conexión de Alcide kAudit

Alcide kAudit puede exportar registros directamente a Azure Sentinel.

1. En el portal de Azure Sentinel, haga clic en la opción **Conectores de datos** situada en el menú de navegación.

1. Seleccione **Alcide kAudit** en la galería y haga clic en el botón **Abrir conector**.

1. Siga las instrucciones paso a paso proporcionadas en la [Guía de instalación de Alcide kAudit](https://awesomeopensource.com/project/alcideio/kaudit?categoryPage=29#before-installing-alcide-kaudit).

1. Cuando se le pida el identificador de área de trabajo y la clave principal, puede copiarlos desde la página del conector de datos de Alcide kAudit.

    :::image type="content" source="media/connect-alcide-kaudit/alcide-workspace-id-primary-key.png" alt-text="Identificador de área de trabajo y clave principal":::

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida una conexión correcta, los datos aparecen en **Registros**, debajo de los siguientes tipos de datos de **CustomLogs**:

- **alcide_kaudit_detections_1_CL**: detecciones de Alcide kAudit 
- **alcide_kaudit_activity_1_CL**: registros de actividad de Alcide kAudit
- **alcide_kaudit_selections_count_1_CL**: recuentos de actividad de cAlcide kAudit
- **alcide_kaudit_selections_details_1_CL**: detalles de actividad de Alcide kAudit

Para usar el esquema pertinente en Registros para Alcide kAudit, busque los tipos de datos mencionados anteriormente.

Los registros pueden tardar hasta 20 minutos en empezar a aparecer en Log Analytics.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar Alcide kAudit a Azure Sentinel. Para sacar el máximo partido de las capacidades integradas en este conector de datos, haga clic en la pestaña **Pasos siguientes** de la página del conector de datos. Allí encontrará algunas consultas de ejemplo preparadas para que pueda empezar a buscar información útil.

Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.