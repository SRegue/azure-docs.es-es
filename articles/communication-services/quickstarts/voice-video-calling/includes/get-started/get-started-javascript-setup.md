---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: rifox
ms.openlocfilehash: efdb3ce1457ec8ac7c66761fab8d0b1ebd730982
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2021
ms.locfileid: "122970255"
---
## <a name="setting-up"></a>Instalación

### <a name="create-a-new-nodejs-application"></a>Creación de una aplicación Node.js

Abra la ventana de comandos o de terminal, cree un nuevo directorio para la aplicación y navegue hasta este.

```console
mkdir calling-quickstart && cd calling-quickstart
```

Ejecute `npm init -y` para crear un archivo **package.json** con la configuración predeterminada.

```console
npm init -y
```

### <a name="install-the-package"></a>Instalar el paquete

Use el comando `npm install` para instalar el SDK de llamadas de Azure Communication Services para JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling@1.1.0 --save
```

Para este inicio rápido se recomiendan las siguientes versiones de webpack:

```console
"webpack": "^4.42.0",
"webpack-cli": "^3.3.11",
"webpack-dev-server": "^3.10.3"
```

La opción `--save` muestra la biblioteca como dependencia en el archivo **package.json**.

### <a name="set-up-the-app-framework"></a>Instalación del marco de la aplicación

Esta guía de inicio rápido usa webpack para agrupar los recursos de la aplicación. Ejecute el siguiente comando para instalar los paquetes de npm webpack, webpack-cli y webpack-dev-server, y mostrarlos como dependencias de desarrollo en el archivo **package.json**:

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Cree un archivo **index.html** en el directorio raíz del proyecto. Este archivo lo usaremos para configurar un diseño básico que permitirá al usuario realizar una llamada.
