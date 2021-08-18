---
title: Archivo de inclusión
description: archivo de inclusión
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/16/2021
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 6e505158c80c0b9a831af17ca2f2d6062dc56f6a
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449854"
---
Para eliminar un contenedor de volúmenes, debe
 - eliminar volúmenes del contenedor de volúmenes. Si el contenedor de volúmenes tiene volúmenes asociados, desconecte primero estos volúmenes. Siga los pasos de [Desconexión de un volumen](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). Una vez que los volúmenes estén desconectados, puede eliminarlos. 
 - eliminar directivas de copia de seguridad e instantáneas en la nube asociadas. Compruebe si el contenedor de volúmenes tiene directivas de copia de seguridad e instantáneas en la nube asociadas. Si es así, [elimine las directivas de copia de seguridad](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). También se eliminarán las instantáneas en la nube. 
 
Si el contenedor de volúmenes no tiene volúmenes asociados, directivas de copia de seguridad ni instantáneas en la nube, puede eliminarlo. Realice el siguiente procedimiento para eliminar un contenedor de volúmenes.

#### <a name="to-delete-a-volume-container"></a>Para eliminar un contenedor de volúmenes

1. Vaya al servicio Administrador de dispositivos de StorSimple y haga clic en **Dispositivos**. Seleccione y haga clic en el dispositivo y, a continuación, vaya a **Configuración > Administrar > Contenedores de volúmenes**.

    ![Hoja Contenedores de volúmenes](./media/storsimple-8000-delete-volume-container/create-volume-container.png)

2. En la lista tabular de contenedores de volúmenes, seleccione el contenedor que desea eliminar, haga clic con el botón derecho en **...**  y, a continuación, seleccione **Eliminar**.

    ![Eliminación de un contenedor de volúmenes](./media/storsimple-8000-delete-volume-container/delete-volume-container-01.png)

3. Si un contenedor de volúmenes no tiene volúmenes asociados, directivas de copia de seguridad ni instantáneas en la nube, puede eliminarlo. Cuando se le pida confirmación, revise y seleccione la casilla que indica el impacto de la eliminación del contenedor de volúmenes. Haga clic en **Eliminar** y, a continuación, elimine el contenedor de volúmenes.

    ![Confirmar eliminación](./media/storsimple-8000-delete-volume-container/delete-volume-container-02.png)<!--Added missing border.-->

La lista de contenedores de volúmenes se actualiza para reflejar el contenedor eliminado.

![Captura de pantalla de la página Contenedor de volúmenes. La lista tabular de contenedores de volúmenes ya no contiene el contenedor eliminado.](./media/storsimple-8000-delete-volume-container/delete-volume-container-05.png)
