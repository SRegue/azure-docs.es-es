---
title: Configuración de una conexión a una cuenta de almacenamiento mediante una identidad administrada
titleSuffix: Azure Cognitive Search
description: Aprenda a configurar una conexión de indexador a una cuenta de Azure Storage mediante una identidad administrada
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 91ca017bf94f2c9a75a8016fb861cc085dc47ebe
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2021
ms.locfileid: "111556971"
---
# <a name="set-up-a-connection-to-an-azure-storage-account-using-a-managed-identity"></a>Configuración de una conexión a una cuenta de Azure Storage mediante una identidad administrada

En esta página se describe cómo configurar una conexión de indexador a una cuenta de Azure Storage mediante una identidad administrada en lugar de proporcionar credenciales en la cadena de conexión del objeto de origen de datos.

Antes de obtener más información acerca de esta característica, se recomienda comprender qué es un indexador y cómo configurar un indexador para el origen de datos. Se puede encontrar más información en los vínculos siguientes:
* [Información general del indexador](search-indexer-overview.md)
* [Indexador de blobs de Azure](search-howto-indexing-azure-blob-storage.md)
* [Indexador de Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
* [Indexador de tablas de Azure](search-howto-indexing-azure-tables.md)

## <a name="set-up-the-connection"></a>Configuración de la conexión

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - Activar la identidad administrada asignada por el sistema

Cuando se habilita una identidad administrada asignada por el sistema, Azure crea una identidad para el servicio de búsqueda que se puede usar para autenticarse en otros servicios de Azure en el mismo inquilino y la misma suscripción. Después, puede usar esta identidad en las asignaciones de control de acceso basado en rol de Azure (RBAC de Azure) que permiten el acceso a los datos durante la indexación.

![Activar la identidad administrada asignada por el sistema](./media/search-managed-identities/turn-on-system-assigned-identity.png "Activar la identidad administrada asignada por el sistema")

Después de seleccionar **Guardar**, verá un identificador de objeto que se ha asignado al servicio de búsqueda.

![Id. de objeto](./media/search-managed-identities/system-assigned-identity-object-id.png "Id. de objeto")
 
### <a name="2---add-a-role-assignment"></a>2 - Agregar una asignación de roles

En este paso, concederá permiso a su servicio de Azure Cognitive Search para leer los datos de la cuenta de almacenamiento.

1. En Azure Portal, navegue hasta la cuenta de almacenamiento que contiene los datos que quiere indexar.
2. Seleccione **Access Control (IAM)**
3. Seleccione **Agregar** y, a continuación, **Agregar asignación de roles**.

    ![Agregar asignación de roles](./media/search-managed-identities/add-role-assignment-storage.png "Agregar asignación de roles")

4. Seleccione los roles apropiados según el tipo de cuenta de almacenamiento que quiera indexar:
    1. Azure Blob Storage requiere que se agregue el rol **Lector de datos de Storage Blob** al servicio de búsqueda.
    1. Azure Data Lake Storage Gen2 requiere que se agregue el rol **Lector de datos de Storage Blob** al servicio de búsqueda.
    1. Azure Table Storage requiere que se agregue el rol **Lector y acceso a los datos** al servicio de búsqueda.
5.  Deje **Asignar acceso a** como **Usuario, grupo o entidad de servicio de Azure AD**.
6.  Busque el servicio de búsqueda, selecciónelo y elija **Guardar**.

    Ejemplo de Azure Blob Storage y Azure Data Lake Storage Gen2:

    ![Agregar la asignación del rol Lector de datos de Storage Blob](./media/search-managed-identities/add-role-assignment-storage-blob-data-reader.png "Agregar la asignación del rol Lector de datos de Storage Blob")

    Ejemplo de Azure Table Storage:

    ![Agregue la asignación del rol Lector y acceso a datos](./media/search-managed-identities/add-role-assignment-reader-and-data-access.png "Agregar la asignación del rol Lector y acceso a datos").

### <a name="3---create-the-data-source"></a>3 - Crear el origen de datos

La [API REST](/rest/api/searchservice/create-data-source), Azure Portal y el [SDK de .NET](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) admiten la cadena de conexión de identidad administrada. A continuación, se muestra un ejemplo de cómo crear un origen de datos para indexar datos desde una cuenta de almacenamiento mediante la [API REST](/rest/api/searchservice/create-data-source) y una cadena de conexión de identidad administrada. El formato de la cadena de conexión de identidad administrada es el mismo en la API REST, el SDK de .NET y Azure Portal.

Al realizar la indexación desde una cuenta de almacenamiento, el origen de datos debe tener las siguientes propiedades obligatorias:

* **name** es el nombre único del origen de datos dentro del servicio de búsqueda.
* **type**
    * Azure Blob Storage: `azureblob`
    * Almacenamiento de tablas de Azure: `azuretable`
    * Azure Data Lake Storage Gen2: el valor de **tipo** se proporcionará una vez que se suscriba a la versión preliminar con [este formulario](https://aka.ms/azure-cognitive-search/mi-preview-request).
* **credentials**
    * Cuando se usa una identidad administrada para la autenticación, el formato de **credenciales** es diferente a cuando no se usa. Aquí proporcionará un ResourceId que no tiene ninguna contraseña ni clave de cuenta. El ResourceId debe incluir el identificador de suscripción de la cuenta de almacenamiento, el grupo de recursos de la cuenta de almacenamiento y el nombre de la cuenta de almacenamiento.
    * Formato de identidad administrada: 
        * *ResourceId=/subscriptions/**id. de la suscripción**/resourceGroups/**nombre del grupo de recursos**/providers/Microsoft.Storage/storageAccounts/**nombre de la cuenta de almacenamiento**/;*
* **container** especifica un contenedor o un nombre de tabla en la cuenta de almacenamiento. De manera predeterminada, se pueden recuperar todos los blobs dentro del contenedor. Si solo desea indexar blobs en un directorio virtual determinado, puede especificar ese directorio. Para ello, use el parámetro opcional **query**.

Ejemplo de cómo crear un objeto de origen de datos de blob mediante la [API de REST](/rest/api/searchservice/create-data-source):

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name/;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}   
```

### <a name="4---create-the-index"></a>4 - Crear el índice

El índice especifica los campos de un documento, los atributos y otras construcciones que conforman la experiencia de búsqueda.

Aquí se muestra cómo crear un índice con un campo `content` utilizable en búsquedas para almacenar el texto extraído de los blobs:   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

Para obtener más información sobre la creación de índices, vea el artículo de [creación de índices](/rest/api/searchservice/create-index).

### <a name="5---create-the-indexer"></a>5 - Crear el indexador

Un indizador conecta un origen de datos con un índice de búsqueda de destino y proporciona una programación para automatizar la actualización de datos.

Una vez creados el índice y el origen de datos, ya podrá crear el indexador.

Ejemplo de definición de indexador para un indexador de blobs:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Este indizador se ejecutará cada dos horas (el intervalo de programación se establece en PT2H). Para ejecutar un indizador cada 30 minutos, establézcalo en PT30M. El intervalo más breve que se admite es de 5 minutos. La programación es opcional: si se omite, el indizador solo se ejecuta una vez cuando se crea. Sin embargo, puede ejecutarlo a petición en cualquier momento.   

Para más información sobre la API Create Indexer, consulte [Crear indexador](/rest/api/searchservice/create-indexer).

Para más información sobre cómo definir las programaciones del indexador, consulte [Programación de indexadores para Azure Cognitive Search](search-howto-schedule-indexers.md).

## <a name="accessing-secure-data-in-storage-accounts"></a>Acceso a datos seguros en cuentas de almacenamiento

Las cuentas de almacenamiento de Azure se pueden proteger aún más mediante firewalls y redes virtuales. Si quiere indexar contenido de una cuenta de almacenamiento de blobs o de una cuenta de almacenamiento de Data Lake Gen2 que está protegida mediante un firewall o una red virtual, siga las instrucciones de [Acceso a los datos de las cuentas de almacenamiento de forma segura mediante una excepción de servicio de confianza](search-indexer-howto-access-trusted-service-exception.md).

## <a name="see-also"></a>Consulte también

Más información sobre los indexadores de Azure Storage:

* [Indexador de blobs de Azure](search-howto-indexing-azure-blob-storage.md)
* [Indexador de Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
* [Indexador de tablas de Azure](search-howto-indexing-azure-tables.md)