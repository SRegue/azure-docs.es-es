---
title: 'Tutorial: Creación de una aplicación de análisis de vídeo con detección de objetos y movimiento en Azure IoT Central (YOLO v3)'
description: En este tutorial se muestra cómo crear una aplicación de análisis de vídeo en IoT Central. Se crea, se personaliza y se conecta a otros servicios de Azure. En este tutorial se usa el sistema de detección de objetos en tiempo real YOLO v3.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 09/01/2021
ms.openlocfilehash: 1517a2312751574e3d590c0fc27636c994e8c12c
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123479929"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central-yolo-v3"></a>Tutorial: Creación de una aplicación de análisis de vídeo con detección de objetos y movimiento en Azure IoT Central (YOLO v3)

Aprenda a crear una aplicación de análisis de vídeo con la plantilla de aplicación *Video analytics - object and motion detection* (Análisis de vídeo: detección de objetos y movimiento) de IoT Central, dispositivos Azure IoT Edge, Azure Media Services y el sistema de detección de objetos y movimiento en tiempo real YOLO v3. La solución usa un comercio minorista para mostrar cómo cumplir con la necesidad empresarial común de supervisar las cámaras de seguridad. La solución utiliza la detección automática de objetos de una fuente de vídeo para identificar y localizar rápidamente eventos de interés.

> [!TIP]
> Para usar OpenVINO&trade; en lugar de YOLO v3 para la detección de objetos y movimiento, consulte [Tutorial: Creación de una aplicación de análisis de vídeo con detección de objetos y movimiento en Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md).

[!INCLUDE [iot-central-video-analytics-part1](../../../includes/iot-central-video-analytics-part1.md)]

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt): este archivo le permite registrar las distintas opciones de configuración que necesitará a medida que avance en los tutoriales.
- [deployment.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deploymentManifests/deployment.amd64.json)
- [LvaEdgeGatewayDcm.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json): solo debe descargar este archivo si tiene previsto usar el dispositivo Intel NUC en el segundo tutorial.

> [!NOTE]
> El repositorio de GitHub también incluye el código fuente de los módulos IoT Edge **LvaEdgeGatewayModule** y **lvaYolov3**. Para más información sobre cómo trabajar con el código fuente, consulte [Compilación de los módulos de la puerta de enlace de LVA](tutorial-video-analytics-build-module.md).

[!INCLUDE [iot-central-video-analytics-part2](../../../includes/iot-central-video-analytics-part2.md)]

## <a name="edit-the-deployment-manifest"></a>Edición del manifiesto de implementación

Un módulo IoT Edge se implementa con un manifiesto de implementación. En IoT Central, puede importar el manifiesto como una plantilla de dispositivo y, a continuación, dejar que IoT Central administre la implementación del módulo.

Para preparar el manifiesto de implementación:

1. Abra el archivo *deployment.amd64.json*, que guardó en la carpeta *lva-configuration*, con un editor de texto.

1. Busque la opción de configuración `LvaEdgeGatewayModule` y compruebe que el nombre de la imagen coincida con el que se muestra en el siguiente fragmento de código:

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. Agregue el nombre de la cuenta de Media Services en el nodo `env` de la sección `LvaEdgeGatewayModule`. El nombre de la cuenta de Media Services se registró en el archivo *scratchpad.txt*:

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_SERVICES_ACCOUNT_NAME>"
        }
    }
    ```

1. La plantilla no expone estas propiedades en IoT Central, por lo que debe agregar los valores de configuración de Media Services al manifiesto de implementación. Busque el módulo `lvaEdge` y reemplace los marcadores de posición por los valores que anotó en el archivo *scratchpad.txt* cuando creó la cuenta de Media Services.

    El elemento `azureMediaServicesArmId` es el **identificador de recurso** que anotó en el archivo *scratchpad.txt* cuando creó la cuenta de Media Services.

    La tabla siguiente incluye los valores de **Connect to Media Services API (JSON)** en el archivo *scratchpad.txtscratchpad.txt* que se debe usar en el manifiesto de implementación:

    | Manifiesto de implementación       | Scratchpad  |
    | ------------------------- | ----------- |
    | aadTenantId               | AadTenantId |
    | aadServicePrincipalAppId  | AadClientId |
    | aadServicePrincipalSecret | AadSecret   |

    > [!CAUTION]
    > Use la tabla anterior para asegurarse de agregar los valores correctos en el manifiesto de implementación. De lo contrario, el dispositivo no funcionará.

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. Guarde los cambios.

En este tutorial se configura la solución para usar el módulo YOLO v3 para la detección de objetos y movimiento. El siguiente fragmento de código muestra la configuración del módulo:

```json
"lvaYolov3": {
    "settings": {
        "image": "mcr.microsoft.com/lva-utilities/yolov3-onnx:1.0"
    },
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "version": "1.0"
}
```

[!INCLUDE [iot-central-video-analytics-part3](../../../includes/iot-central-video-analytics-part3.md)]

### <a name="replace-the-manifest"></a>Reemplazo del manifiesto

En la página **LVA Edge Gateway v2** (Puerta de enlace LVA Edge v2), seleccione **+ Replace manifest** (+ Reemplazar manifiesto).

:::image type="content" source="./media/tutorial-video-analytics-create-app-yolo-v3/replace-manifest.png" alt-text="Reemplazar manifiesto":::

Vaya a la carpeta *lva-configuration* y seleccione el archivo de manifiesto *deployment.amd64.json* que editó previamente. Seleccione **Cargar**. Una vez completada la validación, seleccione **Reemplazar**.

[!INCLUDE [iot-central-video-analytics-part4](../../../includes/iot-central-video-analytics-part4.md)]

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ha terminado con la aplicación, puede eliminar todos los recursos que ha creado de la siguiente manera:

1. En la aplicación de IoT Central, vaya a la página **Your application** (Su aplicación) en la sección **Administration** (Administración). A continuación, seleccione **Eliminar**.
1. En Azure Portal, elimine el grupo de recursos **lva-rg**.
1. En el equipo local, detenga el contenedor de Docker **amp-viewer**.

## <a name="next-steps"></a>Pasos siguientes

Ahora ha creado una aplicación de IoT Central con la plantilla de aplicación **Video analytics - object and motion detection** (Análisis de vídeo: detección de objetos y movimiento), ha creado una plantilla de dispositivo para el dispositivo de puerta de enlace y ha agregado un dispositivo de puerta de enlace a la aplicación.

Si desea probar la aplicación de análisis de vídeo con detección de objetos y movimiento con módulos IoT Edge que ejecutan una máquina virtual en la nube con secuencias de vídeo simuladas:

> [!div class="nextstepaction"]
> [Creación de una instancia de IoT Edge para análisis de vídeo (máquina virtual Linux)](tutorial-video-analytics-iot-edge-vm.md)

Si desea probar la aplicación de análisis de vídeo con detección de objetos y movimiento con módulos IoT Edge que ejecutan un dispositivo real con una cámara **ONVIF** real:

> [!div class="nextstepaction"]
> [Creación de una instancia de IoT Edge para análisis de vídeo (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md)
