---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 7b7ac037522c52aa4713973d1a056a03de5918bd
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2021
ms.locfileid: "114403799"
---
En este inicio rápido se muestra cómo compilar una aplicación de dispositivo IoT Plug and Play de ejemplo, conectarla al centro de IoT y usar la herramienta Azure IoT Explorer para ver los datos de telemetría que envía. La aplicación de ejemplo está escrita en C y se incluye en el SDK de dispositivo IoT de Azure para C. Un generador de soluciones puede usar la herramienta Azure IoT Explorer para comprender las funcionalidades de cualquier dispositivo IoT Plug and Play sin necesidad de ver nada de código del dispositivo.

[![Examinar el código](../articles/iot-central/core/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp)

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Puede ejecutar este inicio rápido en Linux o Windows. Los comandos de shell de este inicio rápido siguen la convención de Linux para los separadores de ruta de acceso "`/`". Si lo sigue en Windows, asegúrese de cambiar estos separadores por "`\`".

Los requisitos previos difieren en función del sistema operativo:

### <a name="linux"></a>Linux

En este inicio rápido se asume que utiliza Ubuntu Linux. Los pasos de este inicio rápido se han probado con Ubuntu 18.04.

Para completar este inicio rápido en Linux, es preciso instalar el siguiente software en el entorno Linux local:

Instale **GCC**, **Git**, **cmake** y todas las dependencias necesarias con el comando `apt-get`:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Compruebe que la versión de `cmake` es posterior a **2.8.12** y que la versión de **GCC** es posterior a **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Para completar este inicio rápido en Windows, instale el siguiente software en el entorno Windows local:

* [Visual Studio (Community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/): asegúrese de incluir la carga de trabajo **Desarrollo para el escritorio con C++** cuando [instale](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

## <a name="download-the-code"></a>Descargar el código

En este inicio rápido, se prepara un entorno de desarrollo que se puede usar para clonar y compilar el SDK de C del dispositivo de Azure IoT Hub.

Abra un símbolo del sistema en el directorio que prefiera. Ejecute el siguiente comando para clonar el repositorio de GitHub de las [bibliotecas y los SDK de C de Azure IoT](https://github.com/Azure/azure-iot-sdk-c) en esta ubicación:

```cmd\bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

Esta operación puede tardar varios minutos en completarse.

## <a name="build-the-code"></a>Compilación del código

Para compilar el código de ejemplo incluido, se usa el SDK del dispositivo:

1. Cree un subdirectorio _cmake_ en la carpeta raíz del SDK del dispositivo y vaya a esa carpeta:

    ```cmd\bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Ejecute los siguientes comandos para compilar el SDK y los ejemplos:

    ```cmd\bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key=ON -Drun_e2e_tests=OFF ..
    cmake --build .
    ```

> [!TIP]
> En Windows, puede abrir la solución generada por el comando `cmake` en Visual Studio 2019. Abra el archivo de proyecto *azure_iot_sdks.sln* en el directorio _cmake_ y establezca el proyecto **pnp_simple_thermostat** como proyecto de inicio en la solución. Ahora puede compilar el ejemplo en Visual Studio y ejecutarlo en modo de depuración.

## <a name="run-the-device-sample"></a>Ejecución del ejemplo de dispositivo

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Para más información sobre la configuración de ejemplo, consulte el [archivo Léame de ejemplo](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md).

Ejecute la aplicación de ejemplo en el SDK que simula un dispositivo IoT Plug and Play que envía telemetría a su centro de IoT:

Desde la carpeta _cmake_, navegue hasta la carpeta que contiene el archivo ejecutable y ejecute lo siguiente:

```bash
# Bash
cd iothub_client/samples/pnp/pnp_simple_thermostat/
./pnp_simple_thermostat
```

```cmd
REM Windows
cd iothub_client\samples\pnp\pnp_simple_thermostat\Debug
.\pnp_simple_thermostat.exe
```

> [!TIP]
> Para realizar el seguimiento de la ejecución del código en Visual Studio en Windows, agregue un punto de interrupción a la función `main` en el archivo _pnp_simple-thermostat.c_.

El dispositivo ya está listo para recibir comandos y actualizaciones de propiedades, y ha empezado a enviar datos de telemetría al centro. Siga ejecutando el ejemplo a medida que complete los pasos siguientes.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Uso de Azure IoT Explorer para validar el código

Una vez iniciado el ejemplo de cliente de dispositivo, compruebe que funciona con la herramienta Azure IoT Explorer.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Revisión del código

En este ejemplo se implementa un dispositivo termostato de IoT Plug and Play sencillo. El modelo que se implementa en este ejemplo no usa los [componentes](../articles/iot-develop/concepts-modeling-guide.md) de IoT Plug and Play. El [archivo de modelo DTDL del dispositivo termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) define la telemetría, las propiedades y los comandos que implementa el dispositivo.

El código del dispositivo usa la función estándar para conectarse a su centro de IoT:

```c
deviceHandle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, MQTT_Protocol)
```

El dispositivo envía el identificador de modelo del modelo DTDL que implementa en la solicitud de conexión. Un dispositivo que envía un identificador de modelo es un dispositivo IoT Plug and Play:

```c
static const char g_ModelId[] = "dtmi:com:example:Thermostat;1";

...

IoTHubDeviceClient_SetOption(deviceHandle, OPTION_MODEL_ID, modelId)
```

El código que actualiza las propiedades, controla los comandos y envía la telemetría es idéntico al código de un dispositivo que no usa las convenciones de IoT Plug and Play.

El código usa una biblioteca de Parson para analizar objetos JSON en las cargas enviadas desde el centro de IoT:

```c
// JSON parser
#include "parson.h"
```
