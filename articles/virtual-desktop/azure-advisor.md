---
title: 'Integración de Azure Virtual Desktop con Azure Advisor: Azure'
description: Uso de Azure Advisor con la implementación de Azure Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 25bdeb3db8872550a4f1f03e7165cd24653272c1
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111745356"
---
# <a name="use-azure-advisor-with-azure-virtual-desktop"></a>Uso de Azure Advisor para Azure Virtual Desktop

Azure Advisor puede ayudar a los usuarios a resolver problemas comunes por su cuenta, sin tener que presentar casos de soporte técnico. Las recomendaciones reducen la necesidad de enviar solicitudes de ayuda, lo que le ahorra tiempo y costos.

En este artículo le indicaremos cómo configurar Azure Advisor en la implementación de Azure Virtual Desktop para ayudar a sus usuarios.

## <a name="what-is-azure-advisor"></a>¿Qué es Azure Advisor?

Azure Advisor analiza las configuraciones y la telemetría para ofrecer recomendaciones personalizadas a fin de resolver problemas comunes. Con estas recomendaciones, puede optimizar sus recursos de Azure para la confiabilidad, la seguridad, la excelencia operativa, el rendimiento y el costo. Obtenga más información en [el sitio web de Azure Advisor](https://azure.microsoft.com/services/advisor/).

## <a name="how-to-start-using-azure-advisor"></a>Cómo empezar a usar Azure Advisor

Todo lo que necesita para empezar es una cuenta de Azure en Azure Portal. En primer lugar, abra Azure Portal en <https://portal.azure.com/#home> y, después, seleccione **Advisor** en **Servicios de Azure**, como se muestra en la siguiente imagen. También puede escribir "Azure Advisor" en la barra de búsqueda de Azure Portal.

> [!div class="mx-imgBorder"]
> ![Captura de pantalla de Azure Portal. El usuario pasa el cursor del mouse sobre el vínculo de Azure Advisor, lo que hace que aparezca un menú desplegable.](media/azure-advisor.png)

Al abrir Azure Advisor, verá cinco categorías:

- Coste
- Seguridad
- Confiabilidad
- Excelencia operativa
- Rendimiento

> [!div class="mx-imgBorder"]
> ![Captura de pantalla de Azure Advisor que muestra los cinco menús de cada categoría. Los cinco elementos mostrados en sus propios cuadros son Costo, Seguridad, Confiabilidad, Excelencia operativa y Rendimiento.](media/advisor-categories.png)

Al seleccionar una categoría, irá a su página de recomendaciones activas. En esta página, puede ver las recomendaciones de Azure Advisor, tal como se muestra en la siguiente imagen.

> [!div class="mx-imgBorder"]
> ![Captura de pantalla de la lista de recomendaciones activas para la Excelencia operativa. La lista muestra siete recomendaciones con distintos niveles de prioridad.](media/active-suggestions.png)

## <a name="additional-tips-for-azure-advisor"></a>Sugerencias adicionales para Azure Advisor

- Asegúrese de comprobar las recomendaciones con frecuencia, más de una vez a la semana. Azure Advisor actualiza las recomendaciones activas varias veces al día. La comprobación de las nuevas recomendaciones puede evitar problemas mayores al detectar y resolver los pequeños.

- Intente resolver siempre los problemas con el nivel de prioridad más alto en Azure Advisor. Los problemas de alta prioridad se marcan en rojo. Dejar las recomendaciones de alta prioridad sin resolver puede dar lugar a problemas posteriores.

- Si una recomendación parece menos importante, puede descartarla o posponerla. Para descartar o posponer una recomendación, vaya a la columna **Acción** y cambie el estado del elemento.

- No descarte las recomendaciones hasta que sepa por qué aparecen y esté seguro de que no le afectarán negativamente a usted ni sus usuarios. Seleccione siempre **Más información** para ver cuál es el problema. Si resuelve un problema siguiendo las instrucciones de Azure Advisor, desaparecerá automáticamente de la lista. Es mejor resolver los problemas que posponerlos de forma repetida.

- Cuando surja un problema en Azure Virtual Desktop, compruebe siempre Azure Advisor primero. Azure Advisor le proporcionará instrucciones sobre cómo resolver el problema o, al menos, le indicará un recurso que pueda ayudarle.

## <a name="next-steps"></a>Pasos siguientes

Para información sobre cómo resolver las recomendaciones, consulte [Resolución de las recomendaciones de Azure Advisor](azure-advisor-recommendations.md).
