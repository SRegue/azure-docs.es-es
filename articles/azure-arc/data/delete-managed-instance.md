---
title: Eliminación de una instancia de SQL Managed Instance habilitada para Azure Arc
description: Eliminación de una instancia de SQL Managed Instance habilitada para Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 19f5befde22ed7b16302b7da5df313c476b47194
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733563"
---
# <a name="delete-azure-arc-enabled-sql-managed-instance"></a>Eliminación de una instancia de SQL Managed Instance habilitada para Azure Arc
En este artículo se describe cómo se puede eliminar una instancia de SQL Managed Instance habilitada para Azure Arc.


## <a name="view-existing-azure-arc-enabled-sql-managed-instances"></a>Visualización de instancias existentes de SQL Managed Instance habilitadas para Azure Arc
Para ver las instancias creadas de SQL Managed Instance, ejecute el siguiente comando:

```azurecli
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
```

La salida debe tener un aspecto similar al siguiente:

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
demo-mi 1/1         10.240.0.4:32023  Ready
```

## <a name="delete-a-azure-arc-enabled-sql-managed-instance"></a>Eliminación de una instancia de SQL Managed Instance habilitada para Azure Arc
Para eliminar una instancia de SQL Managed Instance, ejecute el siguiente comando:

```azurecli
az sql mi-arc delete -n <NAME_OF_INSTANCE> --k8s-namespace <namespace> --use-k8s
```

La salida debe tener un aspecto similar al siguiente:

```azurecli
# az sql mi-arc delete -n demo-mi --k8s-namespace <namespace> --use-k8s
Deleted demo-mi from namespace arc
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>Reclamación de las notificaciones de volumen persistente (PVC) de Kubernetes

La eliminación de una instancia de SQL Managed Instance no elimina sus [PVC](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) asociadas. es así por diseño. La intención es ayudar al usuario a tener acceso a los archivos de base de datos en caso de que la eliminación de la instancia fuera accidental. La eliminación de las PVC no es obligatoria. Sin embargo, se recomienda. Si no reclama estas PVC, finalmente terminará con errores, ya que el clúster de Kubernetes pensará que se está quedando sin espacio en disco. Para reclamar las PVC, realice los pasos siguientes:

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. Enumerar las PVC del grupo de servidores que ha eliminado
Para enumerar las PVC, ejecute el siguiente comando:
```console
kubectl get pvc
```

En el siguiente ejemplo, observe las PVC de las instancias de SQL Managed Instance que ha eliminado.
```console
# kubectl get pvc -n arc

NAME                    STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
data-demo-mi-0        Bound     pvc-1030df34-4b0d-4148-8986-4e4c20660cc4   5Gi        RWO            managed-premium   13h
logs-demo-mi-0        Bound     pvc-11836e5e-63e5-4620-a6ba-d74f7a916db4   5Gi        RWO            managed-premium   13h
```

### <a name="2-delete-each-of-the-pvcs"></a>2. Eliminar cada PVC
Elimine las PVC de datos y de registro para cada una de las instancias de SQL Managed Instance que ha eliminado.
El formato general de este comando es: 
```console
kubectl delete pvc <name of pvc>
```

Por ejemplo:
```console
kubectl delete pvc data-demo-mi-0 -n arc
kubectl delete pvc logs-demo-mi-0 -n arc
```

Cada uno de estos comandos kubectl confirmará la eliminación correcta del PVC. Por ejemplo:
```console
persistentvolumeclaim "data-demo-mi-0" deleted
persistentvolumeclaim "logs-demo-mi-0" deleted
```
  

> [!NOTE]
> Tal y como se indicó, si no elimina las PVC podría llevar al clúster de Kubernetes a una situación en la que se produzcan errores. Algunos de estos errores pueden incluir la incapacidad de crear, leer, actualizar y eliminar recursos de la API de Kubernetes, o bien de ejecutar comandos como `az arcdata dc export`, ya que los pods del controlador se pueden expulsar de los nodos de Kubernetes debido a este problema de almacenamiento (el comportamiento normal de Kubernetes).
>
> Por ejemplo, puede ver mensajes en los registros similares a:  
> - Anotaciones: microsoft.com/ignore-pod-health: true  
> - Estado:         Con error  
> - Motivo:         Expulsado  
> - Mensaje:        El nodo tenía poco recursos: almacenamiento efímero. El controlador del contenedor utilizaba 16 372 Ki, que supera la solicitud de 0.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre las [características y funcionalidades de SQL Managed Instance habilitado para Azure Arc](managed-instance-features.md)

[Comienzo con la creación de un controlador de datos](create-data-controller.md)

¿Ya ha creado un controlador de datos? [Creación de una instancia de SQL Managed Instance habilitada para Azure Arc](create-sql-managed-instance.md)