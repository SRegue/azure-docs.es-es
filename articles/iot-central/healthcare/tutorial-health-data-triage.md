---
title: 'Tutorial: Creación de un panel de evaluación de prioridades de datos de salud con Azure IoT Central | Microsoft Docs'
description: 'Tutorial: Aprenda a crear un panel de evaluación de prioridades de datos de salud mediante plantillas de aplicación de Azure IoT Central.'
author: dominicbetts
ms.author: dobett
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: f8fee85dfab72594f7a00f985d7d095b96d693e6
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597244"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>Tutorial: Creación de un panel de proveedor de Power BI

Al crear la solución de supervisión continua de pacientes, puede crear también un panel para un equipo de cuidados hospitalarios para visualizar los datos de los pacientes. En este tutorial aprenderá a crear un panel de streaming en tiempo real de Power BI a partir de la plantilla de la aplicación de supervisión continua de pacientes de IoT Central.

:::image type="content" source="media/dashboard-gif-3.gif" alt-text="GIF del panel":::

La arquitectura básica seguirá esta estructura:

:::image type="content" source="media/dashboard-architecture.png" alt-text="Panel de proveedor de evaluación de prioridades":::

En este tutorial, aprenderá a:

- Exportación de datos de Azure IoT Central a Azure Event Hubs
- Configuración de un conjunto de datos de streaming de Power BI
- Conexión de la aplicación lógica a Azure Event Hubs
- Transmisión de datos a Power BI desde la aplicación lógica
- Creación de un panel en tiempo real para las constantes vitales de los pacientes


## <a name="prerequisites"></a>Requisitos previos

* Suscripción a Azure. Si no tiene una suscripción de Azure, [regístrese para obtener una cuenta gratuita de Azure](https://azure.microsoft.com/free/).

* Una plantilla de la aplicación de supervisión continua de pacientes de Azure IoT Central. Si aún no tiene una, puede seguir los pasos descritos en [Implementación de una plantilla de aplicación](overview-iot-central-healthcare.md).

* Un [espacio de nombres de Azure Event Hubs y un centro de eventos](../../event-hubs/event-hubs-create.md).

* La aplicación lógica que quiere que acceda al centro de eventos. Para iniciar la aplicación lógica con un desencadenador de Azure Event Hubs, necesita una [aplicación lógica en blanco](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Una cuenta del servicio Power BI. Si aún no tiene una, puede [crear una cuenta de evaluación gratuita para el servicio Power BI](https://app.powerbi.com/). Si no ha usado Power BI antes, puede resultarle útil consultar [Introducción a Power BI](/power-bi/service-get-started).


## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>Configuración de una exportación continua de datos a Azure Event Hubs

En primer lugar, debe configurar una exportación continua de datos desde la plantilla de la aplicación de Azure IoT Central al centro de eventos de Azure de su suscripción. Para ello, siga los pasos de este tutorial de Azure IoT Central para la [Exportación a Event Hubs](../core/howto-export-data.md). Solo tendrá que exportar los datos de telemetría para los fines de este tutorial.


## <a name="create-a-power-bi-streaming-dataset"></a>Creación de un conjunto de datos de streaming de Power BI

1. Inicie sesión en su cuenta de Power BI.

1. En el área de trabajo que prefiera, cree un nuevo conjunto de datos de streaming mediante la selección del botón **+ Crear** situado en la esquina superior derecha de la barra de herramientas. Tendrá que crear un conjunto de un conjunto de datos distinto para cada paciente que desee tener en el panel.

   :::image type="content" source="media/create-streaming-dataset.png" alt-text="Crear conjunto de datos de streaming":::


1. Elija **API** para el origen del conjunto de datos.

1. Escriba un **nombre** (por ejemplo, el nombre de un paciente) para el conjunto de datos y, a continuación, rellene los valores de la transmisión. Puede ver un ejemplo a continuación basado en los valores procedentes de los dispositivos simulados de la plantilla de la aplicación de supervisión continua de pacientes. El ejemplo tiene dos pacientes:

   * Teddy Silvers, que tiene datos del dispositivo Smart Knee Brace
   * Yesenia Sanford, que tiene datos del dispositivo Smart Vitals Patch

   :::image type="content" source="media/enter-dataset-values.png" alt-text="Especificar valores del conjunto de datos":::

Para más información sobre los conjuntos de datos de streaming en Power BI, puede leer este documento sobre [Streaming en tiempo real en Power BI](/power-bi/service-real-time-streaming).


## <a name="connect-your-logic-app-to-azure-event-hubs"></a>Conexión de la aplicación lógica a Azure Event Hubs

Para conectar la aplicación lógica a Azure Event Hubs, puede seguir las instrucciones que se describen en este documento sobre [Envío de eventos con Azure Event Hubs y Azure Logic Apps](../../connectors/connectors-create-api-azure-event-hubs.md#add-event-hubs-action). Estos son algunos parámetros sugeridos:

|Parámetro|Valor|
|---|---|
|Tipo de contenido|application/json|
|Intervalo|3|
|Frecuencia|Segundo|

Al final de este paso, el diseñador de la aplicación lógica debe tener el siguiente aspecto:

>[!div class="mx-imgBorder"] 
>![Conexión de Logic Apps a Event Hubs](media/eh-logic-app.png)

:::image type="content" source="media/enter-dataset-values.png" alt-text="Especificar valores del conjunto de datos":::


## <a name="stream-data-to-power-bi-from-your-logic-app"></a>Transmisión de datos a Power BI desde la aplicación lógica

El siguiente paso consiste en analizar los datos procedentes del centro de eventos para transmitirlos a los conjuntos de datos de Power BI que ha creado previamente.

Antes de poder hacerlo, debe comprender la carga JSON que se envía desde el dispositivo al centro de eventos. Para hacerlo, examine este [esquema de ejemplo](../core/howto-export-data.md#telemetry-format) y modifíquelo para que coincida con su esquema o utilice el [explorador de Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer) para inspeccionar los mensajes. Si usa las aplicaciones de supervisión continua de pacientes, los mensajes tendrán el siguiente aspecto:

**Telemetría del dispositivo Smart Vitals Patch**

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```

**Telemetría del dispositivo Smart Knee Brace**

```json
{
  "Acceleration": {
    "x": 72.73510947763711,
    "y": 72.73510947763711,
    "z": 72.73510947763711
  },
  "RangeOfMotion": 123,
  "KneeBend": 3
}
```

**Propiedades**

```json
{
  "iothub-connection-device-id": "1qsi9p8t5l2",
  "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",  \"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
  "iothub-connection-auth-generation-id": "637063718586331040",
  "iothub-enqueuedtime": 1571681440990,
  "iothub-message-source": "Telemetry",
  "iothub-interface-name": "Patient_health_data_3bk",
  "x-opt-sequence-number": 7,
  "x-opt-offset": "3672",
  "x-opt-enqueued-time": 1571681441317
}
```

1. Ahora que ha inspeccionado las cargas JSON, vuelva al diseñador de aplicaciones lógicas y seleccione **+ Nuevo paso**. Busque y agregue **Inicializar variable** como paso siguiente y especifique los parámetros siguientes:

   |Parámetro|Value|
   |---|---|
   |Nombre|Nombre de la interfaz|
   |Tipo|String|

   Seleccione **Guardar**. 

1. Agregue otra variable llamada **Body** con el tipo establecido como **Cadena**. Se agregarán estas acciones a la aplicación lógica:

   :::image type="content" source="media/initialize-string-variables.png" alt-text="Inicializar variables":::
    
1. Seleccione **+ Nuevo paso** y agregue una acción **Analizar JSON**. Cambie el nombre a **Parse Properties** (Analizar propiedades). Para el contenido, elija **Propiedades** procedentes del centro de eventos. Seleccione **Usar carga de ejemplo para generar esquema** en la parte inferior y pegue la carga de ejemplo de la sección de propiedades anterior.

1. A continuación, elija la acción **Establecer variable** y actualice la variable **Interface Name** con el valor **iothub-interface-name** de las propiedades JSON analizadas.

1. Agregue un control **Dividir** como la acción siguiente y elija la variable **Interface Name** como parámetro On. Lo utilizará para canalizar los datos al conjunto de datos correcto.

1. En la aplicación de Azure IoT Central, busque el nombre de la interfaz para los datos de salud de Smart Vitals Patch y los datos de salud de Smart Knee Brace en la vista **Device Templates** (Plantillas de dispositivo). Cree dos casos diferentes para el control **Modificador** para cada variable Interface Name y cambie el nombre del control adecuadamente. Puede establecer el Caso predeterminado para usar el control **Finalizar** y elegir el estado que le gustaría mostrar.

   :::image type="content" source="media/split-by-interface.png" alt-text="Control Dividir":::

1. En el caso de **Smart Vitals Patch**, agregue una acción **Analizar JSON**. Para el contenido, elija **Contenido** procedente del centro de eventos. Copie y pegue las cargas de ejemplo del dispositivo Smart Vitals Patch anterior para generar el esquema.

1. Agregue una acción **Establecer variable** y actualice la variable **Body** con el elemento **Body** del código JSON analizado en el paso 7.

1. Agregue un control **Condición** como la siguiente acción y establezca la condición en **Body**, **contiene**, **HeartRate** (Ritmo cardíaco). Esto garantizará que tiene el conjunto de datos correcto procedente del dispositivo Smart Vitals Patch antes de rellenar el conjunto de datos de Power BI. Los pasos del 7 al 9 tendrán el siguiente aspecto:

   :::image type="content" source="media/smart-vitals-pbi.png" alt-text="Condición de adición de Smart Vitals":::

1. En el caso de que la condición sea **True**, agregue una acción que llame a la funcionalidad de Power BI **Agregar filas a un conjunto de datos**. Tendrá que iniciar sesión en Power BI para ello. En el caso de que sea **False**, puede volver a usar el control **Finalizar**.

1. Elija el **Área de trabajo**, **Conjunto de datos** y **Tabla** adecuados. Asigne los parámetros que especificó al crear el conjunto de datos de streaming en Power BI a los valores JSON analizados que proceden del centro de eventos. Las acciones rellenadas deben tener este aspecto:
 
   :::image type="content" source="media/add-rows-yesenia.png" alt-text="Agregar filas a Power BI":::

1. En el caso del conmutador **Smart Knee Brace**, agregue una acción **Analizar JSON** para analizar el contenido, de forma similar al paso 7. A continuación, **Agregar filas a un conjunto de datos** para actualizar el conjunto de datos de Teddy Silvers en Power BI.

   :::image type="content" source="media/knee-brace-pbi.png" alt-text="Captura de pantalla que muestra cómo agregar filas a un conjunto de datos":::

1. Presione **Guardar** y, después, ejecute la aplicación lógica.


## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>Creación de un panel en tiempo real para las constantes vitales de los pacientes

Ahora vuelva a Power BI y seleccione **+ Crear** para crear un nuevo **Panel**. Asigne un nombre al panel y pulse **Crear**.

Seleccione los tres puntos en la barra de navegación superior y, a continuación, seleccione **+ Agregar icono**.

:::image type="content" source="media/add-tile.png" alt-text="Agregar icono al panel":::

Elija el tipo de icono que desea agregar y personalice la aplicación como desee.


## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, puede eliminar los recursos mediante los siguientes pasos:

1. En Azure Portal, puede eliminar el centro de eventos y los recursos de Logic Apps que creó.

1. Para la aplicación de IoT Central, vaya a la pestaña Administration (Administración) y seleccione **Delete** (Eliminar).

