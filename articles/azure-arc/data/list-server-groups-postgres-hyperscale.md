---
title: Enumeración de los grupos de servidores de Hiperescala de PostgreSQL habilitados para Azure Arc creados en un controlador de datos de Azure Arc
description: Enumeración de los grupos de servidores de Hiperescala de PostgreSQL habilitados para Azure Arc creados en un controlador de datos de Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 7254ed303e45c69f291aa5c7a06f63390aaed162
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750266"
---
# <a name="list-the-azure-arc-enabled-postgresql-hyperscale-server-groups-created-in-an-azure-arc-data-controller"></a>Enumeración de los grupos de servidores de Hiperescala de PostgreSQL habilitados para Azure Arc creados en un controlador de datos de Azure Arc

En este artículo se explica cómo se puede recuperar la lista de grupos de servidores creados en el controlador de datos de Arc.

Para recuperar esta lista, use cualquiera de los métodos siguientes una vez que esté conectado al controlador de datos de Arc:

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-cli-with-azure-cli-extension-az"></a>Desde la CLI con la extensión de la CLI de Azure (az)

El formato general del comando es el siguiente:
```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
```

Devolverá un resultado como este:
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    2
postgres02  Ready    2
```
Para obtener información detallada acerca de los parámetros disponibles para este comando, ejecute lo siguiente:
```azurecli
az postgres arc-server list --help
```

## <a name="from-cli-with-kubectl"></a>Desde la CLI con kubectl
Ejecute uno de los comandos siguientes:

**Para enumerar los grupos de servidores con independencia de la versión de Postgres, ejecute lo siguiente:**
```console
kubectl get postgresqls
```
Devolverá un resultado como este:
```console
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      51s
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d
```

**Para enumerar los grupos de servidores de una versión específica de Postgres, ejecute lo siguiente:**
```console
kubectl get postgresql-12
```

Para enumerar los grupos de servidores que ejecutan la versión 11 de Postgres, reemplace _postgresql-12_ por _postgresql-11_.

## <a name="next-steps"></a>Pasos siguientes:

* [Lea el artículo sobre cómo obtener los puntos de conexión y formar las cadenas de conexión para conectarse a su grupo de servidores](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md).
* [Lea el artículo sobre mostrar la configuración de un grupo de servidores de Hiperescala de PostgreSQL habilitados para Azure Arc](show-configuration-postgresql-hyperscale-server-group.md).
