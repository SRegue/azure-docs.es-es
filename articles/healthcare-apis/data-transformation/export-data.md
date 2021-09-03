---
title: Ejecución de la exportación mediante la invocación del comando $export en el servicio FHIR
description: En este artículo se describe cómo exportar datos de FHIR mediante $export
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 5/25/2021
ms.author: cavoeg
ms.openlocfilehash: e161834e7b0d503e508f02260a150237f8345342
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780720"
---
# <a name="how-to-export-fhir-data"></a>Exportación de datos de FHIR


La característica de exportación masiva permite exportar datos desde el servidor de FHIR según la [especificación FHIR](https://hl7.org/fhir/uv/bulkdata/export/index.html). 

Antes de usar el comando $export, tendrá que asegurarse de que el servicio FHIR esté configurado para usarlo. Para configurar las opciones de exportación y crear una cuenta de almacenamiento de Azure, consulte la [página de configuración de los datos de exportación](configure-export-data.md).

## <a name="using-export-command"></a>Uso del comando $export

Después de configurar el servicio FHIR para la exportación, puede usar el comando $export para exportar los datos fuera del servicio. Los datos se almacenarán en la cuenta de almacenamiento que especificó al configurar la exportación. Para más información sobre cómo invocar el comando $export en el servidor de FHIR, lea la documentación sobre la [especificación de $export del estándar FHIR de HL7](https://hl7.org/Fhir/uv/bulkdata/export/index.html).


**Trabajos bloqueados en estado incorrecto**

En algunas situaciones, existe la posibilidad de que un trabajo se quede bloqueado en un estado incorrecto. Esto puede ocurrir especialmente si los permisos de la cuenta de almacenamiento no se han configurado correctamente. Una manera de validar si la exportación se realiza correctamente es comprobar la cuenta de almacenamiento para ver si están presentes los archivos de contenedor correspondientes (es decir, ndjson). Si no están presentes y no hay ningún otro trabajo de exportación en ejecución, existe la posibilidad de que el trabajo actual se haya quedado bloqueado en un estado incorrecto. Debe cancelar el trabajo de exportación mediante el envío de una solicitud de cancelación e intentar volver a poner el trabajo en cola. El tiempo de ejecución predeterminado para una exportación en estado incorrecto es de 10 minutos antes de que se detenga y se mueva a un nuevo trabajo o la exportación se vuelva a intentar. 

El servicio FHIR admite $export en los siguientes niveles:
* [Sistema](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export): `GET https://<<FHIR service base URL>>/$export>>`
* [Paciente](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients): `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [Grupo de pacientes*](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients): el servicio FHIR exporta todos los recursos relacionados, pero no exporta las características del grupo: `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`

Cuando se exportan los datos, se crea un archivo independiente para cada tipo de recurso. Para garantizar que los archivos exportados no se vuelvan demasiado grandes, se crea un nuevo archivo cuando el tamaño de un único archivo exportado supera los 64 MB. El resultado es que puede obtener varios archivos para cada tipo de recurso, los cuales se enumerarán (es decir, Patient-1.ndjson, Patient-2.ndjson). 


> [!Note] 
> `Patient/$export` y `Group/[ID]/$export` pueden exportar recursos duplicados si el recurso está en un compartimiento de más de un recurso o se encuentra en varios grupos.

Además, se admite la comprobación del estado de exportación a través de la dirección URL devuelta por el encabezado de ubicación durante la puesta en cola, junto con la cancelación del trabajo de exportación real.

### <a name="exporting-fhir-data-to-adls-gen2"></a>Exportación de datos de FHIR a ADLS Gen2

Actualmente se admiten $export para las cuentas de almacenamiento habilitadas para ADLS Gen2, con la siguiente limitación:

- El usuario no puede aprovechar todavía los [espacios de nombres jerárquicos](../../storage/blobs/data-lake-storage-namespace.md); no hay ninguna manera de destinar la exportación a un subdirectorio específico dentro del contenedor. Solo se ofrece la posibilidad de destinar a un contenedor específico (donde crearemos una carpeta para cada exportación).
- Una vez completada la exportación, nunca se exporta nada de nuevo a esa carpeta, ya que las exportaciones posteriores al mismo contenedor estarán dentro de una carpeta recién creada.


## <a name="settings-and-parameters"></a>Configuración y parámetros

### <a name="headers"></a>Encabezados
Hay dos parámetros de encabezado necesarios que se deben establecer para los trabajos de $export. Los valores se definen mediante la [especificación de $export](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers) actual.
* **Accept** (Aceptar): application/fhir+json.
* **Prefer** (Preferir): respond-async.

### <a name="query-parameters"></a>Parámetros de consulta
El servicio FHIR admite los siguientes parámetros de consulta. Todos estos parámetros son opcionales:

|Parámetro de consulta        | ¿Se define mediante la especificación de FHIR?    |  Descripción|
|------------------------|---|------------|
| \_outputFormat | Sí | Actualmente admite tres valores que se alinean con la especificación de FHIR: application/fhir+ndjson, application/ndjson o just ndjson. Todos los trabajos de exportación devolverán `ndjson` y el valor pasado no tiene ningún efecto sobre el comportamiento del código. |
| \_since | Sí | Permite exportar solo los recursos que se han modificado desde el momento indicado. |
| \_type | Sí | Permite especificar los tipos de recursos que se van a incluir. Por ejemplo, \_type=Patient devolvería solo los recursos de pacientes.|
| \_typefilter | Sí | Para solicitar un filtrado más preciso, puede usar \_typefilter junto con el parámetro \_type. El valor del parámetro _typeFilter es una lista separada por comas de consultas de FHIR que restringen aún más los resultados. |
| \_container | No |  Especifica el contenedor en la cuenta de almacenamiento configurada donde se deben exportar los datos. Si se especifica un contenedor, los datos se exportarán a una carpeta en ese contenedor. Si no se especifica el contenedor, los datos se exportarán a un nuevo contenedor. |

> [!Note]
> Solo las cuentas de almacenamiento de la misma suscripción que el servicio FHIR pueden registrarse como destino para operaciones $export.

## <a name="secure-export-to-azure-storage"></a>Exportación segura a Azure Storage

El servicio FHIR admite una operación de exportación segura. Elija una de las dos opciones siguientes:

* Permitir el servicio FHIR como servicio de confianza de Microsoft para acceder a la cuenta de almacenamiento de Azure.
 
* Permitir direcciones IP específicas asociadas al servicio FHIR para acceder a la cuenta de almacenamiento de Azure. Esta opción proporciona dos configuraciones diferentes en función de si la cuenta de almacenamiento está en la misma ubicación que el servicio FHIR o en una diferente.

### <a name="allowing-fhir-service-as-a-microsoft-trusted-service"></a>Permitir el servicio FHIR como servicio de confianza de Microsoft

Seleccione una cuenta de almacenamiento en Azure Portal y luego seleccione la hoja **Redes**. Seleccione **Redes seleccionadas** en la pestaña **Firewalls y redes virtuales**.

> [!IMPORTANT]
> Asegúrese de que ha concedido permiso de acceso a la cuenta de almacenamiento para el servicio FHIR mediante su identidad administrada. Para obtener más información, consulte [Definición de la configuración de la exportación y de la configuración de la cuenta de almacenamiento](./configure-export-data.md).

  :::image type="content" source="media/export-data/storage-networking.png" alt-text="Configuración de redes de Azure Storage." lightbox="media/export-data/storage-networking.png":::

En la sección **Excepciones**, seleccione **Permitir que los servicios de Microsoft de confianza accedan a esta cuenta de almacenamiento** y guarde la configuración. 

:::image type="content" source="media/export-data/exceptions.png" alt-text="Permitir que los servicios Microsoft de confianza accedan a esta cuenta de almacenamiento":::.

Ya está listo para exportar datos de FHIR a la cuenta de almacenamiento de forma segura. Tenga en cuenta que la cuenta de almacenamiento está en redes seleccionadas y no es accesible públicamente. Para acceder a los archivos, puede habilitar y usar puntos de conexión privados para la cuenta de almacenamiento, o bien habilitar todas las redes para la cuenta de almacenamiento durante un breve período de tiempo.

> [!IMPORTANT]
> La interfaz de usuario se actualizará más adelante para que pueda seleccionar el tipo de recurso para el servicio FHIR y una instancia de servicio específica.

### <a name="allowing-specific-ip-addresses-for-the-azure-storage-account-in-a-different-region"></a>Permitir direcciones IP específicas para la cuenta de almacenamiento de Azure en una región diferente

Seleccione **Redes** de la cuenta de almacenamiento de Azure en el portal. 
   
Seleccione **Redes seleccionadas**. En la sección Firewall, especifique la dirección IP en el cuadro **Intervalo de direcciones**. Agregue intervalos IP para permitir el acceso desde Internet o redes locales. Puede encontrar la dirección IP en la tabla siguiente para la región de Azure donde se aprovisiona el servicio de FHIR.

|**Región de Azure**         |**Dirección IP pública** |
|:----------------------|:-------------------|
| Este de Australia       | 20.53.44.80       |
| Centro de Canadá       | 20.48.192.84      |
| Centro de EE. UU.           | 52.182.208.31     |
| Este de EE. UU.              | 20.62.128.148     |
| Este de EE. UU. 2            | 20.49.102.228     |
| EUAP de Este de EE. UU. 2       | 20.39.26.254      |
| Norte de Alemania        | 51.116.51.33      |
| Centro-oeste de Alemania | 51.116.146.216    |
| Japón Oriental           | 20.191.160.26     |
| Centro de Corea del Sur        | 20.41.69.51       |
| Centro-Norte de EE. UU     | 20.49.114.188     |
| Norte de Europa         | 52.146.131.52     |
| Norte de Sudáfrica   | 102.133.220.197   |
| Centro-sur de EE. UU.     | 13.73.254.220     |
| Sudeste de Asia       | 23.98.108.42      |
| Norte de Suiza    | 51.107.60.95      |
| Sur de Reino Unido             | 51.104.30.170     |
| Oeste de Reino Unido              | 51.137.164.94     |
| Centro-Oeste de EE. UU.      | 52.150.156.44     |
| Oeste de Europa          | 20.61.98.66       |
| Oeste de EE. UU. 2            | 40.64.135.77      |

> [!NOTE]
> Los pasos anteriores son similares a los pasos de configuración descritos en el documento Conversión de datos a FHIR (versión preliminar). Para obtener más información, consulte [Hospedaje y uso de plantillas](./convert-data.md#host-and-use-templates).

### <a name="allowing-specific-ip-addresses-for-the-azure-storage-account-in-the-same-region"></a>Permitir direcciones IP específicas para la cuenta de almacenamiento de Azure en la misma región

El proceso de configuración es el mismo que el anterior, salvo que en su lugar se usa un intervalo de direcciones IP específico en formato CIDR, 100.64.0.0/10. La razón por la que se debe especificar el intervalo de direcciones IP, que incluye 100.64.0.0 a 100.127.255.255, es que la dirección IP real utilizada por el servicio varía, aunque lo hará dentro del intervalo, para cada solicitud de $export.

> [!Note] 
> En su lugar, es posible que se use una dirección IP privada dentro del intervalo 10.0.2.0/24. En ese caso, la operación de $export no se realizará correctamente. Puede volver a intentar la solicitud de $export, pero no hay ninguna garantía de que se usará la próxima vez una dirección IP dentro del intervalo 100.64.0.0/10. Ese es el comportamiento de red conocido por diseño. La alternativa es configurar la cuenta de almacenamiento en una región diferente.
    
## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a exportar recursos de FHIR mediante el comando $export. A continuación, para aprender a exportar datos sin identificación, consulte:
 
>[!div class="nextstepaction"]
>[Exportación de datos sin identificación](de-identified-export.md)