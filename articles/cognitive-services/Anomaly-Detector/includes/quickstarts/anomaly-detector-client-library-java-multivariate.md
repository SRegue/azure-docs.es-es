---
title: Inicio rápido de la biblioteca cliente multivariante de Java de Anomaly Detector
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2021
ms.author: mbullwin
ms.openlocfilehash: cbea7a93d80a0d8f68b23cbcfde92d34d5a0d1d0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802514"
---
Comience a usar la biblioteca cliente multivariante de Anomaly Detector para Java. Siga estos pasos para instalar el inicio del paquete con los algoritmos que proporciona el servicio. Las nuevas API de detección de anomalías multivariante permiten a los desarrolladores integrar fácilmente inteligencia artificial avanzada para detectar anomalías de grupos de métricas, sin necesidad de tener conocimientos de aprendizaje automático ni usar datos etiquetados. Las dependencias y correlaciones entre distintas señales se consideran automáticamente como factores clave. De este modo, podrá proteger de forma proactiva los sistemas complejos frente a errores.

Utilice la biblioteca cliente multivariante de Anomaly Detector para Java con el fin de:

* Detectar anomalías de nivel del sistema para un grupo de series temporales.
* Cuando una serie temporal individual no ofrece demasiada información y se deben examinar todas las señales para detectar un problema.
* Mantenimiento predictivo de recursos físicos costosos con decenas o cientos de tipos de sensores diferentes que miden diversos aspectos del estado del sistema.

[Documentación de referencia de la biblioteca](/java/api/com.azure.ai.anomalydetector) | [Código fuente de la biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/anomalydetector/azure-ai-anomalydetector) | [Paquete (Maven)](https://repo1.maven.org/maven2/com/azure/azure-ai-anomalydetector/3.0.0-beta.2/) | [Código de ejemplo](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/src/samples/java/com/azure/ai/anomalydetector/MultivariateSample.java)

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services)
* La versión actual de [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* La [herramienta de compilación de Gradle](https://gradle.org/install/) u otro administrador de dependencias.
* Cuando tenga la suscripción de Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Creación de un recurso de Anomaly Detector"  target="_blank">cree un recurso de Anomaly Detector </a> en Azure Portal para obtener la clave y el punto de conexión. Espere a que se implemente y haga clic en el botón **Ir al recurso**.
    * Necesitará la clave y el punto de conexión del recurso que cree para conectar la aplicación a Anomaly Detector API. En una sección posterior de este mismo inicio rápido pegará la clave y el punto de conexión en el código siguiente.
    Puede usar el plan de tarifa gratis (`F0`) para probar el servicio y actualizarlo más adelante a un plan de pago para producción.

## <a name="setting-up"></a>Instalación

### <a name="create-a-new-gradle-project"></a>Creación de un proyecto de Gradle

En este inicio rápido se usa el administrador de dependencias Gradle. Puede encontrar más información de la biblioteca cliente en [Maven Central Repository](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor).

En una ventana de la consola (como cmd, PowerShell o Bash), cree un directorio para la aplicación y vaya a él. 

```console
mkdir myapp && cd myapp
```

Ejecute el comando `gradle init` desde el directorio de trabajo. Este comando creará archivos de compilación esenciales para Gradle, como *build.gradle.kts*, que se usa en el runtime para crear y configurar la aplicación.

```console
gradle init --type basic
```

Cuando se le solicite que elija un **DSL**, seleccione **Kotlin**.

### <a name="install-the-client-library"></a>Instalación de la biblioteca cliente

Busque *build.gradle.kts* y ábralo con el IDE o el editor de texto que prefiera. A continuación, se copia en esta configuración de compilación. Asegúrese de incluir las dependencias del proyecto.

```kotlin
dependencies {
    compile("com.azure:azure-ai-anomalydetector")
}
```

### <a name="create-a-java-file"></a>Creación de un archivo Java

Cree una carpeta para la aplicación de ejemplo. En el directorio de trabajo, ejecute el siguiente comando:

```console
mkdir -p src/main/java
```

Vaya a la nueva carpeta y cree un archivo denominado *MetricsAdvisorQuickstarts.java*. Ábralo en el editor o el IDE que prefiera y agregue las siguientes instrucciones `import`:

```java
package com.azure.ai.anomalydetector;

import com.azure.ai.anomalydetector.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.*;
import com.azure.core.http.policy.*;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.PagedResponse;
import com.azure.core.http.rest.Response;
import com.azure.core.http.rest.StreamResponse;
import com.azure.core.util.Context;
import reactor.core.publisher.Flux;

import java.io.FileOutputStream;
import java.io.IOException;
import java.io.UncheckedIOException;
import java.nio.ByteBuffer;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.time.*;
import java.time.format.DateTimeFormatter;
import java.util.Iterator;
import java.util.List;
import java.util.UUID;
import java.util.stream.Collectors;
```

Cree variables para el punto de conexión y la clave de Azure del recurso. Cree otra variable para el archivo de datos de ejemplo.

> [!NOTE]
> Siempre tendrá la opción de usar una de estas dos claves. Esto es para permitir la rotación de claves segura. Para este inicio rápido, use la primera clave. 

```java
String key = "YOUR_API_KEY";
String endpoint = "YOUR_ENDPOINT";
```

Para usar las API multivariantes de Anomaly Detector, primero debe entrenar sus propios modelos. Los datos de entrenamiento son un conjunto de varias series temporales que cumplen los siguientes requisitos:

Cada serie temporal debe ser un archivo CSV con dos columnas (solo dos), "timestamp" y "value" (todo en minúsculas) como fila de encabezado. Los valores de "timestamp" deben cumplir la norma ISO 8601; los de "value" pueden ser números enteros o decimales con cualquier número de posiciones decimales. Por ejemplo:

|timestamp | value|
|-------|-------|
|2019-04-01T00:00:00Z| 5|
|2019-04-01T00:01:00Z| 3.6|
|2019-04-01T00:02:00Z| 4|
|`...`| `...` |

Cada archivo CSV debe tener el nombre de una variable diferente que se usará para el entrenamiento del modelo. Por ejemplo, "temperature.csv" y "humidity.csv". Todos los archivos CSV deben comprimirse en un archivo ZIP sin subcarpetas. El archivo ZIP puede tener el nombre que desee. El archivo ZIP debe cargarse en Azure Blob Storage. Una vez que genere la dirección URL de SAS de blob (firmas de acceso compartido) para el archivo ZIP, se puede usar para el entrenamiento. Consulte este documento para obtener información sobre cómo generar direcciones URL de SAS a partir de Azure Blob Storage.

## <a name="code-examples"></a>Ejemplos de código

Estos fragmentos de código muestran cómo realizar las siguientes acciones con la biblioteca cliente de Anomaly Detector para Node.js:

* [Autenticar el cliente](#authenticate-the-client)
* [Entrenamiento de un modelo](#train-a-model)
* [Detectar anomalías](#detect-anomalies)
* [Exportar un modelo](#export-model)
* [Eliminar un modelo](#delete-model)

## <a name="authenticate-the-client"></a>Autenticar el cliente

Cree una instancia de un objeto `anomalyDetectorClient` con el punto de conexión y las credenciales.

```java
HttpHeaders headers = new HttpHeaders()
    .put("Accept", ContentType.APPLICATION_JSON);

HttpPipelinePolicy authPolicy = new AzureKeyCredentialPolicy(key,
    new AzureKeyCredential(key));
AddHeadersPolicy addHeadersPolicy = new AddHeadersPolicy(headers);

HttpPipeline httpPipeline = new HttpPipelineBuilder().httpClient(HttpClient.createDefault())
    .policies(authPolicy, addHeadersPolicy).build();
// Instantiate a client that will be used to call the service.
HttpLogOptions httpLogOptions = new HttpLogOptions();
httpLogOptions.setLogLevel(HttpLogDetailLevel.BODY_AND_HEADERS);

AnomalyDetectorClient anomalyDetectorClient = new AnomalyDetectorClientBuilder()
    .pipeline(httpPipeline)
    .endpoint(endpoint)
    .httpLogOptions(httpLogOptions)
    .buildClient();
```

## <a name="train-a-model"></a>Entrenamiento de un modelo

### <a name="construct-a-model-result-and-train-model"></a>Construcción de un resultado del modelo y entrenamiento del modelo

En primer lugar, es necesario construir una solicitud de modelo. Asegúrese de que la hora de inicio y finalización se correspondan con su origen de datos.

Para usar las API multivariante de Anomaly Detector, es necesario entrenar nuestro modelo antes de usar la detección. Los datos usados para el entrenamiento son un lote de series temporales; cada serie temporal debe estar en un archivo CSV con solo dos columnas, **"timestamp"** y **"value"** (los nombres de columna deben ser exactamente iguales). Cada archivo CSV debe tener el nombre de cada variable para la serie temporal. Todas las series temporales deben comprimirse en un archivo ZIP y cargarse en [Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md#blobs). No existe ningún requisito para el nombre del archivo ZIP. Como alternativa, se puede incluir un archivo meta.json en el archivo ZIP si desea que el nombre de la variable sea diferente al nombre del archivo .zip. Una vez que se genera una [URL de SAS (firmas de acceso compartido) de blob](../../../../storage/common/storage-sas-overview.md), podemos usar la dirección URL para el archivo ZIP para el entrenamiento.

```java
Path path = Paths.get("test-data.csv");
List<String> requestData = Files.readAllLines(path);
List<TimeSeriesPoint> series = requestData.stream()
    .map(line -> line.trim())
    .filter(line -> line.length() > 0)
    .map(line -> line.split(",", 2))
    .filter(splits -> splits.length == 2)
    .map(splits -> {
        TimeSeriesPoint timeSeriesPoint = new TimeSeriesPoint();
        timeSeriesPoint.setTimestamp(OffsetDateTime.parse(splits[0]));
        timeSeriesPoint.setValue(Float.parseFloat(splits[1]));
        return timeSeriesPoint;
    })
    .collect(Collectors.toList());

Integer window = 28;
AlignMode alignMode = AlignMode.OUTER;
FillNAMethod fillNAMethod = FillNAMethod.LINEAR;
Integer paddingValue = 0;
AlignPolicy alignPolicy = new AlignPolicy()
                                .setAlignMode(alignMode)
                                .setFillNAMethod(fillNAMethod)
                                .setPaddingValue(paddingValue);
String source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
OffsetDateTime startTime = OffsetDateTime.of(2021, 1, 2, 0, 0, 0, 0, ZoneOffset.UTC);
OffsetDateTime endTime = OffsetDateTime.of(2021, 1, 3, 0, 0, 0, 0, ZoneOffset.UTC);
String displayName = "Devops-MultiAD";

ModelInfo request = new ModelInfo()
                        .setSlidingWindow(window)
                        .setAlignPolicy(alignPolicy)
                        .setSource(source)
                        .setStartTime(startTime)
                        .setEndTime(endTime)
                        .setDisplayName(displayName);
TrainMultivariateModelResponse trainMultivariateModelResponse = anomalyDetectorClient.trainMultivariateModelWithResponse(request, Context.NONE);
String header = trainMultivariateModelResponse.getDeserializedHeaders().getLocation();
String[] substring = header.split("/");
UUID modelId = UUID.fromString(substring[substring.length - 1]);
System.out.println(modelId);

//Check model status until the model is ready
Response<Model> trainResponse;
while (true) {
    trainResponse = anomalyDetectorClient.getMultivariateModelWithResponse(modelId, Context.NONE);
    ModelStatus modelStatus = trainResponse.getValue().getModelInfo().getStatus();
    if (modelStatus == ModelStatus.READY || modelStatus == ModelStatus.FAILED) {
        break;
    }
    TimeUnit.SECONDS.sleep(10);
}

if (trainResponse.getValue().getModelInfo().getStatus() != ModelStatus.READY){
    System.out.println("Training failed.");
    List<ErrorResponse> errorMessages = trainResponse.getValue().getModelInfo().getErrors();
    for (ErrorResponse errorMessage : errorMessages) {
        System.out.println("Error code:  " + errorMessage.getCode());
        System.out.println("Error message:  " + errorMessage.getMessage());
    }
}
```

## <a name="detect-anomalies"></a>Detección de anomalías

```java
DetectionRequest detectionRequest = new DetectionRequest().setSource(source).setStartTime(startTime).setEndTime(endTime);
DetectAnomalyResponse detectAnomalyResponse = anomalyDetectorClient.detectAnomalyWithResponse(modelId, detectionRequest, Context.NONE);
String location = detectAnomalyResponse.getDeserializedHeaders().getLocation();
String[] substring = location.split("/");
UUID resultId = UUID.fromString(substring[substring.length - 1]);

DetectionResult detectionResult;
while (true) {
    detectionResult = anomalyDetectorClient.getDetectionResult(resultId);
    DetectionStatus detectionStatus = detectionResult.getSummary().getStatus();;
    if (detectionStatus == DetectionStatus.READY || detectionStatus == DetectionStatus.FAILED) {
        break;
    }
    TimeUnit.SECONDS.sleep(10);
}

if (detectionResult.getSummary().getStatus() != DetectionStatus.READY){
    System.out.println("Inference failed");
    List<ErrorResponse> detectErrorMessages = detectionResult.getSummary().getErrors();
    for (ErrorResponse errorMessage : detectErrorMessages) {
        System.out.println("Error code:  " + errorMessage.getCode());
        System.out.println("Error message:  " + errorMessage.getMessage());
    }
}
```

## <a name="export-model"></a>Exportación de un modelo

> [!NOTE]
> El comando de exportación está diseñado para permitir la ejecución de modelos multivariados de Anomaly Detector en un entorno en contenedor. Actualmente, no se admite para el multivariado, pero se agregará una compatibilidad en el futuro.

Para exportar el modelo entrenado, use la función `exportModelWithResponse`.

```java
StreamResponse response_export = anomalyDetectorClient.exportModelWithResponse(model_id, Context.NONE);
Flux<ByteBuffer> value = response_export.getValue();
FileOutputStream bw = new FileOutputStream("result.zip");
value.subscribe(s -> write(bw, s), (e) -> close(bw), () -> close(bw));
```

## <a name="delete-model"></a>Eliminación del modelo

Para eliminar un modelo existente que esté disponible para el recurso actual, use la función `deleteMultivariateModelWithResponse`.

```java
Response<Void> deleteMultivariateModelWithResponse = anomalyDetectorClient.deleteMultivariateModelWithResponse(model_id, Context.NONE);
```

## <a name="run-the-application"></a>Ejecución de la aplicación

Puede compilar la aplicación con:

```console
gradle build
```
### <a name="run-the-application"></a>Ejecución de la aplicación

Antes de ejecutarla, puede resultar útil comprobar el código con el [código de ejemplo completo](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/src/samples/java/com/azure/ai/anomalydetector/MultivariateSample.java).

Ejecute la aplicación con el objetivo `run`:

```console
gradle run
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Si quiere limpiar y eliminar una suscripción a Cognitive Services, puede eliminar el recurso o grupo de recursos. Al eliminar el grupo de recursos, también se elimina cualquier otro recurso que esté asociado a dicho grupo.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI de Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Pasos siguientes

* [¿Qué es Anomaly Detector API?](../../overview-multivariate.md)
* [Procedimientos recomendados cuando se usa Anomaly Detector API.](../../concepts/best-practices-multivariate.md)
