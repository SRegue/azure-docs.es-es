---
title: Archivo de inclusión
description: archivo de inclusión
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 09f61060f9a76036f5e2d58512ddde9248402bbd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638977"
---
| Nombres de dominio                                          | Puertos de salida | Descripción                |
| ----------------------------------------------------- | -------------- | ---------------------------|
| Nube pública: `*.servicebus.windows.net` <br> Azure Government: `*.servicebus.usgovcloudapi.net` <br> China: `*.servicebus.chinacloudapi.cn`   | 443            | Lo necesita el entorno de ejecución de integración autohospedado para la creación interactiva. |
| Nube pública: `{datafactory}.{region}.datafactory.azure.net`<br> o bien `*.frontend.clouddatahub.net` <br> Azure Government: `{datafactory}.{region}.datafactory.azure.us` <br> China: `{datafactory}.{region}.datafactory.azure.cn` | 443            | El entorno de ejecución de integración autohospedado lo necesita para conectarse al servicio Data Factory. <br>En el caso las nuevas instancias de Data Factory creadas en la nube pública, busque el nombre de dominio completo de la clave del entorno de ejecución de integración autohospedado que se encuentra en el formato {datafactory}.{region}.datafactory.azure.net. En el caso de Data Factory anterior, si no ve el FQDN en la clave del entorno de ejecución de integración autohospedado, use *.frontend.clouddatahub.net en su lugar. |
| `download.microsoft.com`    | 443            | Lo necesita el entorno de ejecución de integración autohospedado para descargar las actualizaciones. Si ha deshabilitado la actualización automática, puede omitir la configuración de este dominio. |
| Dirección URL de Key Vault | 443           | Requerido por Azure Key Vault si almacena la credencial en Key Vault. |
