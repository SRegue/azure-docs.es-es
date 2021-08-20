---
ms.openlocfilehash: a04d7dde8b6f2a492538d2012de5222686fd7fcf
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113762348"
---
## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/dotnet/).
- Instale el [módulo Azure Az PowerShell](/powershell/azure/).

## <a name="create-azure-communication-services-resource"></a>Creación de un recurso de Azure Communication Services

Para crear un recurso de Azure Communication Services, [inicie sesión en la CLI de Azure](/cli/azure/authenticate-azure-cli). Esto puede hacerlo mediante el terminal con el comando ```Connect-AzAccount``` y proporcionando sus credenciales. Ejecute el siguiente comando para crear el recurso:

```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global
```

Si quiere seleccionar una suscripción específica, también puede especificar la marca ```--subscription``` y proporcionar el identificador de la suscripción.
```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global -SubscriptionId SubscriptionID
```

Puede configurar el recurso de Communication Services con las opciones siguientes:

* El grupo de recursos
* El nombre del recurso de Communication Services
* La geografía a la que se asociará el recurso

En el paso siguiente, puede asignar etiquetas al recurso. Las etiquetas se pueden usar para organizar los recursos de Azure. Para obtener más información sobre las etiquetas, consulte la [documentación sobre las etiquetas de recursos](../../../azure-resource-manager/management/tag-resources.md).

## <a name="manage-your-communication-services-resource"></a>Administración del recurso de Communication Services

Para agregar etiquetas al recurso de Communication Services, ejecute los siguientes comandos: También puede usar una suscripción específica como destino.

```PowerShell
PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"}

PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"} -SubscriptionId SubscriptionID
```

Para enumerar todos los recursos de Azure Communication Services en una suscripción determinada, use el siguiente comando:

```PowerShell
PS C:\> Get-AzCommunicationService -SubscriptionId SubscriptionID
```

Para mostrar toda la información de un recurso determinado, use el siguiente comando:

```PowerShell
PS C:\> Get-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1
```