---
title: Administración de recursos compartidos en Azure Stack Edge Pro con GPU | Microsoft Docs
description: En este artículo se explica cómo usar Azure Portal para administrar recursos compartidos en Azure Stack Edge Pro con GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/26/2021
ms.author: alkohli
ms.openlocfilehash: a77cbee43ed52500e5de1b67286bada931a87754
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736865"
---
# <a name="use-azure-portal-to-manage-shares-on-your-azure-stack-edge-pro"></a>Uso de Azure Portal para administrar recursos compartidos en Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En este artículo se explica cómo administrar recursos compartidos en Azure Stack Edge Pro. Azure Stack Edge Pro se puede administrar con Azure Portal o con la interfaz de usuario web local. Use Azure Portal para agregar, eliminar y actualizar recursos compartidos o sincronizar la clave de almacenamiento para la cuenta de almacenamiento asociada con los recursos compartidos.

## <a name="about-shares"></a>Acerca de los recursos compartidos

Para transferir datos a Azure, es preciso crear recursos compartidos en Azure Stack Edge Pro. Los recursos compartidos que agrega en el dispositivo Azure Stack Edge Pro pueden ser recursos compartidos locales o recursos compartidos que insertan datos en la nube.

 - **Recursos compartidos locales**: use estos recursos compartidos cuando quiera procesar los datos localmente en el dispositivo.
 - **Recursos compartidos**: use estos recursos compartidos cuando quiera que los datos del dispositivo se inserten automáticamente en su cuenta de almacenamiento en la nube. Todas las funciones de la nube, como la **actualización** y la **sincronización de claves de almacenamiento** se aplican a los recursos compartidos.


## <a name="add-a-share"></a>Agregar un recurso compartido

Siga estos pasos en Azure Portal para crear un recurso compartido.

1. En Azure Portal, vaya al recurso de Azure Stack Edge y a **Cloud storage gateway > Shares** (Puerta de enlace de almacenamiento en la nube > Recursos compartidos). Seleccione **+ Agregar recurso compartido** en la barra de comandos.

    ![Seleccionar Agregar recurso compartido](media/azure-stack-edge-gpu-manage-shares/add-share-1.png)

2. En **Agregar recurso compartido**, especifique la configuración del recurso compartido. Proporcione un nombre exclusivo para el recurso compartido.
    
    Los nombres de recursos compartidos solo pueden contener letras minúsculas, números y guiones. El nombre del recurso compartido debe tener entre 3 y 63 caracteres y empezar por una letra o un número. Antes y después de cada guion debe ir un carácter que no sea otro guión.

3. Seleccione un **tipo** de recurso compartido. El tipo puede ser **SMB** o **NFS** (SMB es el predeterminado). SMB es el estándar para los clientes de Windows y se usa NFS para los clientes de Linux. Dependiendo de si elige recursos compartidos SMB o NFS, las opciones que se presentan son ligeramente diferentes.

4. Especifique la **cuenta de almacenamiento** en que se encuentra el recurso compartido. Se crea un contenedor en la cuenta de almacenamiento con el nombre del recurso compartido si el contenedor no existía previamente. Si el contenedor ya existe, se usará este.

5. En la lista desplegable, elija el **servicio de almacenamiento** entre blob en bloques, blob en páginas o archivos. El tipo de servicio elegido depende de en qué formato desea que los datos residan en Azure. Por ejemplo, en este caso, queremos que los datos residan como **blobs en bloques** en Azure y, por tanto, seleccionamos esa opción. Si elige **Blob en páginas**, debe asegurarse de que los datos tienen 512 bytes alineados. Use **Blob en páginas** para VHD o VHDX que siempre tienen 512 bytes alineados.

6. Este paso depende de si está creando un recurso compartido SMB o NFS.
    - **Si crea un recurso compartido SMB**: en el campo **Usuario local con todos los privilegios**, elija entre **Crear nuevo** o **Usar existente**. Si va a crear un nuevo usuario local, indique el **nombre de usuario** y la **contraseña** y, después, confirme la contraseña. Esto asigna los permisos al usuario local. Después de haber asignado los permisos aquí, puede utilizar el Explorador de archivos para modificarlos.

        ![Incorporación de recurso compartido de SMB](media/azure-stack-edge-gpu-manage-shares/add-smb-share.png)

        Si selecciona que se permitan operaciones de solo lectura en los datos de este recurso compartido, puede especificar usuarios de solo lectura.
    - **Si crea un recurso compartido NFS**: debe indicar las **direcciones IP de los clientes autorizados** que pueden acceder al recurso compartido.

        ![Incorporación de un recurso compartido NFS](media/azure-stack-edge-gpu-manage-shares/add-nfs-share.png)

7. Para acceder fácilmente a los recursos compartidos de los módulos de proceso perimetral, use el punto de montaje local. Seleccione **Usar el recurso compartido con el proceso perimetral** para que el recurso compartido se monte automáticamente después de que se cree. Cuando se selecciona esta opción, el módulo Edge también puede usar el proceso con el punto de montaje local.

8. Haga clic en **Crear** para crear el recurso compartido. Se le notifica que la creación del recurso compartido está en curso. Una vez creado el recurso compartido con la configuración especificada, la hoja **Recursos compartidos** se actualiza para reflejar el nuevo recurso compartido.

## <a name="add-a-local-share"></a>Incorporación de un recurso compartido local

1. En Azure Portal, vaya al recurso de Azure Stack Edge y, luego, a **Cloud storage gateway > Shares** (Puerta de enlace de almacenamiento en la nube > Recursos compartidos). Seleccione **+ Agregar recurso compartido** en la barra de comandos.

    ![Seleccionar Agregar recurso compartido 2](media/azure-stack-edge-gpu-manage-shares/add-local-share-1.png)

2. En **Agregar recurso compartido**, especifique la configuración del recurso compartido. Proporcione un nombre exclusivo para el recurso compartido.
    
    Los nombres de recursos compartidos solo pueden contener letras minúsculas y mayúsculas, números y guiones. El nombre del recurso compartido debe tener entre 3 y 63 caracteres y empezar por una letra o un número. Antes y después de cada guion debe ir un carácter que no sea otro guión.

3. Seleccione un **tipo** de recurso compartido. El tipo puede ser **SMB** o **NFS** (SMB es el predeterminado). SMB es el estándar para los clientes de Windows y se usa NFS para los clientes de Linux. Dependiendo de si elige recursos compartidos SMB o NFS, las opciones que se presentan son ligeramente diferentes.

   > [!IMPORTANT]
   > Asegúrese de que la cuenta de Azure Storage que usa no tiene directivas de inmutabilidad establecidas si la usa con un dispositivo Azure Stack Edge Pro o Data Box Gateway. Para más información, consulte [Establecimiento y administración de directivas de inmutabilidad para el almacenamiento de blobs](../storage/blobs/immutable-policy-configure-version-scope.md).

4. Para acceder fácilmente a los recursos compartidos de los módulos de proceso perimetral, use el punto de montaje local. Seleccione **Use the share with Edge compute** (Usar el recurso compartido con el proceso perimetral) para que el módulo de Edge pueda usar el proceso con el punto de montaje local.

5. Seleccione **Configure as Edge local shares** (Configurar como recursos compartidos locales de Edge). Los datos de los recursos compartidos locales se quedarán en el dispositivo. Puede procesar estos datos de manera local.

6. En el campo **Usuario local con todos los privilegios**, elija entre **Crear nuevo** o **Usar existente**.

7. Seleccione **Crear**. 

    ![Creación de un recurso compartido local](media/azure-stack-edge-gpu-manage-shares/add-local-share-2.png)

    Verá una notificación de que la creación del recurso compartido está en curso. Una vez creado el recurso compartido con la configuración especificada, la hoja **Recursos compartidos** se actualiza para reflejar el nuevo recurso compartido.

    ![Vista de las actualizaciones de la hoja Recursos compartidos](media/azure-stack-edge-gpu-manage-shares/add-local-share-3.png)
    
    Seleccione el recurso compartido para ver el punto de montaje local para los módulos de proceso perimetral de este recurso compartido.

    ![Vista de los detalles del recurso compartido](media/azure-stack-edge-gpu-manage-shares/add-local-share-4.png)

## <a name="mount-a-share"></a>Montaje de un recurso compartido

Si creó un recurso compartido antes de configurar el proceso en su dispositivo Azure Stack Edge Pro, deberá montar el recurso compartido. Realice los siguientes pasos para montar un recurso compartido.


1. En Azure Portal, vaya al recurso de Azure Stack Edge y, luego, a **Cloud storage gateway > Shares** (Puerta de enlace de almacenamiento en la nube > Recursos compartidos). En la lista de recursos compartidos, seleccione el que desee montar. En la columna **Usado para el proceso**, el estado del recurso compartido seleccionado aparecerá como **Deshabilitado**.

    ![Seleccionar recurso compartido](media/azure-stack-edge-gpu-manage-shares/mount-share-1.png)

2. Seleccione **Montar**.

    ![Seleccionar el montaje](media/azure-stack-edge-gpu-manage-shares/mount-share-2.png)

3. Cuando se le pida confirmación, seleccione **Sí**. Se montará el recurso compartido.

    ![Confirmar el montaje](media/azure-stack-edge-gpu-manage-shares/mount-share-3.png)

4. Después de que se monte el recurso compartido, vaya a la lista de recursos compartidos. Verá que la columna **Usado para el proceso** muestra el estado del recurso compartido como **Habilitado**.

    ![Recurso compartido montado](media/azure-stack-edge-gpu-manage-shares/mount-share-4.png)

5. Vuelva a seleccionar el recurso compartido para ver el punto de montaje local del recurso compartido. El módulo del proceso perimetral usa este punto de montaje local para el recurso compartido.

    ![Punto de montaje local del recurso compartido](media/azure-stack-edge-gpu-manage-shares/mount-share-5.png)

## <a name="unmount-a-share"></a>Desmontaje de un recurso compartido

Siga estos pasos en Azure Portal para desmontar un recurso compartido.

1. En Azure Portal, vaya al recurso de Azure Stack Edge y a **Cloud storage gateway > Shares** (Puerta de enlace de almacenamiento en la nube > Recursos compartidos). En la lista de recursos compartidos, seleccione el que desee desmontar. Desea asegurarse de que ningún módulo usa el recurso compartido que desmonta. Si un módulo usa el recurso compartido, verá que aparecen problemas con este módulo.

    ![Seleccionar recurso compartido 2](media/azure-stack-edge-gpu-manage-shares/unmount-share-1.png)

2.  Seleccione **Desmontar**.

    ![Seleccionar el desmontaje](media/azure-stack-edge-gpu-manage-shares/unmount-share-2.png)

3. Cuando se le pida confirmación, seleccione **Sí**. Se desmontará el recurso compartido.

    ![Confirmar el desmontaje](media/azure-stack-edge-gpu-manage-shares/unmount-share-3.png)

4. Después de que se desmonte el recurso compartido, vaya a la lista de recursos compartidos. Verá que la columna **Usado para el proceso** muestra el estado del recurso compartido como **Deshabilitado**.

    ![Recurso compartido desmontado](media/azure-stack-edge-gpu-manage-shares/unmount-share-4.png)

## <a name="delete-a-share"></a>Eliminación de un recurso compartido

Siga estos pasos en Azure Portal para eliminar un recurso compartido.

1. En la lista de recursos compartidos, seleccione y haga clic en el recurso compartido que desea eliminar.

    ![Seleccionar recurso compartido 3](media/azure-stack-edge-gpu-manage-shares/delete-share-1.png)

2. Haga clic en **Eliminar**.

    ![Clic en Eliminar](media/azure-stack-edge-gpu-manage-shares/delete-share-2.png)

3. Cuando se le pida confirmación, haga clic en **Sí**.

    ![Confirmar eliminación](media/azure-stack-edge-gpu-manage-shares/delete-share-3.png)

La lista de recursos compartidos se actualiza para reflejar la eliminación.

## <a name="refresh-shares"></a>Actualización de recursos compartidos

La característica de actualización permite actualizar el contenido de un recurso compartido. Al actualizar un recurso compartido, se inicia una búsqueda para encontrar todos los objetos de Azure, lo que incluye los blobs y archivos que se agregaron a la nube desde la última actualización. Luego, estos archivos adicionales se descargan para actualizar el contenido del recurso compartido en el dispositivo.

> [!IMPORTANT]
> - Los recursos compartidos locales no se pueden actualizar.
> - En una operación de actualización, no se conservan los permisos y las listas de control de acceso (ACL). 

Siga estos pasos en Azure Portal para actualizar un recurso compartido.

1.  En Azure Portal, vaya a **Recursos compartidos**. Seleccione y haga clic en el recurso compartido que desea actualizar.

    ![Seleccionar recurso compartido 4](media/azure-stack-edge-gpu-manage-shares/refresh-share-1.png)

2.  Haga clic en **Actualizar**. 

    ![Hacer clic en actualizar](media/azure-stack-edge-gpu-manage-shares/refresh-share-2.png)
 
3.  Cuando se le pida confirmación, haga clic en **Sí**. Se inicia el trabajo de actualización del contenido del recurso compartido local.

    ![Confirmar actualización](media/azure-stack-edge-gpu-manage-shares/refresh-share-3.png)
 
4.   Mientras la actualización está en curso, la opción de actualización está deshabilitada en el menú contextual. Haga clic en la notificación para ver el estado del trabajo de actualización.

5.   El tiempo que tarde en completarse la actualización dependerá del número de archivos que haya en el contenedor de Azure, así como de los archivos del dispositivo. Cuando la actualización se haya completado correctamente, se actualizará la marca de tiempo del recurso compartido. Aunque la actualización tenga errores parciales, la operación se considerará correcta y se actualizará la marca de tiempo. También se actualizan los registros de errores de actualización.

![Marca de tiempo actualizada](media/azure-stack-edge-gpu-manage-shares/refresh-share-4.png)
 
Si se produce un error, se genera una alerta, en la que se indica la causa del error y se proporciona una recomendación para solucionarlo. La alerta también incluye vínculos a un archivo que contiene el resumen completo de los errores, incluidos los archivos que no se pudieron actualizar o eliminar.

## <a name="sync-pinned-files"></a>Sincronización de archivos anclados 

Para sincronizar automáticamente los archivos anclados, realice los pasos siguientes en Azure Portal:
 
1. Seleccione una cuenta de Azure Storage. 

2. Vaya a **Contenedores** y seleccione **+ Contenedor** para crear un contenedor. Asigne un nombre a este contenedor como *newcontainer*. Establezca el nivel de acceso público en **Contenedor**.

    ![Sincronización automatizada para archivos anclados 1](media/azure-stack-edge-gpu-manage-shares/image-1.png)

3. Seleccione el nombre del contenedor y establezca los metadatos siguientes:  

    - Nombre = "Pinned" 
    - Valor = True 

    ![Sincronización automatizada para archivos anclados 2](media/azure-stack-edge-gpu-manage-shares/image-2.png)
 
4. Cree un recurso compartido en el dispositivo. Asígnela al contenedor anclado seleccionando la opción de contenedor existente. Marque el recurso compartido como de solo lectura. Cree un usuario y especifique el nombre de usuario y la contraseña correspondiente para este recurso compartido.  

    ![Sincronización automatizada para archivos anclados 3](media/azure-stack-edge-gpu-manage-shares/image-3.png)
 
5. En Azure Portal, busque el contenedor que ha creado. Cargue el archivo que desea anclar en el contenedor newcontainer que tiene los metadatos establecidos en anclado. 

6. Seleccione **Actualizar datos** en Azure Portal para que el dispositivo descargue la directiva de anclaje para ese contenedor de Azure Storage determinado.  

    ![Sincronización automatizada para archivos anclados 4](media/azure-stack-edge-gpu-manage-shares/image-4.png)
 
7. Acceda al nuevo recurso compartido que creó en el dispositivo. El archivo que se cargó en la cuenta de almacenamiento se descarga ahora en el recurso compartido local. 

    Cada vez que el dispositivo se desconecta o se vuelve a conectar, desencadena la actualización. La actualización desactivará solo los archivos que han cambiado. 


## <a name="sync-storage-keys"></a>Sincronización de claves de almacenamiento

Si las claves de la cuenta de almacenamiento han rotado, deberá sincronizar las claves del acceso al almacenamiento. La sincronización ayuda al dispositivo a obtener las claves de almacenamiento más recientes de su cuenta de almacenamiento.

Realice los pasos siguientes en Azure Portal para sincronizar la clave de acceso al almacenamiento.

1. Vaya a **Información general** en el recurso. En la lista de recursos compartidos, elija y haga clic en un recurso compartido asociado con la cuenta de almacenamiento que necesita sincronizar.

    ![Selección del recurso compartido con una cuenta de almacenamiento pertinente](media/azure-stack-edge-gpu-manage-shares/sync-storage-key-1.png)

2. Haga clic en **Sincronizar clave de almacenamiento**. Haga clic en **Sí** cuando se pida confirmación.

     ![Seleccionar Sincronizar clave de almacenamiento](media/azure-stack-edge-gpu-manage-shares/sync-storage-key-2.png)

3. Salga del cuadro de diálogo cuando haya finalizado la sincronización.

>[!NOTE]
> Esta operación solo debe realizarse una vez en cada cuenta de almacenamiento. No es necesario repetir la acción para todos los recursos compartidos asociados con la misma cuenta de almacenamiento.


## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [administrar usuarios desde Azure Portal](azure-stack-edge-gpu-manage-users.md).