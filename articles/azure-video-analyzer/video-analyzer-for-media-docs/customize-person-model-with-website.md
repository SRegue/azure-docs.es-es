---
title: Personalización de un modelo de persona con el sitio web de Azure Video Analyzer for Media (anteriormente, Video Indexer)
titleSuffix: Azure Video Analyzer for Media
description: Obtenga información sobre cómo personalizar un modelo de persona con el sitio web de Azure Video Analyzer for Media (anteriormente, Video Indexer).
services: azure-video-analyzer
author: Juliako
manager: femila
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 12/16/2020
ms.author: juliako
ms.openlocfilehash: 9061300de7a05729b3281d68b440b211e557d99f
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/15/2021
ms.locfileid: "112119257"
---
# <a name="customize-a-person-model-with-the-video-analyzer-for-media-website"></a>Personalización de un modelo de persona con el sitio web de Video Analyzer for Media

Azure Video Analyzer for Media (anteriormente, Video Indexer) admite el reconocimiento de celebridades en el contenido en vídeo. La característica de reconocimiento de celebridades abarca aproximadamente un millón de caras basadas en orígenes de datos comúnmente solicitados, como IMDB, Wikipedia y personas con más influencia de LinkedIn. Para información general detallada, consulte [Personalización de un modelo de persona en Video Analyzer for Media](customize-person-model-overview.md).

Puede usar el sitio web de Video Analyzer for Media para editar las caras que se detectaron en un vídeo, como se describe en este tema. También puede usar la API, como se describe en el artículo de [Personalización de un modelo de persona mediante las API](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Administración central de modelos de persona en la cuenta

1. Para ver, editar y eliminar los modelos de persona de su cuenta, vaya al sitio web de Video Analyzer for Media e inicie sesión.
1. Seleccione el botón personalización del modelo de contenido situado a la izquierda de la página.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Personalización del modelo de contenido":::
1. Seleccione la pestaña Personas.

    Verá el modelo de persona predeterminado en su cuenta. El modelo de persona predeterminado contiene las caras que haya editado o modificado en la información de los vídeos para las que no haya especificado un modelo de persona personalizado durante la indexación.

    Si ha creado otros modelos de persona, se mostrarán en esta página también.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-face-model/content-model-customization-people-tab.png" alt-text="Personalización de personas":::

## <a name="create-a-new-person-model"></a>Creación de un modelo de persona

1. Seleccione el botón **+ Agregar modelo** de la derecha.
1. Escriba el nombre del modelo. Ahora puede agregar nuevas personas y caras al nuevo modelo de persona.
1. Seleccione el botón de menú de lista y elija **+ Add person** (+ Agregar persona).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-face-model/add-new-person.png" alt-text="Agregar una persona":::

## <a name="add-a-new-person-to-a-person-model"></a>Incorporación de una persona nueva a un modelo de persona

> [!NOTE]
> Video Analyzer for Media permite agregar varias personas con el mismo nombre a un modelo de persona. Pese a ello, se recomienda asignar un nombre único a cada persona del modelo para que su uso sea más sencillo y claro.

1. Para agregar una cara nueva a un modelo de persona, seleccione el botón de menú de la lista junto al modelo de persona al que quiera agregar la cara.
1. Seleccione **+ Add person** (+ Agregar persona) en el menú.

    En un elemento emergente se le pedirá que rellene los detalles de la persona. Escriba el nombre de la persona y seleccione el botón de comprobación.

    A continuación, puede usar el Explorador de archivos o arrastrar y colocar las imágenes de la cara. Video Analyzer for Media admite todos los tipos de archivo de imagen estándares (p. ej.: JPG, PNG, etc.).

    Video Analyzer for Media puede detectar la presencia de esta persona en los vídeos futuros que indexe y los actuales que ya haya indexado, gracias al modelo de persona al que agregó la nueva cara. El reconocimiento de la persona en los vídeos actuales puede tardar algún tiempo en aplicarse, ya que se trata de un proceso por lotes.

## <a name="rename-a-person-model"></a>Cambio de nombre de un modelo de persona

Puede cambiar el nombre de cualquier modelo de persona en su cuenta, incluido el modelo predeterminado. Aunque cambie el nombre del modelo de persona predeterminado, este seguirá sirviendo como predeterminado en su cuenta.

1. Seleccione el botón de menú de la lista junto al modelo de persona al que quiera cambiar el nombre.
1. Seleccione **Rename** (Cambiar nombre) en el menú.
1. Seleccione el nombre del modelo actual y escriba el nuevo nombre.
1. Seleccione el botón de comprobación del modelo cuyo nombre vaya a cambiar.

## <a name="delete-a-person-model"></a>Eliminación de un modelo de persona

Puede eliminar cualquier modelo de persona que haya creado en su cuenta. Pero el modelo de persona predeterminado no se puede eliminar.

1. Seleccione **Delete** (Eliminar) en el menú.

    Se mostrará un elemento emergente y se le notificará que esta acción eliminará el modelo de persona y todas las personas y los archivos que contenga. Esta acción no se puede deshacer.
1. Si está seguro, seleccione Delete (Eliminar) de nuevo.

> [!NOTE]
> Los vídeos existentes que se hayan indexado con este modelo de persona (eliminado) no serán compatibles con la capacidad de actualizar los nombres de las caras que aparezcan en el vídeo. Solo podrá editar los nombres de las caras de estos vídeos después de volver a indexarlos con otro modelo de persona. Si vuelve indexar sin especificar un modelo de persona, se usará el predeterminado.

## <a name="manage-existing-people-in-a-person-model"></a>Administración de personas existentes en un modelo de persona

Para ver el contenido de cualquier modelo de persona, seleccione la flecha situada junto al nombre de este. La lista desplegable muestra todas las personas de ese modelo de persona concreto. Si selecciona el botón de menú de la lista situada junto a cada persona, podrá ver, administrar y eliminar opciones, así como cambiarlas de nombre.  

![La captura de pantalla muestra un menú contextual con opciones para administrar, cambiar el nombre y eliminar.](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Cambio de nombre de una persona

1. Para cambiar el nombre de una persona del modelo, seleccione el botón de menú de la lista y elija **Rename** (Cambiar el nombre).
1. Seleccione el nombre actual de la persona y escriba el nuevo.
1. Seleccione el botón de comprobación y el nombre de la persona se cambiará.

### <a name="delete-a-person"></a>Eliminación de una persona

1. Para eliminar el nombre de una persona del modelo, seleccione el botón de menú de la lista y elija **Delete** (Eliminar).
1. Un elemento emergente le indicará que la acción elimina la persona y no se puede deshacer.
1. Seleccione **Delete** (Eliminar) de nuevo para eliminar la persona del modelo de persona.

### <a name="manage-a-person"></a>Administración de una persona

Si selecciona **Manage** (Administrar), verá la ventana **Person's details** (Detalles de la persona) con todas las caras con las que se está entrenando a este modelo Person. Estas caras proceden de apariciones de esa persona en los vídeos que usan este modelo de persona, o de imágenes que se hayan cargado manualmente.

> [!TIP]
> Para ir a la ventana **Person's details** (Detalles de la persona), haga clic en el nombre de la persona o haga clic en **Manage** (Administrar), como se ha mostrado anteriormente.

#### <a name="add-a-face"></a>Adición de una cara

Para agregar más caras a la persona, seleccione **Add images** (Agregar imágenes).

#### <a name="delete-a-face"></a>Eliminación de un archivo

Seleccione la imagen que quiere eliminar y haga clic en **Delete** (Eliminar).

#### <a name="rename-and-delete-the-person"></a>Cambio de nombre y eliminación de la persona 

Puede usar el panel de administración para cambiar el nombre de la persona y eliminar la persona del modelo.

## <a name="use-a-person-model-to-index-a-video"></a>Uso de un modelo de persona para indexar un vídeo

Puede usar un modelo de persona para indexar el vídeo nuevo mediante la asignación del modelo durante la carga del vídeo.

Para usar el modelo de persona en un vídeo nuevo, haga lo siguiente:

1. Seleccione el botón **Cargar** en la parte superior de la página.
1. Coloque el archivo de vídeo o búsquelo.
1. Seleccione la flecha **Advanced options** (Opciones avanzadas).
1. Seleccione la lista desplegable y seleccione el modelo de persona que ha creado.
1. Seleccione la opción **Upload** (Cargar) en la parte inferior de la página y el nuevo vídeo se indexará por ese modelo de persona.

Si no especifica un modelo de persona durante la carga, Video Analyzer for Media indexará el vídeo con el modelo de persona predeterminado en su cuenta.

## <a name="use-a-person-model-to-reindex-a-video"></a>Uso de un modelo de persona para volver a indexar un vídeo

Para usar el modelo de persona para volver a indexar un vídeo de la colección, vaya a Account videos (Vídeos de la cuenta) en la página de inicio de Video Analyzer for Media y mantenga el puntero sobre el nombre del vídeo que quiera volver a indexar.

Verá opciones para editar, eliminar y volver a indexar el vídeo.

1. Seleccione la opción para volver a indexar el vídeo.

    ![La captura de pantalla muestra los vídeos de la cuenta y la opción para reindexar cualquier vídeo.](./media/customize-face-model/reindex.png)

    Ahora puede seleccionar el modelo de persona con el que volver a indexar el vídeo.
1. Seleccione la lista desplegable y seleccione el modelo de persona que quiera usar.
1. Seleccione el botón **Reindex** (Volver a indexar) y el vídeo se volverá a indexar con su modelo de persona.

Las nuevas modificaciones que realice en las caras detectadas y que se reconozcan en el vídeo que acaba de reindexar se guardarán en el modelo de persona que usó para el reindexado.

## <a name="managing-people-in-your-videos"></a>Administración de las personas en sus vídeos

Puede editar y eliminar caras para administrar las caras que se detectan y las personas que se reconocen en los vídeos que indexe.

Al eliminar una cara concreta, esta se elimina de la información del vídeo.

Al editar una cara, cambiará el nombre de la cara que se haya detectado (y posiblemente reconocido) en el vídeo. Al editar una cara en el vídeo, el nombre se guarda como una entrada de persona en el modelo de persona asignado al vídeo durante la carga y el indexado.

Si no asigna un modelo de persona al vídeo durante la carga, la edición se guarda en el modelo de persona predeterminado de su cuenta.

### <a name="edit-a-face"></a>Edición de una cara

> [!NOTE]
> Si un modelo de persona tiene dos o más personas diferentes con el mismo nombre, no podrá etiquetar ese nombre en los vídeos que usen ese modelo de persona. Solo podrá realizar cambios en las personas que comparten el nombre en la pestaña Personas de la página de personalización del modelo de contenido de Video Analyzer for Media. Por este motivo se recomienda proporcionar nombres únicos a las personas del modelo.

1. Vaya al sitio web de Video Analyzer for Media e inicie sesión.
1. Busque un vídeo que quiera ver y edítelo en su cuenta.
1. Para editar una cara del vídeo, vaya a la pestaña Insights (Información) y seleccione en el icono de lápiz en la esquina superior derecha de la ventana.

    ![La captura de pantalla muestra un vídeo con una cara desconocida seleccionada.](./media/customize-face-model/edit-face.png)

1. Seleccione cualquiera de las caras detectadas y cambie sus nombres ("#X desconocido", o los que asignara previamente a la cara) por otros.
1. Después de escribir el nuevo nombre, seleccione el icono de comprobación junto a él. Esta acción guardará el nuevo nombre, y todas las apariciones de esta cara en otros vídeos actuales y en los vídeos futuros que cargue se reconocerán y nombrarán. El reconocimiento de la cara en los otros vídeos actuales puede tardar algún tiempo en aplicarse ya que se trata de un proceso por lotes.

Si asigna a una cara el nombre de una persona existente en el modelo que usa el vídeo, la imagen de la cara detectada en el vídeo se combinará con la de la persona con la que ya existe en el modelo. Si le asigna un nombre completamente nuevo, se creará una entrada de persona en el modelo de persona que use el vídeo.

### <a name="delete-a-face"></a>Eliminación de un archivo

Para eliminar una cara detectada en el vídeo, vaya al panel Insights (Información) y seleccione el icono de lápiz en la esquina superior derecha del panel. Seleccione la opción **Delete** (Eliminar) debajo del nombre de la cara. Esta acción quita la cara detectada del vídeo. La cara de la persona se seguirá detectando en los otros vídeos en los que aparezca, pero también puede eliminarla de esos vídeos después de que se hayan indexado.

Si se le había dado un nombre, la persona también seguirá existiendo en el modelo que se usara para indexar el vídeo donde eliminó la cara (a menos que la persona se elimine expresamente del modelo).

## <a name="next-steps"></a>Pasos siguientes

[Personalización del modelo de persona mediante las API](customize-person-model-with-api.md)
