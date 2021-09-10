---
title: Administración de Azure Data Box o Azure Data Box Heavy mediante la interfaz de usuario web local
description: Describe cómo usar la interfaz de usuario web local para administrar los dispositivos Data Box y Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/25/2021
ms.author: alkohli
ms.openlocfilehash: fa37d5dc9957e39d7fe4f47404bc9aaf249f192f
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123226433"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box-and-data-box-heavy"></a>Use la interfaz de usuario web local para administrar los dispositivos Data Box y Data Box Heavy

En este artículo se describen algunas de las tareas de administración y configuración que se realizan en los dispositivos Data Box y Data Box Heavy. Puede administrar los dispositivos Data Box y Data Box Heavy a través de la interfaz de usuario de Azure Portal y de la interfaz de usuario web local para el dispositivo. Este artículo se centra en las tareas realizadas con la interfaz de usuario web local.

La interfaz de usuario web local para Data Box y Data Box Heavy se usa para la configuración inicial del dispositivo. También puede usar la interfaz de usuario web local para apagar o reiniciar el dispositivo, ejecutar pruebas de diagnóstico, actualizar el software, ver registros de copia, borrar datos locales del dispositivo y generar un paquete de soporte técnico para Soporte técnico de Microsoft. En un dispositivo Data Box Heavy con dos nodos independientes, puede acceder a dos interfaces de usuario web local independientes correspondientes a cada nodo del dispositivo.

## <a name="generate-support-package"></a>Generar un paquete de soporte técnico

Si experimenta los problemas con el dispositivo, puede crear un paquete de soporte técnico de los registros del sistema. El Soporte técnico de Microsoft usa este paquete para solucionar el problema.

Para generar un paquete de soporte técnico, realice los pasos siguientes:

1. En la interfaz de usuario web local, vaya a **Ponerse en contacto con el servicio de soporte técnico**. Opcionalmente, seleccione **Incluir volcados de memoria**. Luego seleccione **Crear un paquete de soporte técnico**.

    Un volcado de memoria es el contenido de la memoria del dispositivo, guardado después de un error del sistema.

    No debe seleccionar la opción **Incluir volcados de memoria**, a menos que Soporte técnico le pida uno. Se tarda mucho tiempo en recopilar un paquete de soporte técnico que incluya volcados de memoria, y se incluyen datos confidenciales.

    ![Crear el paquete de soporte técnico 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

    Se recopila un paquete de soporte técnico. Esta operación tarda unos minutos si solo incluye registros del sistema. Si incluye volcados de memoria, tarda mucho más.

    ![Crear el paquete de soporte técnico 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

2. Una vez completada la creación del paquete de soporte técnico, seleccione **Descargar el paquete de soporte técnico**.

    ![Creación del paquete de soporte técnico 3](media/data-box-local-web-ui-admin/create-support-package-3.png)

3. Busque y elija la ubicación de descarga. Abra la carpeta para ver el contenido.

    ![Crear el paquete de soporte técnico 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

## <a name="erase-local-data-from-your-device"></a>Borrado de datos locales del dispositivo

Puede usar la interfaz de usuario web local para borrar los datos locales del dispositivo antes de devolverlo al centro de datos de Azure.

> [!IMPORTANT]
> Un borrado de datos no se puede revertir. Antes de borrar los datos locales del dispositivo, asegúrese de hacer una copia de seguridad de los archivos.

Para borrar los datos locales del dispositivo, siga estos pasos:

1. En la interfaz de usuario web local, vaya a **Data erase** (Borrado de datos).
2. Escriba la contraseña del dispositivo y seleccione **Erase data** (Borrar datos).

    ![Opción de borrado de datos de un dispositivo](media/data-box-local-web-ui-admin/erase-local-data-1.png)

3. En el mensaje de confirmación, seleccione **Sí** para continuar. Un borrado de datos puede tardar hasta 50 minutos.

   Asegúrese de hacer una copia de seguridad de los datos locales antes de borrarlos del dispositivo. Un borrado de datos no se puede revertir.

    ![Mensaje de confirmación de borrado de datos](media/data-box-local-web-ui-admin/erase-local-data-2.png)

## <a name="shut-down-or-restart-your-device"></a>Apagar o reiniciar el dispositivo

Puede apagar o reiniciar el dispositivo virtual mediante la interfaz de usuario web local. Se recomienda que, antes de reiniciar, desconecte los recursos compartidos del host y, luego, el dispositivo. Esto minimizará la posibilidad de daños en los datos. Asegúrese de que la copia de datos no esté en curso al apagar el dispositivo.

Para apagar el dispositivo, siga estos pasos.

1. En la interfaz de usuario web local, vaya a **Apagar o reiniciar**.

2. Seleccione **Apagar**.

    ![Apagar Data Box 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. Cuando se le pida confirmación, seleccione **Aceptar** para continuar.

    ![Apagar Data Box 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

Una vez que el dispositivo se apague, use el botón de encendido del panel frontal para activar el dispositivo.

Para reiniciar su Data Box, realice los pasos siguientes.

1. En la interfaz de usuario web local, vaya a **Apagar o reiniciar**.
2. Seleccione **Reiniciar**.

    ![Reiniciar Data Box 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. Cuando se le pida confirmación, seleccione **Aceptar** para continuar.

   El dispositivo se apaga y, a continuación, se reinicia.

## <a name="get-share-credentials"></a>Obtención de las credenciales de recursos compartidos 

Si necesita averiguar el nombre de usuario y la contraseña que se usarán para conectarse a un recurso compartido en el dispositivo, puede encontrar las credenciales del recurso compartido en **Conectar y copiar** en la interfaz de usuario web local.

Al solicitar el dispositivo, puede elegir entre usar contraseñas predeterminadas generadas por el sistema para los recursos compartidos del dispositivo o bien usar sus propias contraseñas. En cualquier caso, las contraseñas de recurso compartido se establecen de fábrica y no se pueden cambiar. 

Para obtener las credenciales de un recurso compartido:

1. En la interfaz de usuario web local, vaya a **Conectar y copiar**. Seleccione **SMB** para obtener las credenciales de acceso de los recursos compartidos asociados con la cuenta de almacenamiento.

   ![Captura de pantalla que muestra la página Conectar y copiar en la interfaz de usuario web local para un Data Box. El elemento de menú Conectar y copiar junto con la opción SMB aparecen resaltados.](media/data-box-local-web-ui-admin/get-share-credentials-01.png)

1. En el cuadro de diálogo **Acceder al recurso compartido y copiar datos**, copie los valores de **Nombre de usuario** y **Contraseña** correspondientes al recurso compartido. Para cerrar el cuadro de diálogo, seleccione **Aceptar**.

   ![Captura de pantalla que muestra el cuadro de diálogo Acceder al recurso compartido y copiar datos en la interfaz de usuario web local para un recurso compartido SMB en el Data Box. El icono Copiar de las opciones Cuenta de almacenamiento y Contraseña, así como el botón Aceptar, aparecen resaltados.](media/data-box-local-web-ui-admin/get-share-credentials-02.png)

> [!NOTE]
> Después de varios errores de conexión del recurso compartido debido al uso de una contraseña incorrecta, la cuenta de usuario se bloqueará fuera de ese recurso. El bloqueo de cuenta se eliminará después de unos minutos y podrá conectarse de nuevo a los recursos compartidos.  
> - Data Box 4.1 y versiones posteriores: la cuenta se bloquea durante 15 minutos después de cinco intentos de inicio de sesión con errores. 
> - Data Box 4.0 y versiones anteriores: la cuenta se bloquea durante 30 minutos después de tres intentos de inicio de sesión con errores.

## <a name="download-bom-or-manifest-files"></a>Descarga de los archivos del manifiesto o la lista de materiales

Los archivos del manifiesto o la lista de materiales (BOM) contienen la lista de archivos que se copian en Data Box o en Data Box Heavy. Estos archivos se generan para un pedido de importación al preparar el dispositivo para el envío.

Antes de empezar, siga estos pasos para descargar los archivos de manifiesto o la lista de materiales para el pedido de importación:

1. Vaya a la interfaz de usuario web local de su dispositivo. Compruebe que el dispositivo haya completado el paso **Preparación para el envío**. Una vez completada la preparación del dispositivo, su estado se muestra como **Listo para el envío**.

    ![Dispositivo listo para el envío](media/data-box-local-web-ui-admin/prepare-to-ship-3.png)

2. Seleccione **Descargar lista de archivos** para descargar la lista de archivos que se copiaron en Data Box.

    <!-- ![Select Download list of files](media/data-box-portal-admin/download-list-of-files.png) -->

3. En el Explorador de archivos se generan listas de archivos independientes según el protocolo usado para conectarse al dispositivo y el tipo de instancia de Azure Storage empleado.

    <!-- ![Files for storage type and connection protocol](media/data-box-portal-admin/files-storage-connection-type.png) -->
    ![Archivos para tipo de almacenamiento y protocolo de conexión](media/data-box-local-web-ui-admin/prepare-to-ship-5.png)

   La tabla siguiente asigna los nombres de archivo al tipo de instancia de Azure Storage y al protocolo de conexión utilizados.

    |Nombre de archivo  |Tipo de instancia de Azure Storage  |Protocolo de conexión utilizado |
    |---------|---------|---------|
    |utSAC1_202006051000_BlockBlob-BOM.txt     |Blobs en bloques         |SMB/NFS         |
    |utSAC1_202006051000_PageBlob-BOM.txt     |Blobs en páginas         |SMB/NFS         |
    |utSAC1_202006051000_AzFile-BOM.txt    |Azure Files         |SMB/NFS         |
    |utsac1_PageBlock_Rest-BOM.txt     |Blobs en páginas         |REST        |
    |utsac1_BlockBlock_Rest-BOM.txt    |Blobs en bloques         |REST         |

Esta lista sirve para comprobar qué archivos se han cargado en la cuenta de Azure Storage después de que Data Box vuelva al centro de datos de Azure. A continuación, se muestra un archivo de manifiesto de ejemplo.

> [!NOTE]
> En un dispositivo Data Box Heavy, los dos conjuntos de la lista de archivos (BOM) están presentes, correspondientes a los dos nodos en el dispositivo.

```xml
<file size="52689" crc64="0x95a62e3f2095181e">\databox\media\data-box-deploy-copy-data\prepare-to-ship2.png</file>
<file size="22117" crc64="0x9b160c2c43ab6869">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer2.png</file>
<file size="57159" crc64="0x1caa82004e0053a4">\databox\media\data-box-deploy-copy-data\verify-used-space-dashboard.png</file>
<file size="24777" crc64="0x3e0db0cd1ad438e0">\databox\media\data-box-deploy-copy-data\prepare-to-ship5.png</file>
<file size="162006" crc64="0x9ceacb612ecb59d6">\databox\media\data-box-cable-options\cabling-dhcp-data-only.png</file>
<file size="155066" crc64="0x051a08d36980f5bc">\databox\media\data-box-cable-options\cabling-2-port-setup.png</file>
<file size="150399" crc64="0x66c5894ff328c0b1">\databox\media\data-box-cable-options\cabling-with-switch-static-ip.png</file>
<file size="158082" crc64="0xbd4b4c5103a783ea">\databox\media\data-box-cable-options\cabling-mgmt-only.png</file>
<file size="148456" crc64="0xa461ad24c8e4344a">\databox\media\data-box-cable-options\cabling-with-static-ip.png</file>
<file size="40417" crc64="0x637f59dd10d032b3">\databox\media\data-box-portal-admin\delete-order1.png</file>
<file size="33704" crc64="0x388546569ea9a29f">\databox\media\data-box-portal-admin\clone-order1.png</file>
<file size="5757" crc64="0x9979df75ee9be91e">\databox\media\data-box-safety\japan.png</file>
<file size="998" crc64="0xc10c5a1863c5f88f">\databox\media\data-box-safety\overload_tip_hazard_icon.png</file>
<file size="5870" crc64="0x4aec2377bb16136d">\databox\media\data-box-safety\south-korea.png</file>
<file size="16572" crc64="0x05b13500a1385a87">\databox\media\data-box-safety\taiwan.png</file>
<file size="999" crc64="0x3f3f1c5c596a4920">\databox\media\data-box-safety\warning_icon.png</file>
<file size="1054" crc64="0x24911140d7487311">\databox\media\data-box-safety\read_safety_and_health_information_icon.png</file>
<file size="1258" crc64="0xc00a2d5480f4fcec">\databox\media\data-box-safety\heavy_weight_hazard_icon.png</file>
<file size="1672" crc64="0x4ae5cfa67c0e895a">\databox\media\data-box-safety\no_user_serviceable_parts_icon.png</file>
<file size="3577" crc64="0x99e3d9df341b62eb">\databox\media\data-box-safety\battery_disposal_icon.png</file>
<file size="993" crc64="0x5a1a78a399840a17">\databox\media\data-box-safety\tip_hazard_icon.png</file>
<file size="1028" crc64="0xffe332400278f013">\databox\media\data-box-safety\electrical_shock_hazard_icon.png</file>
<file size="58699" crc64="0x2c411d5202c78a95">\databox\media\data-box-deploy-ordered\data-box-ordered.png</file>
<file size="46816" crc64="0x31e48aa9ca76bd05">\databox\media\data-box-deploy-ordered\search-azure-data-box1.png</file>
<file size="24160" crc64="0x978fc0c6e0c4c16d">\databox\media\data-box-deploy-ordered\select-data-box-option1.png</file>
<file size="115954" crc64="0x0b42449312086227">\databox\media\data-box-disk-deploy-copy-data\data-box-disk-validation-tool-output.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
<file size="2823" crc64="0x63db1ada6fcdc672">\databox\index.yml</file>
<file size="4364" crc64="0x62b5710f58f00b8b">\databox\data-box-local-web-ui-admin.md</file>
<file size="3603" crc64="0x7e34c25d5606693f">\databox\TOC.yml</file>
```

Este archivo contiene la lista de todos los archivos que se copiaron en Data Box o en Data Box Heavy. En este archivo, el valor *crc64* está relacionado con la suma de comprobación que se genera para el archivo correspondiente.

## <a name="view-available-capacity-of-the-device"></a>Ver la capacidad disponible del dispositivo

Puede usar el panel del dispositivo para ver la capacidad disponible y utilizada del dispositivo.

1. En la interfaz de usuario web local, vaya a **Ver panel**.
2. En **Conectar y copiar**, se muestra el espacio disponible y utilizado del dispositivo.

    ![Ver la capacidad disponible](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)

## <a name="skip-checksum-validation"></a>Omitir la validación de la suma de comprobación

Las sumas de comprobación se generan para sus datos de forma predeterminada cuando se prepara el envío. En algunos casos poco frecuentes, según el tipo de datos (tamaños de archivo reducidos), el rendimiento puede ser lento. En tales casos, puede omitir la suma de comprobación.

El cálculo de la suma de comprobación durante la preparación para el envío solo se realiza en los pedidos de importación y no en los pedidos de exportación.

Se recomienda encarecidamente no deshabilitar la suma de comprobación, a menos que el rendimiento se vea afectado de forma considerable.

1. En la esquina superior derecha de la interfaz de usuario web local del dispositivo, vaya a **Configuración**.

    ![Deshabilitar la suma de comprobación](media/data-box-local-web-ui-admin/disable-checksum.png)

2. **Deshabilite** la validación de la suma de comprobación.
3. Seleccione **Aplicar**.

> [!NOTE]
> La opción para omitir el cálculo de la suma de comprobación solo está disponible cuando Azure Data Box está desbloqueado. No verá esta opción cuando el dispositivo esté bloqueado.

## <a name="enable-smb-signing"></a>Habilitación de la firma de SMB

La firma del bloque de mensajes del servidor (SMB) es una característica que permite firmar digitalmente las comunicaciones que usan SMB en el nivel de paquete. Esta firma evita ataques que modifican los paquetes SMB en tránsito.

Para obtener más información relacionada con la firma de SMB, consulte [Introducción a la firma del bloque de mensajes de servidor](https://support.microsoft.com/help/887429/overview-of-server-message-block-signing).

Para habilitar la firma de SMB en el dispositivo de Azure:

1. En la esquina superior derecha de la interfaz de usuario web local del dispositivo, seleccione **Configuración**.

    ![Abrir Configuración](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Habilite** la firma de SMB.

    ![Habilitación de la firma de SMB](media/data-box-local-web-ui-admin/data-box-smb-signing-1.png)

3. Seleccione **Aplicar**.
4. En la interfaz de usuario web local, vaya a **Apagar o reiniciar**.
5. Seleccione **Reiniciar**.

## <a name="enable-backup-operator-privileges"></a>Habilitación de los privilegios de operador de copia de seguridad

Los usuarios de la interfaz de usuario web tienen privilegios de operador de copia de seguridad en los recursos compartidos SMB de forma predeterminada. Si no desea esto, use **Enable Backup Operator privileges** (Habilitar los privilegios de operador de copia de seguridad) para deshabilitar o habilitar los privilegios.

Para más información, consulte Operadores de copia de seguridad en [Grupos de seguridad de Active Directory](/windows/security/identity-protection/access-control/active-directory-security-groups#backup-operators).

Para habilitar los privilegios de operador de copia de seguridad en el dispositivo de Azure:

1. En la esquina superior derecha de la interfaz de usuario web local del dispositivo, seleccione **Configuración**.

   ![Abrir configuración de Data Box: 1](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. Haga clic en **Enable** (Habilitar) para habilitar los privilegios de operador de copia de seguridad.

   ![Habilitación de los privilegios de operador de copia de seguridad](media/data-box-local-web-ui-admin/data-box-backup-operator-privileges-1.png)

3. Seleccione **Apply** (Aplicar).
4. En la interfaz de usuario web local, vaya a **Apagar o reiniciar**.
5. Seleccione **Reiniciar**.

## <a name="enable-acls-for-azure-files"></a>Habilitación de las ACL para Azure Files

Los metadatos de los archivos se transfieren de forma predeterminada cuando los usuarios cargan datos mediante SMB en Data Box. Los metadatos incluyen listas de control de acceso (ACL), atributos de archivo y marcas de tiempo. Si no quiere esto, use **ACLs for Azure Files** (ACL para Azure Files) para deshabilitar o habilitar esta característica.

<!--For more information about metadata that is transferred, see [Preserving the ACLs and metadata with Azure Data Box](./data-box-local-web-ui-admin.md#enable-backup-operator-privileges) - IN DEVELOPMENT-->

> [!Note]
> Para transferir metadatos con archivos, debe ser un operador de copia de seguridad. Al usar esta característica, asegúrese de que los usuarios locales de la interfaz de usuario web son operadores de copia de seguridad. Consulte [Habilitación de los privilegios de operador de copia de seguridad](#enable-backup-operator-privileges).

Para habilitar la transferencia de ACL para Azure Files:

1. En la esquina superior derecha de la interfaz de usuario web local del dispositivo, seleccione **Configuración**.

    ![Abrir configuración de Data Box: 2](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. Haga clic en **Enable** (Habilitar) para las ACL para Azure Files.

     ![Habilitación de las ACL para Azure Files](media/data-box-local-web-ui-admin/data-box-acls-for-azure-files-1.png)
  
3. Seleccione **Aplicar**.
4. En la interfaz de usuario web local, vaya a **Apagar o reiniciar**.
5. Seleccione **Reiniciar**.

## <a name="enable-tls-11"></a>Habilitación de TLS 1.1

De manera predeterminada, Azure Data Box usa Seguridad de la capa de transporte (TLS) 1.2 para el cifrado, ya que es más seguro que TSL 1.1. Pero si usted o sus clientes usan un explorador para acceder a datos que no admiten TLS 1.2, puede habilitar TLS 1.1.

Para obtener más información relacionada con TLS, consulte [Seguridad de Azure Data Box Gateway](../databox-gateway/data-box-gateway-security.md).

Para habilitar TLS 1.1 en el dispositivo de Azure:

1. En la esquina superior derecha de la interfaz de usuario web local del dispositivo, seleccione **Configuración**.

    ![Abrir configuración de Data Box: 3](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Habilite** TLS 1.1.

    ![Habilitación de TLS 1.1](media/data-box-local-web-ui-admin/data-box-tls-1-1.png)

3. Seleccione **Aplicar**.
4. En la interfaz de usuario web local, vaya a **Apagar o reiniciar**.
5. Seleccione **Reiniciar**.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [administrar Data Box y Data Box Heavy mediante Azure Portal](data-box-portal-admin.md).
