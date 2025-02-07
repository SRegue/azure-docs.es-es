---
title: 'Inicio rápido: Creación de la primera función de C# en Azure mediante Visual Studio'
description: En este inicio rápido, aprenderá a usar Visual Studio para crear y publicar una función de C# desencadenada por HTTP en Azure Functions que se ejecuta en .NET Core 3.1.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 05/18/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f, contperf-fy21q3-portal
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./functions-create-your-first-function-visual-studio-uiex
ms.openlocfilehash: 5a7df784ec30b958eb6924de674e09cbbe21c91e
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830051"
---
# <a name="quickstart-create-your-first-c-function-in-azure-using-visual-studio"></a>Inicio rápido: Creación de la primera función de C# en Azure mediante Visual Studio

Azure Functions permite ejecutar el código de C# en un entorno sin servidor en Azure. 

En este artículo aprenderá a:

> [!div class="checklist"]
> * Use Visual Studio para crear un proyecto de biblioteca de clases de C# (.NET Core 3.1).
> * Cree una función que responda a solicitudes HTTP. 
> * Ejecute el código localmente para comprobar el comportamiento de la función.
> * Implemente el proyecto de código en Azure Functions. 
 
En este artículo se admite la creación de ambos tipos de funciones de C# compiladas: 

+ [En proceso](functions-create-your-first-function-visual-studio.md?tabs=in-process): se ejecuta en el mismo proceso que el proceso de host de Functions. Para obtener más información, consulte [Desarrollo de funciones de la biblioteca de clases de C# con Azure Functions](functions-dotnet-class-library.md).
+ [Proceso aislado](functions-create-your-first-function-visual-studio.md?tabs=isolated-process): se ejecuta en un proceso de trabajo de .NET independiente. Para más información, consulte la [Guía para ejecutar funciones en .NET 5.0 en Azure](dotnet-isolated-process-guide.md).

Este inicio rápido supone un pequeño costo en su cuenta de Azure.

También hay una [versión basada en Visual Studio Code](create-first-function-vs-code-csharp.md) de este artículo.

## <a name="prerequisites"></a>Prerrequisitos

+ [Visual Studio 2019](https://azure.microsoft.com/downloads/). Asegúrese de seleccionar la carga de trabajo de **desarrollo de Azure** durante la instalación. 

+ [Suscripción de Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing). Si aún no tiene ninguna cuenta, cree una [gratuita](https://azure.microsoft.com/free/dotnet/) antes de empezar.

## <a name="create-a-function-app-project"></a>Creación de un proyecto de aplicación de función

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio crea un proyecto y una clase que contiene código reutilizable para el tipo de función de desencadenador HTTP. El código reutilizable envía una respuesta HTTP que incluye un valor del cuerpo de la solicitud o de la cadena de consulta. El atributo `HttpTrigger` especifica que la función es desencadenada por una solicitud HTTP. 

## <a name="rename-the-function"></a>Cambio del nombre de la función

El atributo del método `FunctionName` establece el nombre de la función que, de forma predeterminada, se genera como `Function1`. Como las herramientas no le permiten reemplazar el nombre de la función predeterminada al crear un proyecto, dedique un minuto a crear un nombre mejor para la clase, el archivo y los metadatos de la función.

1. En el **Explorador de archivos**, haga clic con el botón derecho en el archivo Function1.cs y cambie su nombre a `HttpExample.cs`.

1. En el código, cambie el nombre de la clase Function1 a `HttpExample`.

1. En el método `HttpTrigger` denominado `Run`, cambie el nombre del atributo del método `FunctionName` a `HttpExample`. 

La definición de la función ahora debe parecerse al código siguiente:

# <a name="in-process"></a>[En proceso](#tab/in-process) 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs" range="15-18"::: 

# <a name="isolated-process"></a>[Proceso aislado](#tab/isolated-process)

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-isolated/HttpExample.cs" range="11-13"::: 

---

Ahora que ha cambiado el nombre de la función, puede probarla en el equipo local.

## <a name="run-the-function-locally"></a>Ejecución local de la función

Visual Studio se integra con Azure Functions Core Tools; de este modo puede probar las funciones localmente mediante el sistema en tiempo de ejecución completo de Azure Functions.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Después de comprobar que la función se ejecuta correctamente en el equipo local es el momento de publicar el proyecto en Azure.

## <a name="publish-the-project-to-azure"></a>Publicar el proyecto en Azure

Para poder publicar el proyecto, debe tener una aplicación de funciones en la suscripción de Azure. La publicación de Visual Studio crea una aplicación de funciones la primera vez que se publica el proyecto.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="verify-your-function-in-azure"></a>Comprobación de la función en Azure

1. En Cloud Explorer, se debe seleccionar la nueva aplicación de funciones. Si no es así, expanda su suscripción > **App Services** y seleccione la nueva aplicación de funciones.

1. Haga clic con el botón derecho en la aplicación de funciones y elija **Abrir en explorador**. Se abre la raíz de su aplicación de funciones en el explorador web predeterminado y muestra la página que indica que su aplicación de funciones se está ejecutando. 

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="Aplicación de funciones en ejecución":::

1. En la barra de direcciones del explorador, anexe la cadena `/api/HttpExample?name=Functions` a la URL base y ejecute la solicitud.

    La dirección URL que llama a la función de desencadenador HTTP tiene el formato siguiente:

    `http://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`

1. Vaya a esta dirección URL y verá una respuesta en el explorador para la solicitud GET remota devuelta por la función, que es similar a la del siguiente ejemplo:

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png" alt-text="Respuesta de la función en el explorador":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Otras guías de inicio rápido de esta colección se basan en los valores de esta. Si tiene previsto trabajar con las siguientes guías de inicio rápido, tutoriales o con cualquiera de los servicios que haya creado en esta guía de inicio rápido, no elimine los recursos.

En Azure, los *recursos* son aplicaciones de función, funciones o cuentas de almacenamiento, entre otros. Se agrupan en *grupos de recursos* y se puede eliminar todo el contenido de un grupo si este se elimina. 

Ha creado recursos para completar estas guías de inicio rápido. Se le pueden facturar por estos recursos, dependiendo del [estado de la cuentade los ](https://azure.microsoft.com/account/) y [precios de los servicios](https://azure.microsoft.com/pricing/). 

[!INCLUDE [functions-vstools-cleanup](../../includes/functions-vstools-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha usado Visual Studio para crear y publicar una aplicación de funciones en C# en Azure con una función de desencadenador HTTP sencilla. 

Prosiga en el siguiente artículo para aprender a agregar un enlace de cola de Azure Storage a la función:
> [!div class="nextstepaction"]
> [Adición de un enlace de cola de Azure Storage a una función](functions-add-output-binding-storage-queue-vs.md)

