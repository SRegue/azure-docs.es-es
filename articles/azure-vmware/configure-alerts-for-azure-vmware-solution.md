---
title: Configuración de alertas y trabajo con métricas en Azure VMware Solution
description: Aprenda a usar alertas para recibir notificaciones. Conozca también cómo trabajar con métricas para obtener información más detallada sobre la nube privada de Azure VMware Solution.
ms.topic: how-to
ms.date: 07/23/2021
ms.openlocfilehash: 718838d5335ff10200fc41029a6431a96552894b
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2021
ms.locfileid: "114653582"
---
# <a name="configure-azure-alerts-in-azure-vmware-solution"></a>Configuración de alertas de Azure en Azure VMware Solution 

En este artículo va a aprender a configurar [grupos de acciones de Azure](../azure-monitor/alerts/action-groups.md) en [alertas de Microsoft Azure](../azure-monitor/alerts/alerts-overview.md) para recibir notificaciones de eventos desencadenados que defina. También va a aprender a usar [métricas de Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md) para obtener información más detallada sobre la nube privada de Azure VMware Solution.

>[!NOTE]
>Los incidentes que afectan a la disponibilidad de un host de Azure VMware Solution y su restauración correspondiente se envían automáticamente al administrador de cuenta, al administrador de servicios (permisos clásicos), a los coadministradores (permisos clásicos) y a los propietarios (rol RBAC) de las suscripciones que contienen nubes privadas de Azure VMware Solution.

## <a name="supported-metrics-and-activities"></a>Métricas y actividades admitidas

Las siguientes métricas son visibles por medio de métricas de Azure Monitor.

| **Nombre de señal**                                                         | **Tipo de señal** | **Servicio de supervisión** |
|-------------------------------------------------------------------------|-----------------|---------------------|
| Capacidad total del disco de almacén de datos                                           | Métrica          | Plataforma            |
| Porcentaje de disco del almacén de datos empleado                                          | Métrica          | Plataforma            |
| Porcentaje de CPU                                                          | Métrica          | Plataforma            |
| Promedio de memoria efectiva                                                | Métrica          | Plataforma            |
| Promedio de sobrecarga de la memoria                                                 | Métrica          | Plataforma            |
| Promedio de memoria total                                                    | Métrica          | Plataforma            |
| Promedio de uso de memoria                                                    | Métrica          | Plataforma            |
| Disco de almacén de datos empleado                                                     | Métrica          | Plataforma            |
| Todas las operaciones administrativas                                           | Registro de actividad    | Administrativo      |
| Registrar proveedor de recursos de Microsoft.AVS (Microsoft.AVS/privateClouds) | Registro de actividad    | Administrativo      |
| Crear o actualizar una nube privada (Microsoft.AVS/privateClouds)          | Registro de actividad    | Administrativo      |
| Eliminar una nube privada (Microsoft.AVS/privateClouds)                    | Registro de actividad    | Administrativo      |

## <a name="configure-an-alert-rule"></a>Configuración de una regla de alerta
1. En la nube privada de Azure VMware Solution, seleccione **Supervisión** > **Alertas** y luego **Nueva regla de alerta**.
 
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png" alt-text="Captura de pantalla que muestra dónde configurar una regla de alerta en la nube privada de Azure VMware Solution." lightbox="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png":::

   Se abre una nueva pantalla de configuración donde se puede:
   - Definir el ámbito
   - Configurar una condición
   - Configurar el grupo de acciones
   - Definir los detalles de la regla de alerta
    
   :::image type="content" source="../devtest-labs/media/activity-logs/create-alert-rule-done.png" alt-text="Captura de pantalla que muestra la ventana Crear regla de alertas." lightbox="../devtest-labs/media/activity-logs/create-alert-rule-done.png":::

1. En **Ámbito**, seleccione el recurso de destino que quiere supervisar. De manera predeterminada, se ha definido la nube privada de Azure VMware Solution desde la que se ha abierto el menú Alertas.

1. En **Condición**, seleccione **Agregar condición** y, en la ventana que se abre, seleccione la señal que quiere crear para la regla de alerta. 

   En el ejemplo se ha seleccionado **Porcentaje de disco del almacén de datos empleado**, que es relevante desde una perspectiva del [Acuerdo de Nivel de Servicio de Azure VMware Solution](https://aka.ms/avs/sla). 

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/configure-signal-logic-options.png" alt-text="Captura de pantalla que muestra la ventana de configuración de la lógica de señal con indicaciones para crearla para la regla de alertas."::: 

1. Defina la lógica que va a desencadenar la alerta y seleccione **Listo**. 

   En el ejemplo solo se han ajustado el **Umbral** y la **Frecuencia de evaluación**. 
   
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/define-alert-logic-threshold.png" alt-text="Captura de pantalla que muestra el umbral, el operador, el tipo de agregación y la granularidad, el valor del umbral y la frecuencia de evaluación de la lógica de alerta de señal."::: 

1. En **Acciones**, seleccione **Agregar grupos de acciones**. El grupo de acciones define *cómo* se recibe la notificación y *quién* la recibe.   Puede recibir notificaciones por correo electrónico, SMS, [notificaciones de inserción de Azure Mobile App](https://azure.microsoft.com/features/azure-portal/mobile-app/) o mensajes de voz.

1. Puede seleccionar un grupo de acciones existente o seleccionar **Crear grupo de acciones** para crear uno nuevo.
 
1. En la ventana que se abre, en la pestaña **Aspectos básicos**, asigne un nombre y un nombre para mostrar al grupo de acciones.

1. Seleccione la pestaña **Notificaciones**, **Tipo de notificación** y **Nombre**. Después, seleccione **Aceptar**.

   El ejemplo se basa en la notificación por correo electrónico.

   :::image type="content" source="../azure-monitor/alerts/media/action-groups/action-group-2-notifications.png" alt-text="Captura de pantalla que muestra las opciones de correo electrónico, mensaje SMS, inserción y voz para la alerta." lightbox="../azure-monitor/alerts/media/action-groups/action-group-2-notifications.png":::    

1. (Opcional) Configure las **Acciones** si quiere tomar medidas proactivas y recibir notificaciones sobre el evento. Seleccione un **Tipo de acción** disponible y luego **Revisar y crear**. 

   - **Runbooks de Automation**: para automatizar tareas basadas en alertas.

   - **Azure Functions**: para la ejecución personalizada de código sin servidor controlado por eventos

   - **ITSM**: para la integración con un proveedor de servicios como ServiceNow a fin de crear un vale

   - **Aplicación lógica**: para una orquestación de flujo de trabajo más compleja

   - **Webhooks**: para desencadenar un proceso en otro servicio


1. En **Detalles de la regla de alertas**, proporcione un nombre, una descripción, un grupo de recursos para almacenar la regla de alerta y la gravedad. Luego seleccione **Crear regla de alertas**.
   
   La regla de alerta es visible y se puede administrar desde Azure Portal.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png" alt-text="Captura de pantalla que muestra la nueva regla de alerta en la ventana Reglas." lightbox="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png":::      

   En cuanto una métrica alcanza el umbral definido en una regla de alerta, el menú **Alertas** se actualiza y se hace visible.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png" alt-text="Captura de pantalla que muestra la alerta después de alcanzar el umbral definido en la regla de alertas." lightbox="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png":::     

   En función del grupo de acciones configurado, se recibe una notificación por el medio configurado. En el ejemplo se ha configurado el correo electrónico.
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/alert-notification.png" alt-text="Captura de pantalla de una alerta de Azure Monitor con la cadena de error y la fecha y la hora en que se ha desencadenado el evento."::: 

## <a name="work-with-metrics"></a>Trabajo con métricas

1. En la nube privada de Azure VMware Solution, seleccione **Supervisión** > **Métricas**. Luego seleccione la métrica que quiere en la lista desplegable.
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png" alt-text="Captura de pantalla que muestra la ventana Métricas y un foco en la lista desplegable Métrica." lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png":::   

1. Puede cambiar los parámetros del diagrama, como el **Intervalo de tiempo** o la **Granularidad de tiempo**. 

   Otras opciones son:
   - **Obtener detalles de los registros** y consultar los datos en el área de trabajo de Log Analytics relacionada
   - **Anclar este diagrama** a un panel de Azure para mayor comodidad.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png" alt-text="Captura de pantalla que muestra las opciones de intervalo de tiempo y granularidad de tiempo de la métrica." lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png":::  
 
 
## <a name="next-steps"></a>Pasos siguientes

Ahora que ha configurado una regla de alerta para la nube privada de Azure VMware Solution, puede que quiera obtener más información sobre:
- [Métricas de Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md)
- [Alertas de Azure Monitor](../azure-monitor/alerts/alerts-overview.md)
- [Grupos de acciones de Azure](../azure-monitor/alerts/action-groups.md)

También puede continuar con una de las otras guías de procedimientos de [Azure VMware Solution](index.yml).
