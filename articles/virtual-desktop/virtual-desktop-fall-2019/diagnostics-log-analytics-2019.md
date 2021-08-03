---
title: 'Análisis de registros de diagnóstico de Azure Virtual Desktop (clásico): Azure'
description: Uso del análisis de registros con la característica de diagnóstico de Azure Virtual Desktop (clásico).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 5c1bdadf92d72ebec7ffa122b3c49c5878e2d2b8
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111745176"
---
# <a name="use-log-analytics-for-the-diagnostics-feature-in-azure-virtual-desktop-classic"></a>Uso de Log Analytics para la característica de diagnóstico en Azure Virtual Desktop (clásico)

>[!IMPORTANT]
>Este contenido se aplica a Azure Virtual Desktop (clásico), que no admite objetos de Azure Resource Manager. Si está intentando administrar objetos de Azure Virtual Desktop para Azure Resource Manager, consulte [este artículo](../diagnostics-log-analytics.md).

Azure Virtual Desktop ofrece una característica de diagnóstico que permite al administrador detectar problemas a través de una única interfaz. Esta característica registra información de diagnóstico cada vez que alguien que tiene asignado el rol de Azure Virtual Desktop usa el servicio. Cada registro contiene información sobre el rol de Azure Virtual Desktop implicado en la actividad, los mensajes de error que se muestran durante la sesión, la información del inquilino y la información del usuario. La característica de diagnóstico crea registros de actividad para las acciones de usuario y administrativas. Cada registro de actividad se divide en tres categorías principales:

- Actividades de suscripción a fuente: cuando un usuario intenta conectarse a su fuente mediante aplicaciones de Escritorio remoto de Microsoft.
- Actividades de conexión: cuando un usuario intenta conectarse a un escritorio o a RemoteApp mediante aplicaciones de Escritorio remoto de Microsoft.
- Actividades de administración: cuando un administrador realiza operaciones de administración en el sistema, como crear grupos de hosts, asignar usuarios a grupos de aplicaciones y crear asignaciones de roles.

Las conexiones que no lleguen a Azure Virtual Desktop no aparecerán en los resultados de diagnóstico, ya que el propio servicio de rol de diagnóstico forma parte de Azure Virtual Desktop. Se pueden producir problemas de conexión a Azure Virtual Desktop si el usuario está experimentando problemas de conectividad de red.

## <a name="why-you-should-use-log-analytics"></a>Por qué debería usar Log Analytics

Le recomendamos que use Log Analytics para analizar los datos de diagnóstico en el cliente de Azure que van más allá de la solución de problemas de un solo usuario. Dado que puede extraer los contadores de rendimiento de la máquina virtual en Log Analytics, tiene una herramienta para recopilar información para la implementación.

## <a name="before-you-get-started"></a>Antes de comenzar

Para poder usar Log Analytics con la característica de diagnóstico, deberá [crear un área de trabajo](../../azure-monitor/vm/quick-collect-windows-computer.md#create-a-workspace).

Después de crear el área de trabajo, siga las instrucciones del artículo [Conexión de equipos Windows a Azure Monitor](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key) para obtener la siguiente información:

- Identificador del área de trabajo
- Clave principal del área de trabajo

Necesitará esta información posteriormente en el proceso de instalación.

## <a name="push-diagnostics-data-to-your-workspace"></a>Inserción de datos de diagnóstico en el área de trabajo

Puede insertar datos de diagnóstico desde el inquilino de Azure Virtual Desktop a Log Analytics para el área de trabajo. Puede configurar esta característica directamente cuando cree el inquilino por primera vez. Para ello, vincule el área de trabajo a su inquilino. También puede configurarlo más adelante con un inquilino existente.

Para vincular el inquilino al área de trabajo de Log Analytics mientras configura el nuevo inquilino, ejecute el siguiente cmdlet a fin de iniciar sesión en Azure Virtual Desktop con su cuenta de usuario de TenantCreator:

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

Si va a vincular un inquilino existente en lugar de un nuevo inquilino, ejecute este cmdlet en su lugar:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

Deberá ejecutar estos cmdlets para cada inquilino que quiera vincular a Log Analytics.

>[!NOTE]
>Si no quiere vincular el área de trabajo de Log Analytics cuando crea un inquilino, ejecute el cmdlet `New-RdsTenant` en su lugar.

## <a name="cadence-for-sending-diagnostic-events"></a>Cadencia para enviar eventos de diagnóstico

Los eventos de diagnóstico se envían a Log Analytics cuando se completan.

## <a name="example-queries"></a>Consultas de ejemplo

En las consultas de ejemplo siguientes se muestra cómo la característica de diagnóstico genera un informe para las actividades más frecuentes en el sistema:

En este primer ejemplo se muestran las actividades de conexión que inician los usuarios con clientes de escritorio remoto compatibles:

```powershell
WVDActivityV1_CL

| where Type_s == "Connection"

| join kind=leftouter (

    WVDErrorV1_CL

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g 

| join  kind=leftouter (

    WVDCheckpointV1_CL

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g

|project-away ActivityId_g, ActivityId_g1
```

En esta consulta de ejemplo siguiente se muestran las actividades de administración por parte de los administradores de los inquilinos:

```powershell
WVDActivityV1_CL

| where Type_s == "Management"

| join kind=leftouter (

    WVDErrorV1_CL

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g 

| join  kind=leftouter (

    WVDCheckpointV1_CL

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g

|project-away ActivityId_g, ActivityId_g1
```

## <a name="stop-sending-data-to-log-analytics"></a>Detención del envío de datos a Log Analytics

Para detener el envío de datos de un inquilino existente a Log Analytics, ejecute el siguiente cmdlet y establezca cadenas vacías:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

Tendrá que ejecutar este cmdlet para cada inquilino del que quiera dejar de enviar datos.

## <a name="next-steps"></a>Pasos siguientes

Para revisar los escenarios de error comunes que la característica de diagnóstico puede identificar automáticamente, consulte [Identificación y diagnóstico de problemas](diagnostics-role-service-2019.md#common-error-scenarios).
