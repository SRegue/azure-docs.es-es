---
title: 'Tutorial: Configuración de certificados para un dispositivo de Azure Stack Edge Pro con GPU en Azure Portal | Microsoft Docs'
description: En este tutorial para implementar Azure Stack Edge Pro con GPU, se indica cómo configurar certificados en el dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/30/2020
ms.author: alkohli
ms.openlocfilehash: 2b63b496fb06c2987161661ddbbfcfe74580e3bf
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113356624"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-with-gpu"></a>Tutorial: Configuración de certificados para Azure Stack Edge Pro con GPU

En este tutorial se describe cómo puede configurar certificados para el dispositivo de Azure Stack Edge Pro con una GPU incorporada mediante la interfaz de usuario web local.

El tiempo necesario para este paso puede variar en función de la opción específica que elija y de cómo se establezca el flujo de certificados en su entorno.

En este tutorial, obtendrá información sobre lo siguiente:

> [!div class="checklist"]
>
> * Requisitos previos
> * Configuración de certificados para el dispositivo físico

## <a name="prerequisites"></a>Requisitos previos

Antes de configurar e instalar el dispositivo de Azure Stack Edge Pro con GPU, asegúrese de que:

* Ha instalado el dispositivo físico tal y como se describe en [Instalación de Azure Stack Edge Pro con GPU](azure-stack-edge-gpu-deploy-install.md).
* Si tiene previsto aportar sus propios certificados:
    - Debe tener los certificados preparados en el formato adecuado, incluido el certificado de la cadena de firma. Para obtener más información sobre el certificado, vaya a [Administración de certificados](azure-stack-edge-gpu-manage-certificates.md).

    - Si el dispositivo se implementa en Azure Government y no se implementa en la nube pública de Azure, se requerirá un certificado de cadena de firma para poder activar el dispositivo. 
    Para más detalles sobre el certificado, consulte [Administración de certificados](azure-stack-edge-gpu-manage-certificates.md).


## <a name="configure-certificates-for-device"></a>Configuración de certificados para el dispositivo

1. En la página **Certificados**, configurará los certificados. En función de si cambió el nombre del dispositivo o el dominio DNS en la página **Dispositivo**, puede elegir una de las siguientes opciones para los certificados.

    - Si no ha cambiado el nombre del dispositivo o el dominio DNS en el paso anterior y no desea aportar sus propios certificados, puede omitir este paso y continuar en el paso siguiente. El dispositivo ha generado automáticamente certificados autofirmados para comenzar. 

        ![Página "Certificados" de la interfaz de usuario web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-2.png)

    - Si cambió el nombre del dispositivo o el dominio DNS, verá que el estado de los certificados se mostrará como **No válido**. 

        ![Página 2 de "Certificados" de la interfaz de usuario web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1.png)    

        Seleccione un certificado para ver los detalles del estado.

        ![Página 3 de "Certificados" de la interfaz de usuario web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)  

        Esto se debe a que los certificados no reflejan el nombre del dispositivo y el dominio DNS actualizados (que se usan en el nombre del firmante y el nombre alternativo del firmante). Para activar correctamente el dispositivo, elija una de las siguientes opciones: 
    
        - **Generación de todos los certificados del dispositivo**. Estos certificados de dispositivo solo se deben usar para las pruebas, y no para las cargas de trabajo de producción. Para más información, vaya a [Generación de certificados de dispositivo en su instancia de Azure Stack Edge Pro con GPU](#generate-device-certificates).

        - **Aportación de sus propios certificados**. Puede aportar sus propios certificados de punto de conexión firmados y las cadenas de firma correspondientes. Primero debe agregar la cadena de firma y, a continuación, cargar los certificados de punto de conexión. **Se recomienda que siempre aporte sus propios certificados para cargas de trabajo de producción.** Para más información, vaya a [Aportación de sus propios certificados en un dispositivo de Azure Stack Edge Pro con GPU](#bring-your-own-certificates).
    
        - Puede aportar algunos de sus propios certificados y generar algunos certificados de dispositivo. La opción **Generar certificados** solo volverá a generar los certificados del dispositivo.

    - Si cambió el nombre del dispositivo o el dominio DNS, y no genera certificados ni aporta sus propios certificados, **se bloqueará la activación**.


### <a name="generate-device-certificates"></a>Generación de certificados de dispositivo

Siga estos pasos para generar certificados de dispositivo.

Siga estos pasos para volver a generar y descargar los certificados de dispositivo de Azure Stack Edge Pro con GPU:

1. En la interfaz de usuario local del dispositivo, vaya a **Configuración > Certificados**. Seleccione **Generar certificados**.

    ![Generación y descarga del certificado 1](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-3.png)

2. En **Generar certificados** del dispositivo, seleccione **Generar**. 

    ![Generación y descarga del certificado 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-4.png)

    Se generan y aplican los certificados de dispositivo. La generación y aplicación de los certificados tarda unos minutos.
    
    > [!IMPORTANT]
    > Mientras la operación de generación de certificados está en curso, no aporte sus propios certificados e intente agregarlos a través de la opción **+ Agregar certificado**.

    Recibirá una notificación cuando la operación se haya completado correctamente. **Para evitar posibles problemas de caché, reinicie el explorador.**
    
    ![Generación y descarga del certificado 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-5.png)

3. Una vez generados los certificados: 

    - El estado de todos los certificados se muestra como **Válido**. 

        ![Generación y descarga del certificado 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6.png)

    - Puede seleccionar un nombre de certificado específico y ver los detalles de dicho certificado. 

        ![Generación y descarga del certificado 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6a.png)

    - Ya está rellenada la columna **Descarga**. Esta columna contiene vínculos para descargar los certificados regenerados. 

        ![Generación y descarga del certificado 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6b.png)


4. Seleccione el vínculo de descarga de un certificado y, cuando se le pida, guarde el certificado. 

    ![Generación y descarga del certificado 8](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-7.png)

5. Repita este proceso para todos los certificados que desee descargar. 
    
    ![Generación y descarga del certificado 9](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-8.png)

    Los certificados generados por el dispositivo se guardan como certificados DER con el siguiente formato de nombre: 

    `<Device name>_<Endpoint name>.cer`. Estos certificados contienen la clave pública para los certificados correspondientes instalados en el dispositivo. 

Deberá instalar estos certificados en el sistema cliente que usa para acceder a los puntos de conexión en el dispositivo Azure Stack Edge. Estos certificados establecen confianza entre el cliente y el dispositivo.

Para importar e instalar estos certificados en el cliente que usa para acceder al dispositivo, siga los pasos descritos en [Importación de certificados en los clientes que tienen acceso al dispositivo de Azure Stack Edge Pro con GPU](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device). 

Si usa el Explorador de Azure Storage, tendrá que instalar los certificados en el cliente en formato PEM y deberá convertir los certificados generados por el dispositivo a formato PEM. 

> [!IMPORTANT]
> - El vínculo de descarga solo está disponible para los certificados generados por el dispositivo y no si aporta sus propios certificados.
> - Puede optar por una combinación de certificados generados por el dispositivo y sus propios certificados, siempre y cuando se cumplan otros requisitos de certificado. Para más información, vaya a [Requisitos de certificado](azure-stack-edge-gpu-certificate-requirements.md).
    

### <a name="bring-your-own-certificates"></a>Aportación de sus propios certificados

Puede aportar sus propios certificados. 

- Para empezar, comprenda los [tipos de certificados que se pueden usar con un dispositivo de Azure Stack Edge](azure-stack-edge-gpu-certificates-overview.md).
- A continuación, revise los [requisitos de certificado para cada tipo de certificado](azure-stack-edge-gpu-certificate-requirements.md).
- Luego puede [crear los certificados mediante Azure PowerShell](azure-stack-edge-gpu-create-certificates-powershell.md) o [crear los certificados mediante la herramienta Readiness Checker](azure-stack-edge-gpu-create-certificates-tool.md).
- Por último, [convierta los certificados al formato adecuado](azure-stack-edge-gpu-prepare-certificates-device-upload.md) para que estén listos para cargarlos en el dispositivo.

Siga estos pasos para cargar sus propios certificados, incluida la cadena de firma.

1. Para cargar el certificado, en la página **Certificado**, seleccione **+ Agregar certificado**.

    ![Página 4 de "Certificados" de la interfaz de usuario web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-1.png)

2. Cargue primero la cadena de firma y seleccione **Validar y agregar**.

    ![Página 5 de "Certificados" de la interfaz de usuario web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-2.png)

3. Ahora puede cargar otros certificados. Por ejemplo, puede cargar los certificados de punto de conexión de Blob Storage y Azure Resource Manager.

    ![Página 6 de "Certificados" de la interfaz de usuario web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-3.png)

    También puede cargar el certificado de la interfaz de usuario web local. Después de cargar este certificado, se le pedirá que inicie el explorador y borre la memoria caché. A continuación, tendrá que conectarse a la interfaz de usuario web local del dispositivo.  

    ![Página 7 de "Certificados" de la interfaz de usuario web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-5.png)

    También puede cargar el certificado de nodo.

    ![Página 8 de "Certificados" de la interfaz de usuario web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-4.png)

    En cualquier momento, puede seleccionar un certificado y ver sus detalles para asegurarse de que coinciden con el certificado que ha cargado.

    ![Página 9 de "Certificados" de la interfaz de usuario web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-6.png)

    La página del certificado debe actualizarse para reflejar los certificados recién agregados.

    ![Página 10 de "Certificados" de la interfaz de usuario web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-7.png)  

    > [!NOTE]
    > A excepción de la nube pública de Azure, es necesario que los certificados de la cadena de firma se agreguen antes de la activación de todas las configuraciones de nube (Azure Government o Azure Stack).


El dispositivo ya está listo para activarse. Seleccione **< Atrás para comenzar**.


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, obtendrá información sobre lo siguiente:

> [!div class="checklist"]
>
> * Requisitos previos
> * Configuración de certificados para el dispositivo físico

Para obtener información sobre cómo activar el dispositivo Azure Stack Edge Pro con GPU, consulte:

> [!div class="nextstepaction"]
> [Activación del dispositivo Azure Stack Edge Pro con GPU](./azure-stack-edge-gpu-deploy-activate.md)
