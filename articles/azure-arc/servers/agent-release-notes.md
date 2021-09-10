---
title: Novedades del agente de servidores habilitados para Azure Arc
description: En este artículo se incluyen las notas de la versión del agente de servidores habilitados para Azure Arc. Muchos de los problemas resumidos incluyen vínculos para obtener detalles adicionales.
ms.topic: conceptual
ms.date: 07/16/2021
ms.openlocfilehash: d53ebd32c870ce8ec26bca7bcb811fbdd45c58b2
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114392380"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>Novedades del agente de servidores habilitados para Azure Arc

El agente de Connected Machine de los servidores habilitados para Azure Arc se mejora de manera continua. Para mantenerse al día de los avances más recientes, este artículo proporciona información acerca de los elementos siguientes:

- Versiones más recientes
- Problemas conocidos
- Corrección de errores

## <a name="july-2021"></a>Julio de 2021

Versión 1.8

### <a name="new-features"></a>Nuevas características

- Confiabilidad mejorada al instalar la extensión del agente de Azure Monitor en los sistemas Red Hat y CentOS
- Se ha agregado el cumplimiento del lado agente de la longitud máxima del nombre de recurso (54 caracteres)
- Mejoras de la directiva de configuración de invitado:
  - Se ha agregado compatibilidad con directivas de configuración de invitado basada en PowerShell en sistemas operativos Linux
  - Se ha agregado compatibilidad para varias asignaciones de la misma directiva de configuración de invitado en el mismo servidor
  - Se ha actualizado PowerShell Core a la versión 7.1 en sistemas operativos Windows

### <a name="fixed"></a>Fijo

- El agente seguirá ejecutándose si no puede escribir eventos de inicio o detención del servicio en el registro de eventos de la aplicación Windows

## <a name="june-2021"></a>Junio de 2021

Versión 1.7

### <a name="new-features"></a>Nuevas características

- Confiabilidad mejorada durante la incorporación:
  - Lógica de reintento mejorada cuando HIMDS no está disponible
  - Ahora, la incorporación continuará en lugar de anularse si no se puede obtener información del sistema operativo
- Confiabilidad mejorada al instalar la extensión del agente de OMS en los sistemas Red Hat y CentOS

## <a name="may-2021"></a>Mayo de 2021

Version 1.6

### <a name="new-features"></a>Nuevas características

- Se ha agregado compatibilidad con SUSE Enterprise Linux 12
- Se ha actualizado el agente de configuración de invitado a la versión 1.26.12.0 para incluir lo siguiente:

   - Las directivas se ejecutan en un proceso independiente.
   - Compatibilidad adicional con la firma de V2 para la validación de extensiones.
   - Actualización secundaria para el registro de datos.

## <a name="april-2021"></a>Abril de 2021

Versión 1.5

### <a name="new-features"></a>Nuevas características

- Se ha agregado compatibilidad con Red Hat Enterprise Linux 8 y CentOS Linux 8.
- Nuevo parámetro `-useStderr` para dirigir la salida de error y detallada a stderr.
- El nuevo parámetro `-json` para dirigir la salida da lugar al formato JSON (cuando se usa con -useStderr).
- Recopile otros metadatos de instancia: fabricante, modelo y el identificador de recurso del clúster (para nodos de Azure Stack HCI).
 
## <a name="march-2021"></a>Marzo de 2021

Versión 1.4

### <a name="new-features"></a>Nuevas características

- Se ha agregado compatibilidad con puntos de conexión privados, que actualmente se encuentra en versión preliminar limitada.
- Lista expandida de códigos de salida para azcmagent.
- Los parámetros de configuración del agente ahora se pueden leer a partir desde un archivo mediante el parámetro `--config`.
- Recopilación de nuevos metadatos de instancia para determinar si Microsoft SQL Server está instalado en el servidor

### <a name="fixed"></a>Fijo

Las comprobaciones de punto de conexión de red son ahora más rápidas.

## <a name="december-2020"></a>Diciembre de 2020

Versión: 1.3

### <a name="new-features"></a>Nuevas características

Se ha agregado compatibilidad con Windows Server 2008 R2 SP1.

### <a name="fixed"></a>Fijo

Problema resuelto que impide que la extensión de script personalizado de Linux se instale correctamente.

## <a name="november-2020"></a>Noviembre de 2020

Versión: 1.2

### <a name="fixed"></a>Fijo

Se resolvió un problema en el que la configuración del proxy se podía perder después de actualizar las distribuciones basadas en RPM.

## <a name="october-2020"></a>Octubre de 2020

Versión: 1.1

### <a name="fixed"></a>Fijo

- Se corrigió un script de proxy para controlar la ubicación alternativa del archivo de unidad de demonio GC.
- Cambios de confiabilidad del agente GuestConfig.
- Compatibilidad del agente GuestConfig con la región US Gov Virginia.
- Los mensajes de informes de la extensión del agente GuestConfig son más detallados en caso de error.

## <a name="september-2020"></a>Septiembre de 2020

Versión: 1.0 (Disponibilidad general)

### <a name="plan-for-change"></a>Plan de cambio

- La compatibilidad con las versiones preliminares de agentes (todas las versiones anteriores a 1.0) se quitará en una actualización de servicio futura.
- Se ha quitado la compatibilidad con el punto de conexión de reserva `.azure-automation.net`. Si tiene un proxy, debe permitir el punto de conexión `*.his.arc.azure.com`.
- Si el agente de Connected Machine está instalado en una máquina virtual hospedada en Azure, las extensiones de máquina virtual no se pueden instalar ni modificar desde el recurso de servidores habilitados para Arc. Esto es así para evitar que se realicen operaciones de extensión en conflicto desde los recursos **Microsoft.Compute** y **Microsoft.HybridCompute** de la máquina virtual. Use el recurso **Microsoft.Compute** de la máquina para todas las operaciones de extensión.
- El nombre del proceso Configuración de invitado ha cambiado, de *gcd* a *gcad* en Linux y de *gcservice* a *gcarcservice* en Windows.

### <a name="new-features"></a>Nuevas características

- Se ha agregado la opción `azcmagent logs` para recopilar información de soporte técnico.
- Se ha agregado la opción `azcmagent license` para mostrar el CLUF.
- Se ha agregado la opción `azcmagent show --json` al estado del agente de salida en un formato fácilmente analizable.
- Se ha agregado una marca en la salida `azcmagent show` para indicar si el servidor está en una máquina virtual hospedada en Azure.
- Se ha agregado la opción `azcmagent disconnect --force-local-only` para permitir el restablecimiento del estado del agente local cuando no se puede acceder al servicio de Azure.
- Se ha agregado la opción `azcmagent connect --cloud` para admitir otras nubes. En esta versión, solo Azure es compatible con el servicio en el momento de la versión del agente.
- El agente se ha localizado en idiomas compatibles con Azure.

### <a name="fixed"></a>Fijo

- Mejoras en la comprobación de conectividad.
- Se ha corregido la incidencia con la configuración del servidor proxy que se había perdido al actualizar el agente en Linux.
- Se han resuelto incidencias al intentar instalar el agente en el servidor que ejecuta Windows Server 2012 R2.
- Mejoras en la fiabilidad de la instalación de extensiones

## <a name="august-2020"></a>Agosto de 2020

Versión: 0,11

- Esta versión ha anunciado anteriormente la compatibilidad con Ubuntu 20.04. Dado que algunas extensiones de máquina virtual de Azure no admiten Ubuntu 20.04, se está quitando la compatibilidad con esta versión de Ubuntu.

- Mejoras de la confiabilidad para las implementaciones de extensiones.

### <a name="known-issues"></a>Problemas conocidos

Si usa una versión anterior del agente de Linux y la ha configurado para usar un servidor proxy, debe volver a configurar los valores del servidor proxy después de la actualización. Para ello, ejecute `sudo azcmagent_proxy add http://proxyserver.local:83`.

## <a name="next-steps"></a>Pasos siguientes

- Antes de evaluar o habilitar los servidores habilitados para Arc en varias máquinas híbridas, consulte la [introducción al agente de Connected Machine](agent-overview.md) para conocer los requisitos y detalles técnicos del agente y los métodos de implementación.

- Examine la [guía de planeamiento e implementación](plan-at-scale-deployment.md) para planear la implementación de servidores habilitados para Azure Arc a cualquier escala e implementar la administración y supervisión centralizadas.
