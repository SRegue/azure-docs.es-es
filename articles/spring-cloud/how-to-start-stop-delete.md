---
title: Inicio, detención y eliminación de una aplicación de Azure Spring Cloud | Microsoft Docs
description: Inicio, detención y eliminación de una aplicación de Azure Spring Cloud
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 73fcf419438f24c784514c723397049d50dd87be
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015444"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Inicio, detención y eliminación de una aplicación de Azure Spring Cloud

**Este artículo se aplica a:** ✔️ Java ✔️ C#

En esta guía se explica cómo cambiar el estado de una aplicación en Azure Spring Cloud mediante Azure Portal o la CLI de Azure.

## <a name="using-the-azure-portal"></a>Uso de Azure Portal

Una vez que implemente una aplicación, puede iniciarla, detenerla y eliminarla desde Azure Portal.

1. Vaya a la instancia de servicio de Azure Spring Cloud en Azure Portal.
1. Seleccione la pestaña **Panel de la aplicación**.
1. Seleccione la aplicación cuyo estado desea cambiar.
1. En la página **Información general** de esa aplicación, seleccione **Iniciar/Detener**, **Reiniciar** o **Eliminar**.

## <a name="using-the-azure-cli"></a>Uso de la CLI de Azure

> [!NOTE]
> Puede usar parámetros opcionales y configurar los valores predeterminados con la CLI de Azure. Para más información sobre la CLI de Azure, lea [la documentación de referencia](/cli/azure/spring-cloud).

En primer lugar, instale la extensión de Azure Spring Cloud para la CLI de Azure de la manera siguiente:

```azurecli
az extension add --name spring-cloud
```

A continuación, seleccione cualquiera de estas operaciones de la CLI de Azure:

* Para iniciar la aplicación:

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Para detener la aplicación:

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Para reiniciar la aplicación:

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Para eliminar la aplicación:

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
