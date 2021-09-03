---
title: Habilitación de Azure Site Recovery en las máquinas virtuales mediante Azure Policy
description: Aprenda a habilitar la compatibilidad con Azure Policy para proteger las máquinas virtuales mediante Azure Site Recovery.
author: rishjai-msft
ms.author: rishjai
ms.topic: how-to
ms.date: 07/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 1c936df9ffb467d732e0c07651e7c6fb31f28b5c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739045"
---
# <a name="using-policy-with-azure-site-recovery-public-preview"></a>Uso de Azure Policy con Azure Site Recovery (versión preliminar pública)

En este artículo se describe cómo configurar [Azure Site Recovery](./site-recovery-overview.md) para los recursos mediante Azure Policy. [Azure Policy](../governance/policy/overview.md) ayuda a aplicar ciertas reglas de negocio a los recursos de Azure y a evaluar el cumplimiento de esos recursos.

## <a name="disaster-recovery-with-azure-policy"></a>Recuperación ante desastres con Azure Policy
Site Recovery ayuda a mantener las aplicaciones en funcionamiento en caso de interrupciones zonales o regionales planeadas o no planeadas. Habilitar Site Recovery en las máquinas a gran escala mediante Azure Portal puede ser complicado. Ahora, tiene la manera de hacerlo en masa en grupos de recursos específicos (_ámbito_ de la directiva) mediante el portal.

Azure Policy soluciona este problema. Una vez que haya creado una directiva de recuperación ante desastres para un grupo de recursos, todas las nuevas máquinas virtuales agregadas a él tendrán habilitado Site Recovery automáticamente. Además, puede tener habilitado Site Recovery en todas las máquinas virtuales que ya estén presentes en el grupo de recursos mediante un proceso denominado _corrección_ (a continuación, se proporciona más información).

>[!NOTE]
>El _ámbito_ de esta directiva debe estar en el nivel de grupo de recursos.

## <a name="prerequisites"></a>Prerrequisitos

- Conozca cómo asignar una directiva [aquí](../governance/policy/assign-policy-portal.md).
- Aprenda más sobre la arquitectura de Azure para la recuperación ante desastres de Azure [aquí](./azure-to-azure-architecture.md).
- Revise la matriz de compatibilidad de Azure Policy con Azure Site Recovery:

**Escenario** | **Declaración de compatibilidad**
--- | ---
Managed Disks | Compatible
Unmanaged Disks  | No compatible
Varios discos | Compatible
Conjuntos de disponibilidad | Compatible
Zonas de disponibilidad | Compatible
Máquinas virtuales habilitadas para Azure Disk Encryption (ADE) | No compatible
Grupos con ubicación por proximidad (PPG) | Compatible
Discos habilitados para claves administradas por el cliente (CMK) | No compatible
Clústeres de espacio de almacenamiento directo (S2D) | No compatible
Modelo de implementación de Azure Resource Manager | Compatible
Modelo de implementación clásica | No compatible
Recuperación ante desastres de zona a zona  | Compatible
Interoperabilidad con otras directivas aplicadas de forma predeterminada por Azure (si las hay) | Compatible

>[!NOTE]
>En los casos siguientes, no se habilitará Site Recovery. Sin embargo, se reflejarán como _No compatibles_ en el cumplimiento de recursos:
>1. Si se crea una máquina virtual no compatible dentro del ámbito de la directiva.
>1. Si una máquina virtual forma parte a la vez de un conjunto de disponibilidad y de un grupo con ubicación por proximidad.

## <a name="create-a-policy-assignment"></a>Creación de una asignación de directiva
En esta sección, creará una asignación de directiva que permita Azure Site Recovery en todos los recursos recién creados.
1. Vaya a **Azure Portal** y diríjase a **Azure Policy**.
1. Seleccione **Asignaciones** en el panel izquierdo de la página de Azure Policy. Una asignación es una directiva que se asignó para ejecutarse dentro de un ámbito específico.
   :::image type="content" source="./media/azure-to-azure-how-to-enable-policy/select-assignments.png" alt-text="Captura de pantalla de la selección de la página Asignaciones en la página Información general de la directiva." border="false":::

1. Seleccione **Asignar directiva** en la parte superior de la página **Policy - Asignaciones**.
:::image type="content" source="./media/azure-to-azure-how-to-enable-policy/select-assign-policy.png" alt-text="Captura de pantalla de la selección de la opción &quot;Asignar directiva&quot; en la página Asignaciones." border="false":::

1. En la página **Asignar directiva**, establezca la opción **Ámbito**; para ello, seleccione los puntos suspensivos y, luego, elija una suscripción y un grupo de recursos. Un ámbito determina en qué recursos o agrupación de recursos se implementa la asignación de directiva. Luego, use el botón **Seleccionar** situado en la parte inferior de la página **Ámbito**.

1. Inicie el _selector de definiciones de directivas_; para ello, seleccione los puntos suspensivos junto a **Definición de directiva**. _Busque "Configure disaster recovery on virtual machines by enabling replication"_ (Configurar la recuperación ante desastres en las máquinas virtuales habilitando la replicación) y seleccione la directiva.
:::image type="content" source="./media/azure-to-azure-how-to-enable-policy/select-policy-definition.png" alt-text="Captura de pantalla de la selección de &quot;Definición de directiva&quot; en la página Aspectos básicos." border="true":::

1. **Nombre de asignación** se rellena automáticamente con el nombre de directiva seleccionado, pero puede cambiarlo. Puede resultar útil si planea asignar varias directivas de Azure Site Recovery al mismo ámbito.

1. Seleccione **Siguiente** para configurar las propiedades de Azure Site Recovery de la directiva.

## <a name="configure-target-settings-and-properties"></a>Configuración y propiedades de destino
Se dispone a crear una directiva para habilitar Azure Site Recovery. Ahora vamos a configurar las propiedades y las opciones de destino:
1. Se encuentra en la sección _Parámetros_ del flujo de trabajo _Asignar directiva_, que tiene el siguiente aspecto: :::image type="content" source="./media/azure-to-azure-how-to-enable-policy/specify-parameters.png" alt-text="Captura de pantalla de las opciones de configuración de la página Parámetros." border="true":::
1. Seleccione los valores adecuados para estos parámetros:
    - **Región de origen**: la región de origen de las máquinas virtuales en las que será aplicable la directiva.
    >[!NOTE]
    >La directiva se aplicará a todas las máquinas virtuales que pertenezcan a la región de origen en el ámbito de la directiva. Las máquinas virtuales que no existan en la región de origen no se incluirán en _Compatibilidad de recursos_.
    - **Región de destino**: ubicación donde se replicarán los datos de la máquina virtual de origen. Site Recovery proporciona la lista de regiones de destino en las que el cliente puede replicar. Se recomienda usar la misma ubicación que la del almacén de Recovery Services.
    - **Grupo de recursos de destino**: el grupo de recursos al que pertenecen todas las máquinas virtuales replicadas. De forma predeterminada, Site Recovery crea un nuevo grupo de recursos en la región de destino.
    - **Grupo de recursos del almacén**: el grupo de recursos en el que existe el almacén de Recovery Services.
    - **Almacén de Recovery Services**: el almacén en el que se protegerán todas las máquinas virtuales del ámbito. La directiva puede crear un almacén en su nombre si es necesario.
    - **Recovery Virtual Network** (Red virtual de recuperación): elija una red virtual existente en la región de destino que se usará para la máquina virtual de recuperación. La directiva también puede crear una red virtual en su nombre si es necesario.
    - **Zona de disponibilidad de destino**: escriba la zona de disponibilidad de la región de destino donde se conmutará por error la máquina virtual.
    >[!NOTE]
    >En un escenario de zona a zona, debe elegir la misma región de destino que la región de origen y optar por una zona de disponibilidad diferente en _Zona de disponibilidad de destino_.     
    >Si algunas de las máquinas virtuales del grupo de recursos ya están en la zona de disponibilidad de destino, la directiva no se les aplicará en caso de que esté configurando la recuperación ante desastres de zona a zona.
    - **Efecto**: permite habilitar o deshabilitar la ejecución de la directiva. Seleccione _DeployIfNotExists_ para habilitar la directiva en cuanto se cree.

1. Seleccione **Siguiente** para decidir la tarea de corrección.

## <a name="remediation-and-other-properties"></a>Corrección y otras propiedades
1. Las propiedades de destino de Azure Site Recovery se han configurado. Sin embargo, esta directiva solo tendrá efecto en las máquinas virtuales recién creadas en el ámbito de la directiva. Se puede aplicar a los recursos existentes mediante una tarea de corrección una vez asignada la directiva. Puede crear una tarea de corrección aquí activando la casilla _Crear una tarea de corrección_.

1. Azure Policy creará una [identidad administrada](../governance/policy/how-to/remediate-resources.md) que tendrá permisos de propietario para habilitar Azure Site Recovery en los recursos del ámbito.

1. Puede configurar un mensaje personalizado de no cumplimiento para la directiva en la pestaña _Non-compliance messages_ (Mensajes de no cumplimiento).

1. Seleccione Siguiente, en la parte inferior de la página, o la pestaña _Revisar y crear_, en la parte superior de la página, para avanzar a la siguiente sección del Asistente para asignación.

1. Revise las opciones seleccionadas y, a continuación, seleccione _Crear_ en la parte inferior de la página.

## <a name="next-steps"></a>Pasos siguientes

[Más información](site-recovery-test-failover-to-azure.md) sobre la ejecución de una conmutación por error de prueba.
