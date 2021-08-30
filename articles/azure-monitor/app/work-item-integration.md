---
title: 'Integración de elementos de trabajo: Application Insights'
description: Aprenda a crear elementos de trabajo en GitHub o Azure DevOps con los datos de Application Insights insertados en ellos.
ms.topic: conceptual
ms.date: 06/27/2021
ms.openlocfilehash: 02fbb15f417d01d1f9c5b572fdb5553ad1127037
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2021
ms.locfileid: "112989151"
---
# <a name="work-item-integration"></a>Integración de elementos de trabajo 

La funcionalidad de integración de elementos de trabajo le permite crear fácilmente en GitHub o Azure DevOps elementos de trabajo que tienen datos de Application Insights pertinentes insertados en ellos.


La nueva integración de elementos de trabajo ofrece las características siguientes respecto a la versión [clásica](#classic-work-item-integration):
- Campos avanzados, como persona asignada, proyectos o hitos.
- Iconos de repositorio para que pueda diferenciar entre los libros de Azure DevOps y GitHub.
- Varias configuraciones para cualquier número de repositorios o elementos de trabajo.
- Implementación mediante plantillas de Azure Resource Manager.
- Consultas pregeneradas y personalizables del lenguaje de consulta de palabras clave (KQL) para agregar datos de Application Insights a los elementos de trabajo.
- Plantillas de libro personalizables.


## <a name="create-and-configure-a-work-item-template"></a>Creación y configuración de una plantilla de elemento de trabajo

1. Para crear una plantilla de elemento de trabajo, vaya a su recurso de Application Insights y, a la izquierda, en *Configurar*, seleccione **Elementos de trabajo**; luego, en la parte superior, elija **Create a new template** (Crear nueva plantilla).

    :::image type="content" source="./media/work-item-integration/create-work-item-template.png" alt-text=" Captura de pantalla de la pestaña Elementos de trabajo con la opción Create a new template (Crear nueva plantilla) seleccionada." lightbox="./media/work-item-integration/create-work-item-template.png":::

    Si no existe ninguna plantilla actual, también puede crear una plantilla de elemento de trabajo desde la pestaña de detalles de la transacción completa. Seleccione un evento y, a la derecha, elija **Crear un elemento de trabajo**; luego, seleccione **Start with a workbook template** (Empezar con una plantilla de libro).

    :::image type="content" source="./media/work-item-integration/create-template-from-transaction-details.png" alt-text=" Captura de pantalla de la pestaña Detalles de la transacción completa con Crear un elemento de trabajo < Start with a workbook template (Empezar con una plantilla de libro) seleccionadas." lightbox="./media/work-item-integration/create-template-from-transaction-details.png":::

2. Después de seleccionar **Create a new template** (Crear nueva plantilla), puede elegir los sistemas de seguimiento, asignar un nombre al libro, crear un vínculo al sistema de seguimiento seleccionado y elegir una región para almacenar la plantilla (el valor predeterminado es la región en la que se encuentra el recurso de Application Insights). Los parámetros de dirección URL son la dirección URL predeterminada del repositorio, por ejemplo, `https://github.com/myusername/reponame` o `https://mydevops.visualstudio.com/myproject`.

    :::image type="content" source="./media/work-item-integration/create-workbook.png" alt-text=" Captura de pantalla de Create a new work item workbook template (Crear una plantilla de libro de elemento de trabajo).":::

    Puede establecer propiedades específicas de los elementos de trabajo directamente desde la propia plantilla. Esto incluye la persona asignada, la ruta de acceso de iteración, los proyectos, etc. en función del proveedor de control de versiones.

## <a name="create-a-work-item"></a>Crear un elemento de trabajo

 Puede acceder a la nueva plantilla desde cualquier detalle de transacción completa al que pueda acceder desde las pestañas Rendimiento, Errores, Disponibilidad, u otras.

1. Para crear un elemento de trabajo, vaya a Detalles de la transacción completa, seleccione un evento y, a continuación, elija **Crear elemento de trabajo** y la plantilla de elemento de trabajo.

    :::image type="content" source="./media/work-item-integration/create-work-item.png" alt-text=" Captura de pantalla de la pestaña Detalles de la transacción completa con Crear elemento de trabajo seleccionada." lightbox="./media/work-item-integration/create-work-item.png":::

1. Se abrirá una nueva pestaña en el explorador que le lleva al sistema de seguimiento seleccionado. En Azure DevOps, puede crear un error o una tarea y, en GitHub, puede crear un problema en el repositorio. Se crea automáticamente un elemento de trabajo con la información contextual proporcionada por Application Insights.

    :::image type="content" source="./media/work-item-integration/github-work-item.png" alt-text=" Captura de pantalla del problema de GitHub creado automáticamente" lightbox="./media/work-item-integration/github-work-item.png":::

    :::image type="content" source="./media/work-item-integration/azure-devops-work-item.png" alt-text=" Captura de pantalla de la creación automática de errores en Azure DevOps." lightbox="./media/work-item-integration/azure-devops-work-item.png":::

## <a name="edit-a-template"></a>Edición de una plantilla

Para editar la plantilla, vaya a la pestaña **Elementos de trabajo** en *Configurar* y seleccione el icono de lápiz situado junto al libro que quiere actualizar.

:::image type="content" source="./media/work-item-integration/edit-template.png" alt-text=" Captura de pantalla de la pestaña Elemento de trabajo con el icono de lápiz Editar seleccionado.":::

Seleccione Editar ![icono Editar](./media/work-item-integration/edit-icon.png) en la parte superior para empezar a editar la plantilla. Las plantillas de elemento de trabajo se basan en [libros de Azure Monitor](../visualize/workbooks-overview.md). La información del elemento de trabajo se genera mediante el lenguaje de consulta de palabras clave. Puede modificar las consultas para agregar más contexto esencial a su equipo. Cuando haya terminado de editar, guarde el libro; para ello, seleccione el icono Guardar ![icono Guardar](./media/work-item-integration/save-icon.png) en la barra de herramientas superior.

:::image type="content" source="./media/work-item-integration/edit-workbook.png" alt-text=" Captura de pantalla del libro de la plantilla de elemento de trabajo en modo de edición." lightbox="./media/work-item-integration/edit-workbook.png":::

Puede crear más de una configuración de elementos de trabajo y tener un libro personalizado adecuado para cada escenario. Los libros también pueden implementarse mediante Azure Resource Manager, lo que garantiza implementaciones estándar entre los entornos.

## <a name="classic-work-item-integration"></a>Integración de elementos de trabajo clásicos 

1. En la opción *Configurar* del recurso de Application Insights, seleccione **Elementos de trabajo**.
1. Seleccione **Cambiar al modo clásico**, rellene los campos con su información y realice la autorización. 

    :::image type="content" source="./media/work-item-integration/classic.png" alt-text="Captura de pantalla de cómo configurar elementos de trabajo clásicos." lightbox="./media/work-item-integration/classic.png":::

1. Para crear un elemento de trabajo, vaya a los detalles de la transacción completa, seleccione un evento y, a continuación, elija **Crear elemento de trabajo (clásico)** . 


### <a name="migrate-to-new-work-item-integration"></a>Migración a una nueva integración de elementos de trabajo

Para migrar, elimine la configuración de elemento de trabajo clásico y, a continuación, [cree y configure una plantilla de elemento de trabajo](#create-and-configure-a-work-item-template) para volver a crear la integración.

Para eliminar, vaya al recurso de Application Insights en *Configurar*, seleccione **Elementos de trabajo** y, a continuación, seleccione **Cambiar al modo clásico** y **Eliminar* en la parte superior.


## <a name="next-steps"></a>Pasos siguientes
[Prueba de disponibilidad](availability-overview.md)

