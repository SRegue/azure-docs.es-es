---
title: 'Equilibrio de carga del grupo de hosts de Azure Virtual Desktop: Azure'
description: Obtenga información sobre los métodos de equilibrio de carga del grupo de hosts para un entorno de Azure Virtual Desktop.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 54cbd03283814fd21a95dfe7578173f3481c4cd8
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111757614"
---
# <a name="host-pool-load-balancing-methods"></a>Métodos de equilibrio de carga para un grupo host

>[!IMPORTANT]
>Este contenido se aplica a Azure Virtual Desktop con objetos de Azure Resource Manager. Si usa Azure Virtual Desktop (clásico) sin objetos de Azure Resource Manager, consulte [este artículo](./virtual-desktop-fall-2019/host-pool-load-balancing-2019.md).

Azure Virtual Desktop admite dos métodos de equilibrio de carga. Cada método determina qué host de sesión hospedará la sesión de un usuario cuando se conecte a un recurso de un grupo de hosts.

Están disponibles los siguientes métodos de equilibrio de carga en Azure Virtual Desktop:

- El equilibrio de carga en amplitud le permite distribuir uniformemente las sesiones de usuario entre los hosts de sesión de un grupo de hosts.
- El equilibrio de carga en profundidad le permite saturar un host de sesión con las sesiones de usuario de un grupo de hosts. Una vez que la primera sesión alcanza su umbral de límite de sesión, el equilibrador de carga dirige las nuevas conexiones de usuario al siguiente host de sesión del grupo de hosts hasta que alcanza su límite, y así sucesivamente.

Cada grupo de hosts solo puede configurar un tipo de equilibrio de carga específico. Sin embargo, ambos métodos de equilibrio de carga comparten los comportamientos siguientes, independientemente del grupo de hosts en el que estén:

- Si un usuario ya tiene una sesión en el grupo de hosts y se vuelve a conectar a esa sesión, el equilibrador de carga lo redirigirá correctamente al host de sesión con su sesión existente. Este comportamiento se aplica incluso si la propiedad AllowNewConnections de ese host de sesión se establece en False.
- Si un usuario ya no tiene una sesión en el grupo de hosts, el equilibrador de carga no tendrá en cuenta los hosts de sesión cuya propiedad AllowNewConnections esté establecida en False durante el equilibrio de carga.

## <a name="breadth-first-load-balancing-method"></a>Método de equilibrio de carga en amplitud

El método de equilibrio de carga en amplitud permite distribuir las conexiones de usuario para optimizar este escenario. Este método es ideal para las organizaciones que desean proporcionar la mejor experiencia a los usuarios se conectan a su entorno de escritorio virtual agrupado.

El método en amplitud consulta primero los hosts de sesión que permiten nuevas conexiones. Después, el método selecciona un host de sesión aleatoriamente de la mitad del conjunto de hosts de sesión con el menor número de sesiones. Por ejemplo, si hay nueve máquinas con las sesiones 11, 12, 13, 14, 15, 16, 17, 18 y 19, una nueva sesión que cree no irá automáticamente a la primera máquina. En su lugar, puede ir a cualquiera de las cinco primeras máquinas con el menor número de sesiones (11, 12, 13, 14 y 15).

## <a name="depth-first-load-balancing-method"></a>Método de equilibrio de carga en profundidad

El método de equilibrio de carga en profundidad permite saturar un host de sesión a la vez para optimizar este escenario. Este método es ideal para las organizaciones preocupadas por los costos que desean tener un control más pormenorizado sobre el número de máquinas virtuales que han asignado a un grupo de hosts.

El método en profundidad consulta primero los hosts de sesión que permiten nuevas conexiones y que aún no han excedido su límite máximo de la sesiones. Luego, selecciona el host de sesión con el número máximo de sesiones. Si hay un empate, el método selecciona el primer host de sesión de la consulta.

>[!IMPORTANT]
>El algoritmo de equilibrio de carga en profundidad distribuye las sesiones a los hosts de sesión en función del límite máximo de hosts de sesión. Este parámetro es necesario cuando se usa el algoritmo de equilibrio de carga en profundidad. Para obtener la mejor experiencia de usuario posible, asegúrese de cambiar el parámetro de límite máximo de hosts de sesión al número que mejor se adapte a su entorno.