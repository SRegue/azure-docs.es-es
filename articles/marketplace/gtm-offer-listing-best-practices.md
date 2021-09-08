---
title: 'Procedimientos recomendados para la publicación de ofertas: marketplace comercial de Microsoft'
description: Obtenga información sobre los procedimientos recomendados sobre la publicación para la comercialización de sus ofertas de Microsoft AppSource y Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 06/03/2021
ms.openlocfilehash: 12c3641597168ee7e76acf4a16984f4419d40555
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112298842"
---
# <a name="offer-listing-best-practices"></a>Procedimientos recomendados para la publicación de ofertas

En este artículo se ofrecen sugerencias para crear ofertas de marketplace comercial de Microsoft y participar en ellas. En las tablas siguientes se describen los procedimientos recomendados para completar la información de la oferta en el Centro de partners. Para obtener un análisis del rendimiento de las ofertas, vaya al [Panel de información de Marketplace](https://go.microsoft.com/fwlink/?linkid=2165936) en el Centro de partners.

## <a name="online-store-offer-details"></a>Detalles de oferta de tienda en línea

| Configuración | Procedimiento recomendado |
|:--- |:--- |  
| Nombre de la oferta | Para las aplicaciones, proporcione un título claro que incluya palabras clave de búsqueda para ayudar a los clientes a detectar su oferta. <br> <br> En el caso de los servicios de consultoría, siga este formato: [nombre de la oferta: [duración] [tipo de oferta] (por ejemplo, Contoso: implementación en 2 semanas) |
| Descripción de la oferta | Proporcione una descripción clara de la propuesta de valor de la oferta en las primeras oraciones.  Tenga en cuenta que estas oraciones se pueden usar en los resultados del motor de búsqueda. Los componentes principales de la propuesta de valor deben incluir lo siguiente: <ul> <li>Descripción del producto o de la solución. </li> <li> Rol de usuario que se beneficia del producto o de la solución. </li> <li> Necesidad o preocupación del cliente que aborda el producto o la solución. </li> </ul> <br> Cuando sea posible, use un vocabulario estándar del sector o palabras que se basen en ventajas.  No se base exclusivamente en las características y funcionalidades para vender su producto.  En su lugar, céntrese en el valor que proporciona. <br> <br> En el caso de los anuncios de servicios de consultoría, debe indicar claramente los servicios profesionales que proporciona. |

> [!IMPORTANT]
> Asegúrese de que el nombre y la descripción de la oferta se ajustan a las **[Directrices de marca y marca comercial de Microsoft](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks/usage/general.aspx)** , así como a cualquier otra directriz específica de cada producto, cuando se haga referencia a las marcas comerciales de Microsoft y a nombres de software, productos y servicios de Microsoft.

## <a name="online-store-listing-details"></a>Detalles de la publicación de tiendas en línea

En esta tabla se muestra qué tipos de ofertas tienen categorías y sectores aplicables a las diferentes tiendas en línea: Azure Marketplace y Microsoft AppSource.

| Tipo de oferta | Categorías de Azure Marketplace | Categorías de AppSource | Sectores para AppSource |
| :------------------- |:----------------:|:------:|:-------------:|
| Aplicación de Azure     | X |   |   |
| Contenedor de Azure       | X |   |   |
| Máquina virtual de Azure | X |   |   |
| Servicio de consultoría    | X<sup>*</sup> |   | X<sup>*</sup> |
| Dynamics 365 Customer Engagement & Power Apps | | X | X |
| Dynamics 365 for Operations | | X | X |
| Dynamics 365 Business Central | | X | X |
| Módulo IoT Edge | X | |  |
| Servicio administrado | X | |  |
| Power BI app | | X | X |
| SaaS | X | X | X |

* La oferta se publica en la tienda en línea correspondiente en función del producto principal. Si el producto principal es Azure, pasa a Azure Marketplace. De lo contrario, se publica en AppSource.

### <a name="categories"></a>Categorías

Microsoft AppSource y Azure Marketplace son tiendas en línea que ofrecen diferentes tipos de soluciones. Azure Marketplace ofrece soluciones de TI basadas en Azure o para Azure.  Microsoft AppSource ofrece soluciones empresariales, como aplicaciones SaaS del sector, complementos de Dynamics 365, complementos de Microsoft 365 y aplicaciones de Power Platform.

Las categorías y subcategorías se asignan a cada tienda en línea en función del tipo de solución. La oferta se publicará en Microsoft AppSource o Azure Marketplace en función del tipo de oferta, las capacidades de transacción de la oferta y la selección de categoría/subcategoría. 

Seleccione las categorías y subcategorías que mejor se adapten al tipo de solución. Puede seleccionar:

* Seleccione dos categorías como máximo, incluidas una categoría principal y una secundaria (opcional).
* Hasta dos subcategorías para cada categoría principal o secundaria. Si no se selecciona ninguna subcategoría, solo se podrá detectar en la categoría seleccionada.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

#### <a name="important-saas-offers-and-microsoft-365-add-ins"></a>IMPORTANTE: ofertas de SaaS y complementos de Microsoft 365

Para los detalles específicos sobre cómo las capacidades de transacción pueden afectar a la forma en que los clientes de marketplace pueden ver y comprar su oferta, vea [Transacciones en marketplace comercial](marketplace-commercial-transaction-capabilities-and-considerations.md). En el caso de las ofertas de SaaS, la funcionalidad de transacción de la oferta, así como la selección de categoría, determinará la tienda en línea donde se publicará la oferta.

En esta tabla se muestran las combinaciones de opciones aplicables a las diferentes tiendas en línea: Azure Marketplace y Microsoft AppSource.

| Facturación de uso medido | Complementos de Microsoft 365 | Plan solo privado | Plan solo público | Planes públicos y privados | Tienda en línea aplicable |
|:-------------:|:---:|:--------:|:---------:|:---------------------:|:-------------:|
|  | X |  |  |  | AppSource |
| X |  | X |  |  | Azure Marketplace |
| X |  |  | X |  | Azure Marketplace |
| X |  |  |  | X | Azure Marketplace<sup>2</sup> |
|  |  | X |  |  | Azure Marketplace |
|  |  |  | X |  | AppSource<sup>1</sup><br>Azure Marketplace<sup>1</sup> |
|  |  |  |  | X | AppSource<sup>1</sup><br>Azure Marketplace<sup>1,2</sup> |
|  |  |  |  | X | AppSource<sup>1</sup><br>Azure Marketplace<sup>1</sup> |

1. En función de la selección de categoría/subcategoría y del sector
2. Las ofertas con planes privados se publicarán en Azure Portal

> [!NOTE]
> No puede tener planes de publicación y planes transaccionales en la misma oferta.

### <a name="industries"></a>Industrias

La selección de sector solo se aplica a las ofertas publicadas en AppSource y a los servicios de consultoría publicados en Azure Marketplace.  Seleccione sectores o verticales si la oferta aborda las necesidades específicas del sector, con lo que se destacan las funcionalidades específicas del sector en la descripción de la oferta. Puede seleccionar hasta dos (2) sectores, así como dos (2) verticales por cada sector seleccionado.

>[!Note]
>En el caso de las ofertas de servicio de consultoría de Azure Marketplace, no hay ninguna vertical del sector.

| **Industrias** |  **Segmentos verticales** |
| :------------------- | :----------------|
| **Agricultura** | |
| **Arquitectura y construcción** | |
| **Automoción** | |
| **Distribución** | Venta al por mayor <br> Envío de paquetes |  
| **Education** | Educación superior <br> Educación primaria y secundaria / K-12 <br> Bibliotecas y museos |
| **Servicios financieros** | Banca y mercados de capital <br> Seguros | 
| **Gobierno** |  Defensa e inteligencia <br> Gobierno civil <br> Justicia y seguridad pública |
| **Atención sanitaria** | Responsable de los pagos de mantenimiento <br> Proveedor de asistencia sanitaria <br> Productos farmacéuticos | 
| **Hostelería y viajes** | Viajes y transporte <br> Hoteles y ocio <br> Servicio de restaurantes y hostelería | 
| **Fabricación y recursos** | Química y agroquímica <br> Fabricación discreta <br> Sector energético | 
| **Elementos multimedia y comunicaciones** | Elementos multimedia y entretenimiento <br> Telecomunicaciones | 
| **Otras industrias del sector público** | Silvicultura y pesca <br> Sin ánimo de lucro | 
| **Servicios profesionales** | Servicios profesionales de partner <br> Información legal | 
| **Bienes inmuebles** | |

Sector solo para Microsoft AppSource:

| **Sector** |  **Segmentos verticales** |
| :------------------- | :----------------|
| **Minoristas y bienes de consumo** | Minoristas <br> Bienes de consumo |

### <a name="applicable-products"></a>Productos aplicables

Seleccione los productos aplicables con los que funciona la aplicación para que la oferta se muestre con los productos seleccionados en AppSource.

### <a name="search-keywords"></a>Palabras clave de búsqueda

Pueden ayudar a los clientes a encontrar su oferta cuando realicen búsquedas Identifique las principales palabras clave de búsqueda de su oferta, agréguelas en el resumen de la oferta y la descripción, así como en la sección de palabras claves de los detalles de la oferta.

## <a name="online-store-marketing-details"></a>Detalles del marketing de tiendas en línea
| Configuración | Procedimiento recomendado |
|:--- |:--- |  
| Logotipo de la oferta (formato PNG, de 216 × 216 a 350 x 350 píxeles): página de detalles de la aplicación | Diseñe y optimice su logotipo para un medio digital:<br>Cargue el logotipo en formato PNG para la página de publicación de detalles de la aplicación de la oferta. El Centro de partners cambiará su tamaño a los tamaños de logotipo necesarios. |
| Logotipo de la oferta (formato .png, 48 x 48 píxeles): página de búsqueda | El Centro de partners generará este logotipo a partir del logotipo grande que ha cargado. Opcionalmente, puede reemplazarlo por una imagen diferente. |
| Documentos de "Más información" | Incluya recursos auxiliares de ventas y marketing en "Más información", como:<ul><li>notas del producto</li><li> folletos</li><li>listas de comprobación,</li><li> presentaciones de PowerPoint</li></ul><br>Guarde todos los archivos en formato PDF. El objetivo debe ser informar a los clientes acerca del producto, no venderlo.<br><br>Agregue un vínculo a la página principal de la aplicación en todos los documentos e incluya parámetros de URL para ayudarlo a realizar un seguimiento de los visitantes y las evaluaciones. |
| Videos: solo AppSource, servicios de consultoría y ofertas de SaaS | Los vídeos más atractivos comunican el valor de la oferta en forma de narración:<ul> <li> Asegúrese de que el héroe de la historia es el cliente, no su negocio. </li> <li> El vídeo debe abordar los objetivos y retos más importantes del cliente de destino. </li> <li> Longitud recomendada: de 60 a 90 segundos.</li> <li> Incorpore palabras clave de búsqueda que usan el nombre de los vídeos. </li> <li> Considere la posibilidad de agregar otros vídeos como, por ejemplo, de instrucciones, de introducción o de testimonios de clientes. </li> </ul> |
| Capturas de pantalla (1280&nbsp;&times;&nbsp;720) | Agregue hasta cinco capturas de pantalla:<br>Incorpore palabras clave de búsqueda en los nombres de archivos. |

## <a name="link-to-your-offer-page-from-your-website"></a>Vinculación a la página de la oferta desde su sitio web

Al vincular el distintivo de AppSource o de Azure Marketplace del sitio web a la publicación en el marketplace comercial, puede incluir los siguientes parámetros de consulta al final de la dirección URL para obtener análisis e informes detallados:
* **src**: incluya el origen desde donde se enruta el tráfico a AppSource; por ejemplo, el sitio web, LinkedIn o Facebook.
* **mktcmpid**: el identificador de la campaña de marketing, que puede contener hasta 16 caracteres en cualquier combinación de letras, números, guiones bajos y guiones (por ejemplo, *blogpost_12*).

La siguiente dirección URL de ejemplo contiene los dos parámetros de consulta anteriores: `https://appsource.microsoft.com/product/dynamics-365/mscrm.04931187-431c-415d-8777-f7f482ba8095?src=website&mktcmpid=blogpost_12`

Al agregar estos parámetros a la dirección URL de AppSource, puede revisar la eficacia de la campaña en el [panel de análisis](https://go.microsoft.com/fwlink/?linkid=2165765) del Centro de partners.

## <a name="next-steps"></a>Pasos siguientes

Más información acerca de las [ventajas de Marketplace comercial](./gtm-your-marketplace-benefits.md).

Inicie sesión en el [Centro de partners](https://go.microsoft.com/fwlink/?linkid=2165290) para crear y configurar la oferta. Si todavía no se ha inscrito en el Centro de partners, [cree una cuenta](create-account.md).
