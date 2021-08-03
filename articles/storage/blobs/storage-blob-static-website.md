---
title: Hospedaje de sitios web estáticos en Azure Storage
description: Azure Storage le ofrece hospedaje de sitios web estáticos, lo que le proporciona una solución rentable y escalable para hospedar aplicaciones web modernas.
author: normesta
ms.service: storage
ms.topic: how-to
ms.author: normesta
ms.reviewer: dineshm
ms.date: 09/04/2020
ms.subservice: blobs
ms.custom: devx-track-js
ms.openlocfilehash: 10caff631d59085aa2bbfcd573c3e3114b0306f7
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2021
ms.locfileid: "111560877"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hospedaje de sitios web estáticos en Azure Storage

Puede proporcionar contenido estático (HTML, CSS, JavaScript y archivos de imagen) directamente desde un contenedor de almacenamiento llamado *$web*. El hospedaje de contenido en Azure Storage permite usar arquitecturas sin servidor que incluyen [Azure Functions](../../azure-functions/functions-overview.md) y otros servicios de plataforma como servicio (PaaS). El hospedaje de sitios web estáticos de Azure Storage es una opción excelente en los casos en los que no es necesario que un servidor web represente contenido.

Las [aplicaciones web estáticas de App Service](https://azure.microsoft.com/services/app-service/static/) es una excelente alternativa al hospedaje de sitios web estáticos de Azure Storage y también es adecuado en los casos en los que no es necesario que un servidor web represente contenido. Las aplicaciones web estáticas de App Service proporcionan un flujo de trabajo de integración continua y entrega continua (CI/CD) totalmente administrado desde el origen de GitHub a la implementación global.

Si necesita un servidor web para representar contenido, puede usar [Azure App Service](https://azure.microsoft.com/services/app-service/).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up-a-static-website"></a>Configuración de un sitio web estático

El hospedaje de sitios web estáticos es una característica que permite habilitar la cuenta de almacenamiento.

Para habilitar el hospedaje de sitios web estáticos, seleccione el nombre del archivo predeterminado y, opcionalmente, proporcione una ruta de acceso a una página 404 personalizada. Si aún no existe un contenedor de almacenamiento de blobs denominado **$web** en la cuenta, se creará uno. Agregue los archivos del sitio a ese contenedor.

Para obtener instrucciones detalladas, consulte [Hospedaje de sitios web estáticos en Azure Storage](storage-blob-static-website-how-to.md).

![Métrica de métricas de sitios web estáticos de Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Los archivos del contenedor **$web** distinguen mayúsculas de minúsculas, se proporcionan mediante solicitudes de acceso anónimo y solo están disponibles a través operaciones de lectura.

## <a name="uploading-content"></a>Carga de contenido

Puede usar cualquiera de estas herramientas para cargar contenido en el contenedor **$web**:

> [!div class="checklist"]
> * [CLI de Azure](storage-blob-static-website-how-to.md?tabs=azure-cli)
> * [Módulo de Azure PowerShell](storage-blob-static-website-how-to.md?tabs=azure-powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Extensión de Visual Studio Code](/azure/developer/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Visualización de contenido

Los usuarios pueden ver contenido del sitio desde un explorador usando la dirección URL pública del sitio web. Puede buscar la dirección URL usando Azure Portal, CLI de Azure o PowerShell. Consulte cómo [Búsqueda de la dirección URL del sitio web](storage-blob-static-website-how-to.md#portal-find-url).

Si el servidor devuelve un error 404 y no se ha especificado un documento de error al habilitar el sitio web, se devuelve una página 404 predeterminada al usuario.

> [!NOTE]
> El [uso compartido de recursos entre orígenes (CORS) para Azure Storage](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) no se admite para los sitios web estáticos.

### <a name="regional-codes"></a>Códigos regionales

La dirección URL del sitio contiene un código regional. Por ejemplo, la dirección URL `https://contosoblobaccount.z22.web.core.windows.net/` contiene el código regional `z22`.

Aunque el código debe permanecer en la dirección URL, solo es para uso interno y no se tiene que usar de ninguna otra manera.

El documento del índice que especifique al habilitar el hospedaje de sitios web estáticos aparece cuando los usuarios abren el sitio y no especifican un archivo concreto (por ejemplo, `https://contosoblobaccount.z22.web.core.windows.net`).

### <a name="secondary-endpoints"></a>Puntos de conexión secundarios

Si configura la [redundancia en una región secundaria](../common/storage-redundancy.md#redundancy-in-a-secondary-region), también puede tener acceso al contenido del sitio web mediante un punto de conexión secundario. Dado que los datos se replican en las regiones secundarias de forma asincrónica, los archivos que están disponibles en el punto de conexión secundario no están siempre sincronizados con los archivos que están disponibles en el punto de conexión principal.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Impacto de la configuración del nivel de acceso público del contenedor web

Puede modificar el nivel de acceso público del contenedor **$web**, lo cual no afecta al punto de conexión principal del sitio web estático porque estos archivos se proporcionan a través de solicitudes de acceso anónimo. Eso significa acceso público (de solo lectura) a todos los archivos.

En la siguiente captura de pantalla se muestra la configuración del nivel de acceso público en Azure Portal:

![Captura de pantalla que muestra cómo establecer el nivel de acceso público en el portal](./media/anonymous-read-access-configure/configure-public-access-container.png)

Aunque el punto de conexión principal del sitio web estático no se ve afectado, un cambio en el nivel de acceso público afecta al punto de conexión principal de Blob service.

Por ejemplo, si cambia el nivel de acceso público del contenedor **$web** de **Privado (sin acceso anónimo)** a **Blob (acceso anónimo de lectura solo para blobs)** , el nivel de acceso público al punto de conexión principal del sitio web estático `https://contosoblobaccount.z22.web.core.windows.net/index.html` no cambia.

Sin embargo, el acceso público al punto de conexión principal de Blob service `https://contosoblobaccount.blob.core.windows.net/$web/index.html` cambia de privado a público. Ahora los usuarios pueden abrir ese archivo mediante cualquiera de estos dos punto de conexión.

Deshabilitar el acceso público en una cuenta de almacenamiento no afecta a los sitios web estáticos que se hospedan en ella. Para más información, consulte [Configuración del acceso de lectura público anónimo a contenedores y blobs](anonymous-read-access-configure.md).

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Asignación de un dominio personalizado a una dirección URL de un sitio web estático

Puede hacer que el sitio web estático esté disponible a través de un dominio personalizado.

Es más fácil habilitar el acceso HTTP para un dominio personalizado, ya que Azure Storage lo admite de forma nativa. Para habilitar HTTPS, tendrá que usar Azure CDN porque Azure Storage no admite de forma nativa HTTPS con dominios personalizados. Para obtener instrucciones pormenorizadas, consulte [Asignación de un dominio personalizado a un punto de conexión de Azure Blob Storage](storage-custom-domain-name.md).

Si la cuenta de almacenamiento está configurada para [requerir la transferencia segura](../common/storage-require-secure-transfer.md) a través de HTTPS, los usuarios deben utilizar el punto de conexión HTTPS.

> [!TIP]
> Consider la posibilidad de hospedar el dominio en Azure. Para más información, consulte [Hospedaje de un dominio en Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="adding-http-headers"></a>Adición de encabezados HTTP

No hay forma de configurar encabezados como parte de la característica de sitio web estático. Sin embargo, puede usar Azure CDN para agregar encabezados y anexar (o sobrescribir) sus valores. Consulte [Referencia del motor de reglas estándar de Azure CDN](../../cdn/cdn-standard-rules-engine-reference.md).

Si quiere usar encabezados para controlar el almacenamiento en caché, consulte [Control del comportamiento del almacenamiento en caché de Azure CDN con reglas de almacenamiento en caché](../../cdn/cdn-caching-rules.md).

## <a name="multi-region-website-hosting"></a>Hospedaje de sitios web de varias regiones

Si planea hospedar un sitio web en varias zonas geográficas, se recomienda usar [Content Delivery Network](../../cdn/index.yml) para el almacenamiento en caché regional. Use [Azure Front Door](../../frontdoor/index.yml) para proporcionar contenido diferente en cada región. También proporciona funcionalidad de conmutación por error. [No se recomienda](../../traffic-manager/index.yml) Azure Traffic Manager si tiene previsto usar un dominio personalizado. Pueden surgir problemas debido a cómo Azure Storage comprueba los nombres de dominio personalizados.

## <a name="permissions"></a>Permisos

El permiso para habilitar el sitio web estático es Microsoft.Storage/storageAccounts/blobServices/write o clave compartida.  Los roles integrados que proporcionan este acceso incluyen Colaborador de la cuenta de almacenamiento.  

## <a name="pricing"></a>Precios

Puede habilitar el hospedaje de sitios web estáticos de forma gratuita. Se le factura solo por el almacenamiento de blobs que el sitio usa y por los costes de las operaciones. Para obtener más información acerca de los precios de Azure Blob Storage, consulte la [página de precios de Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Métricas

Puede habilitar métricas en páginas de sitios web estáticos. Después de habilitar las métricas, las estadísticas de tráfico de los archivos en el contenedor **$web** aparecen en el panel de métricas.

Para habilitar métricas en las páginas de su sitio web estático, consulte [Enable metrics on static website pages](storage-blob-static-website-how-to.md#metrics) (Habilitación de métricas en páginas de sitios web estáticos).

## <a name="next-steps"></a>Pasos siguientes

* [Hospedaje de sitios web estáticos en Azure Storage](storage-blob-static-website-how-to.md)
* [Asignación de un dominio personalizado a un punto de conexión de Azure Blob Storage](storage-custom-domain-name.md)
* [Funciones de Azure](../../azure-functions/functions-overview.md)
* [Azure App Service](../../app-service/overview.md)
* [Crear la primera aplicación web sin servidor](/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Tutorial: Hospedaje del dominio en Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
