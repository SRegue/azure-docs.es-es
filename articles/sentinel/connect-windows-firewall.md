---
title: Conexión de datos de Firewall de Windows Defender a Azure Sentinel | Microsoft Docs
description: Habilite el conector de Firewall de Windows en Azure Sentinel para transmitir fácilmente los eventos de firewall desde las máquinas Windows que tienen instalados agentes de Log Analytics.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2020
ms.author: yelevin
ms.openlocfilehash: 3af0977880adf900171dad6c518f0267a1d9d2a1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780245"
---
# <a name="connect-windows-defender-firewall-with-advanced-security-to-azure-sentinel"></a>Conexión de Firewall de Windows Defender con seguridad avanzada a Azure Sentinel

El conector [Firewall de Windows Defender con seguridad avanzada](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) permite que Azure Sentinel ingiera de manera sencilla registros de Firewall de Windows Defender con seguridad avanzada desde cualquier máquina Windows del área de trabajo. Esta conexión le permite ver y analizar los eventos de Firewall de Windows en los libros, para usarlos en la creación de alertas personalizadas y para incorporarlos en sus investigaciones de seguridad, lo que le proporciona más información sobre la red de la organización y mejora las funcionalidades de las operaciones de seguridad. 

La solución recopila eventos de firewall de Windows de las máquinas de Windows en las que está instalado un agente de Log Analytics. 

> [!NOTE]
> - Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.
>
> - Si las alertas de Azure Defender procedentes de Azure Security Center ya están recopiladas en el área de trabajo de Azure Sentinel, no es necesario habilitar la solución Firewall de Windows mediante este conector. Sin embargo, si la ha habilitado, no hará que se generen datos duplicados.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]


## <a name="prerequisites"></a>Prerrequisitos

- Debe tener permisos de lectura y escritura en el área de trabajo a la que se conectan las máquinas que quiere supervisar.

- Debe tener asignado el rol **Colaborador de Log Analytics** en la solución SecurityInsights de esa área de trabajo, además de cualquier rol de **Azure Sentinel**. [Más información](../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="enable-the-connector"></a>Habilitar el conector 

1. En el menú de navegación del portal de Azure Sentinel, seleccione **Conectores de datos**.

1. Seleccione **Firewall de Windows** en la galería de conectores y haga clic en **Open connector page** (Open connector page).

### <a name="instructions-tab"></a>Pestaña Instrucciones

- **Si las máquinas Windows están en Azure:**

    1. Seleccione **Install agent on Azure Windows Virtual Machine** (Instalar el agente en la máquina virtual Windows de Azure).

    1. Haga clic en el vínculo **Download & install agent for Azure Windows Virtual machines >** (Descargar e instalar el agente para máquinas virtuales Windows de Azure >) que aparece.

    1. En la lista **Máquinas virtuales**, seleccione la máquina Windows que quiera transmitir a Azure Sentinel. (Puede seleccionar **Windows** en el filtro de la columna de SO para asegurarse de que solo se muestran las máquinas virtuales Windows).

    1. En la ventana de la máquina virtual que se abre, haga clic en **Connect**(Conectar).

    1. Vuelva al panel **Virtual Machines** (Máquinas virtuales) y repita los dos pasos anteriores para las demás máquinas virtuales que quiere conectar. Cuando haya terminado, vuelva al panel **Firewall de Windows**.

- **Si la máquina Windows no es una máquina virtual de Azure:**

    1. Seleccione **Install agent on non-Azure Windows Machine** (Instalar el agente en la máquina virtual Windows que no es de Azure).

    1. Haga clic en el vínculo **Download & install agent for non-Azure Windows Virtual machines >** (Descargar e instalar el agente para máquinas virtuales Windows que no son de Azure >) que aparece.

    1. En el panel **Agents management** (Administración de agentes), seleccione **Download Windows Agent (64 bit)** (Descargar agente de Windows [64 bits]) o **Download Windows Agent (32 bit)** (Descargar agente de Windows [32 bits]), según sea necesario.

    1. Copie las cadenas **Id. de área de trabajo**, **Clave principal** y **Clave secundaria** en un archivo de texto. Copie ese archivo y el archivo de instalación descargado en la máquina Windows. Ejecute el archivo de instalación y, cuando se lo solicite, escriba las cadenas clave y el id. en el archivo de texto durante la instalación.

    1. Vuelva al panel **Firewall de Windows**.

1. Haga clic en **Install solution** (Instalar solución).

### <a name="next-steps-tab"></a>Pestaña Pasos siguientes

- Consulte los libros y ejemplos de consultas disponibles recomendados que se incluyen en el conector de datos **Firewall de Windows** para obtener información sobre los datos de registro de Firewall de Windows.

- Para consultar datos de Firewall de Windows en **Registros**, escriba **WindowsFirewall** en la ventana de consulta.

## <a name="validate-connectivity"></a>Validar conectividad
 
Dado que los registros del Firewall de Windows se envían a Azure Sentinel solo cuando el archivo de registro local alcanza la capacidad, lo más probable es que dejar el registro con su tamaño predeterminado de 4096 KB genere una latencia de colección alta. Puede reducir la latencia si reduce el tamaño del archivo de registro. Para obtener más información, vea [cómo configurar el registro de Windows Firewall](/windows/security/threat-protection/windows-firewall/configure-the-windows-firewall-log).

> [!NOTE]
>
> - Aunque los tamaños de registro más pequeños reducirán la latencia de recopilación, también podrían afectar negativamente al rendimiento de la máquina local.
> 
> - La configuración de recopilación de datos requiere un mínimo de 1000 nuevas líneas en el archivo de registro para poder recopilar los datos. Por lo tanto, el tamaño del archivo de registro debe establecerse en no menos de 100 (cien) KB, ya que esto garantizará la acumulación de 1000 líneas.

## <a name="next-steps"></a>Pasos siguientes
En este documento, ha aprendido a conectar Firewall de Windows a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
