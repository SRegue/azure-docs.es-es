---
title: 'Tutorial: Modificación de los módulos de análisis de vídeo en directo de Azure IoT Edge'
description: En este tutorial se muestra cómo modificar y compilar los módulos de la puerta de enlace de análisis de vídeo en directo que usa la plantilla de aplicación de análisis de vídeo con detección de objetos y movimiento.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 09/01/2021
ms.openlocfilehash: 8f37c25eb627442e18fbcc688c0db20369f872a4
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123474493"
---
# <a name="tutorial-modify-and-build-the-live-video-analytics-gateway-modules"></a>Tutorial: Modificación y compilación de los módulos de la puerta de enlace de análisis de vídeo en directo

En este tutorial se muestra cómo modificar el código del módulo IoT Edge para los módulos de análisis de vídeo en directo (LVA).

En los tutoriales anteriores se usan imágenes precompiladas de los módulos.

## <a name="prerequisites"></a>Requisitos previos

Para completar los pasos de este tutorial, necesitará lo siguiente:


> [!div class="checklist"]
> * [Node.js](https://nodejs.org/en/download/) versión 10 o posterior.
> * [Visual Studio Code](https://code.visualstudio.com/Download) con la extensión [TSLint](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-tslint-plugin) instalada.
> * El motor de [Docker](https://www.docker.com/products/docker-desktop).
> * Una instancia de [Azure Container Registry](../../container-registry/index.yml) para hospedar las versiones de los módulos.
> * Una cuenta de [Azure Media Services](../../media-services/index.yml). Si completó los tutoriales anteriores, puede volver a usar la que ha creado anteriormente.

## <a name="clone-the-repository"></a>Clonación del repositorio

Si aún no ha clonado el repositorio, use el comando siguiente para clonarlo en una ubicación adecuada en el equipo local:

```cmd
git clone https://github.com/Azure/live-video-analytics
```

Abra la carpeta del repositorio local *live-video-analytics* con VS Code.

## <a name="edit-the-deploymentamd64json-file"></a>Edición del archivo deployment.amd64.json

1. Si todavía no lo ha hecho, cree una carpeta llamada *ref-apps/lva-edge-iot-central-gateway/storage* en la copia local del repositorio **lva-gateway**. Git omite esta carpeta para evitar que se inserte accidentalmente en el repositorio cualquier información confidencial.
1. Copie el archivo *deployment.amd64.json* desde la carpeta *setup* a la carpeta *storage*.
1. En VS Code, abra el archivo *storage/deployment.amd64.json*.
1. Edite la sección `registryCredentials` para agregar las credenciales de Azure Container Registry.
1. Edite la sección del módulo `LvaEdgeGatewayModule` para agregar el nombre de la imagen y el nombre de la cuenta de AMS en el elemento `env:amsAccountName:value`.
1. Edite la sección del módulo `lvaYolov3` y agregue el nombre de la imagen.
1. Edite la sección del módulo `lvaEdge` y agregue el nombre de la imagen.
1. Consulte [Creación de una aplicación de análisis de vídeo en Azure IoT Central](tutorial-video-analytics-create-app-yolo-v3.md) para más información sobre cómo completar la configuración.

## <a name="build-the-code"></a>Compilación del código

1. Antes de intentar compilar el código por primera vez, use el terminal de VS Code para ejecutar el comando `npm install`. Este comando instala los paquetes necesarios y ejecuta los scripts de instalación.

    > [!TIP]
    > Si extrae una versión más reciente del repositorio de GitHub, elimine la carpeta *node_modules* y ejecute `npm install` de nuevo.

1. Edite el archivo *./setup/imageConfig.json* para actualizar la imagen con el nombre en función del nombre del registro de contenedor:

    ```json
    {
        "arch": "amd64",
        "imageName": "[Server].azurecr.io/lva-edge-gateway"
    }
    ```

1. Use el terminal de VS Code para ejecutar el comando `docker login [your server].azurecr.io`. Use las mismas credenciales que proporcionó en el manifiesto de implementación para los módulos.

1. Use el terminal de VS Code para ejecutar el comando **npm version patch**. Este script de compilación implementa las imágenes en el registro de contenedor. La salida de la ventana del terminal de VS Code muestra si la compilación se ha realizado correctamente.

1. La versión de la imagen **LvaEdgeGatewayModule** se incrementa cada vez que se completa la compilación. Debe usar esta versión en el archivo de manifiesto de implementación.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ha terminado con la aplicación, puede eliminar todos los recursos que ha creado de la siguiente manera:

1. En la aplicación de IoT Central, vaya a la página **Your application** (Su aplicación) en la sección **Administration** (Administración). A continuación, seleccione **Eliminar**.
1. En Azure Portal, elimine el grupo de recursos **lva-rg**.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido acerca de la plantilla de aplicación de análisis de vídeo con detección de objetos y movimiento y los módulos IoT Edge de LVA, el siguiente paso sugerido es consultar:

> [!div class="nextstepaction"]
> [Creación de soluciones de comercio minorista con Azure IoT Central](overview-iot-central-retail.md)