---
title: Cifrado de datos en reposo del servicio de voz
titleSuffix: Azure Cognitive Services
description: Microsoft ofrece claves de cifrado administradas por Microsoft y también le permite administrar las suscripciones de Cognitive Services con sus propias claves, llamadas claves administradas por el cliente (CMK). En este artículo, encontrará información sobre el cifrado de datos en reposo del servicio Voz.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: egeaney
ms.openlocfilehash: cdf8276904fda5098b3192779e0372b4a1bcc9d2
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113766621"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>Cifrado de datos en reposo del servicio de voz

El servicio de voz cifra de forma automática los datos al guardarlos en la nube. El cifrado del servicio de voz protege los datos y le ayuda a satisfacer los requisitos de cumplimiento normativo y de seguridad de la organización.

## <a name="about-cognitive-services-encryption"></a>Información sobre el cifrado de Cognitive Services

Los datos se cifran y descifran con el cifrado [AES de 256 bits](https://en.wikipedia.org/wiki/FIPS_140-2) compatible con [FIPS 140-2](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard). El cifrado y el descifrado son transparentes, lo que significa que el cifrado y el acceso se administran automáticamente. Dado que los datos están protegidos de forma predeterminada, no es necesario modificar el código ni las aplicaciones para utilizar el cifrado.

## <a name="about-encryption-key-management"></a>Información sobre la administración de claves de cifrado

Cuando se usan Custom Speech y Voz personalizada, el servicio de voz puede almacenar los siguientes datos en la nube:  

* Datos de seguimiento de voz (solo si activa el seguimiento para su punto de conexión personalizado)
* Carga de datos de entrenamiento y prueba

De forma predeterminada, los datos se guardan en el almacenamiento de Microsoft y la suscripción usa claves de cifrado administradas por Microsoft. También tiene la opción de preparar su propia cuenta de almacenamiento. El acceso a la tienda lo administra la identidad administrada y el servicio de voz no puede acceder directamente a sus propios datos, como los datos de seguimiento de voz, datos de entrenamiento de personalización y modelos personalizados.

Para más información sobre la identidad administrada, consulte [¿Qué son las identidades administradas?](../../active-directory/managed-identities-azure-resources/overview.md)

Mientras tanto, cuando se usa Comando personalizado, puede administrar la suscripción con sus propias claves de cifrado. Las claves administradas por el cliente (CMK), también conocidas como Bring Your Own Key (BYOK), ofrecen más flexibilidad para crear, girar, deshabilitar y revocar controles de acceso. También permite auditar las claves de cifrado que se usan para proteger los datos. Para obtener más información sobre Comando personalizado y CMK, consulte [Cifrado de Comandos personalizados de datos en reposo](custom-commands-encryption-of-data-at-rest.md).

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>Traiga su propio almacenamiento (BYOS) para la personalización y el registro

Para solicitar acceso para traer su propio almacenamiento, rellene y envíe el  [formulario de solicitud del servicio de voz: traiga su propia cuenta de almacenamiento (BYOS)](https://aka.ms/cogsvc-cmk). Una vez que reciba la aprobación, será preciso que cree su propia cuenta de almacenamiento para almacenar los datos necesarios para la personalización y el registro. Al agregar una cuenta de almacenamiento, el recurso del servicio de voz habilitará una identidad administrada asignada por el sistema.

> [!IMPORTANT]
> La cuenta de usuario que se use para crear un recurso de voz con la funcionalidad BYOS habilitada debe tener asignado el [rol Propietario en el ámbito de la suscripción de Azure](../../cost-management-billing/manage/add-change-subscription-administrator.md#to-assign-a-user-as-an-administrator). De lo contrario, recibirá un error de autorización durante el aprovisionamiento de recursos.

Después de que se habilite la identidad administrada asignada por el sistema, este recurso se registrará con Azure Active Directory (AAD). Tras su registro, se concederá acceso a la identidad administrada a la cuenta de almacenamiento. Para más información sobre las identidades administradas, consulte [¿Qué son las identidades administradas?](../../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> Si deshabilita identidades administradas asignadas por el sistema, se quitará el acceso a la cuenta de almacenamiento. Esto hará que las partes del servicio de voz que requieran acceso a la cuenta de almacenamiento dejen de funcionar.  

El servicio de voz no admite actualmente la Caja de seguridad del cliente. Sin embargo, los datos del cliente pueden almacenarse mediante BYOS, lo que le permite lograr controles de datos similares para la [Caja de seguridad del cliente](../../security/fundamentals/customer-lockbox-overview.md). Tenga en cuenta que los datos del servicio de voz permanecen y se procesan en la región en la que se creó el recurso de voz. Esto se aplica a los datos en reposo y a los datos en tránsito. Cuando se usan características de personalización, como el Habla personalizada y la Voz personalizada, todos los datos del cliente se transfieren, almacenan y procesan en la misma región donde residen el recurso de BYOS (si se usa) y el servicio de voz.

> [!IMPORTANT]
> Microsoft **no** usa los datos del cliente para mejorar sus modelos de voz. Además, si el registro de puntos de conexión está deshabilitado y no se usa ninguna personalización, no se almacenan los datos de clientes. 

## <a name="next-steps"></a>Pasos siguientes

* [Formulario de solicitud del servicio de voz: traiga su propia cuenta de almacenamiento (BYOS)](https://aka.ms/cogsvc-cmk)
* [¿Qué son las identidades administradas?](../../active-directory/managed-identities-azure-resources/overview.md)
