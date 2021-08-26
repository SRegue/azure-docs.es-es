---
title: Copia de datos de Presto mediante Azure Data Factory
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre cómo copiar datos de Presto en almacenes de datos receptores compatibles a través de una actividad de copia de una canalización de Azure Data Factory.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: jianleishen
ms.openlocfilehash: 4fe458ce4f358e81c115761b571874a955ededc4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638736"
---
# <a name="copy-data-from-presto-using-azure-data-factory"></a>Copia de datos de Presto mediante Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se explica el uso de la actividad de copia de Azure Data Factory para copiar datos de Presto. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Este conector Presto es compatible con las actividades siguientes:

- [Actividad de copia](copy-activity-overview.md) con [matriz de origen o receptor compatible](copy-activity-overview.md)
- [Actividad de búsqueda](control-flow-lookup-activity.md)

Puede copiar datos de Presto en cualquier almacén de datos de receptor compatible. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

Azure Data Factory proporciona un controlador integrado para habilitar la conectividad. Por lo tanto, no es necesario instalar manualmente ningún controlador mediante este conector.

## <a name="getting-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

En las secciones siguientes se proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector de Presto.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de Presto:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type debe establecerse en: **Presto** | Sí |
| host | Dirección IP o nombre de host del servidor de Presto. (por ejemplo, 192.168.222.160)  | Sí |
| serverVersion | Versión del servidor de Presto. (por ejemplo, 0.148-t)  | Sí |
| catalog | Contexto de catálogo para todas las solicitudes en el servidor.  | Sí |
| port | Puerto TCP que el servidor de Presto utiliza para escuchar las conexiones del cliente. El valor predeterminado es 8080.  | No |
| authenticationType | Mecanismo de autenticación utilizado para conectarse al servidor de Presto. <br/>Los valores permitidos son: **Anonymous** y **LDAP** | Sí |
| username | Nombre de usuario que se usa para conectarse al servidor de Presto.  | No |
| password | Contraseña que corresponde al nombre de usuario. Marque este campo como SecureString para almacenarlo de forma segura en Data Factory o [para hacer referencia a un secreto almacenado en Azure Key Vault](store-credentials-in-key-vault.md). | No |
| enableSsl | Especifica si las conexiones al servidor se cifran mediante TLS. El valor predeterminado es false.  | No |
| trustedCertPath | Ruta de acceso completa del archivo .pem que contiene certificados de CA de confianza para comprobar el servidor al conectarse a través de TLS. Esta propiedad solo puede establecerse al utilizar TLS en IR autohospedados. El valor predeterminado es el archivo cacerts.pem instalado con el IR.  | No |
| useSystemTrustStore | Especifica si se utiliza un certificado de CA del almacén de confianza del sistema o de un archivo PEM especificado. El valor predeterminado es false.  | No |
| allowHostNameCNMismatch | Especifica si se requiere que el nombre del certificado TLS/SSL emitido por una CA coincida con el nombre de host del servidor al conectarse a través de TLS. El valor predeterminado es false.  | No |
| allowSelfSignedServerCert | Especifica si se permiten los certificados autofirmados del servidor. El valor predeterminado es false.  | No |
| timeZoneID | Zona horaria local que usa la conexión. Los valores válidos para esta opción se especifican en la base de datos de la zona horaria IANA. El valor predeterminado es la zona horaria del sistema.  | No |

**Ejemplo**:

```json
{
    "name": "PrestoLinkedService",
    "properties": {
        "type": "Presto",
        "typeProperties": {
            "host" : "<host>",
            "serverVersion" : "0.148-t",
            "catalog" : "<catalog>",
            "port" : "<port>",
            "authenticationType" : "LDAP",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "timeZoneID" : "Europe/Berlin"
        }
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre [conjuntos de datos](concepts-datasets-linked-services.md). En esta sección se proporciona una lista de las propiedades compatibles con el conjunto de datos de Presto.

Para copiar datos de Presto, establezca la propiedad type del conjunto de datos en **PrestoObject**. Se admiten las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del conjunto de datos debe establecerse en: **PrestoObject** | Sí |
| esquema | Nombre del esquema. |No (si se especifica "query" en el origen de la actividad)  |
| table | Nombre de la tabla. |No (si se especifica "query" en el origen de la actividad)  |
| tableName | Nombre de la tabla con el esquema. Esta propiedad permite la compatibilidad con versiones anteriores. Use `schema` y `table` para la carga de trabajo nueva. | No (si se especifica "query" en el origen de la actividad) |

**Ejemplo**

```json
{
    "name": "PrestoDataset",
    "properties": {
        "type": "PrestoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Presto linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades compatibles con el origen de Presto.

### <a name="presto-as-source"></a>Presto como origen

Para copiar datos de Presto, establezca el tipo de origen de la actividad de copia en **PrestoSource**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del origen de la actividad de copia debe establecerse en: **PrestoSource** | Sí |
| Query | Use la consulta SQL personalizada para leer los datos. Por ejemplo: `"SELECT * FROM MyTable"`. | No (si se especifica "tableName" en el conjunto de datos) |

**Ejemplo**:

```json
"activities":[
    {
        "name": "CopyFromPresto",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Presto input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "PrestoSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Propiedades de la actividad de búsqueda

Para obtener información detallada sobre las propiedades, consulte [Actividad de búsqueda](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.
