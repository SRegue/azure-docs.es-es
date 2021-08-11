---
title: 'Tutorial: Implementación de sitios web de Next.js representados de forma estática en Azure Static Web Apps'
description: Genere e implemente sitios dinámicos de Next.js con Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: eb4a9c69ed29b1f13ab2769044c0067779a5e195
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112454139"
---
# <a name="deploy-static-rendered-nextjs-websites-on-azure-static-web-apps"></a>Implementación de sitios web de Next.js representados de forma estática en Azure Static Web Apps

En este tutorial, aprenderá a implementar un sitio web estático generado por [Next.js](https://nextjs.org) en [Azure Static Web Apps](overview.md). Para empezar, aprenderá a instalar, configurar e implementar una aplicación de Next.js. Durante este proceso, también aprenderá a abordar los desafíos comunes a los que debe enfrentarse a menudo al generar páginas estáticas con Next.js.

## <a name="prerequisites"></a>Prerrequisitos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/).
- Una cuenta de GitHub. [Cree una cuenta gratuita](https://github.com/join).
- [Node.js](https://nodejs.org) instalado.

## <a name="set-up-a-nextjs-app"></a>Configuración de una aplicación de Next.js

En lugar de usar la siguiente CLI de Next.js para crear una aplicación, puede usar un repositorio de inicio que incluya una aplicación de Next.js existente. Este repositorio incluye una aplicación de Next.js con rutas dinámicas, lo que resalta un problema común de implementación. Las rutas dinámicas necesitan una configuración de implementación adicional, algo que abordaremos en un momento.

Para comenzar, cree un repositorio en su cuenta de GitHub desde un repositorio de plantillas.

1. Vaya a [https://github.com/staticwebdev/nextjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nextjs-starter/generate).
1. Asigne el nombre **nextjs-starter** al repositorio.
1. A continuación, clone el nuevo repositorio en la máquina. Asegúrese de reemplazar `<YOUR_GITHUB_ACCOUNT_NAME>` por el nombre de su cuenta.

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nextjs-starter
    ```

1. Vaya a la aplicación de Next.js recién clonada:

   ```bash
   cd nextjs-starter
   ```

1. Instale las dependencias:

    ```bash
    npm install
    ```

1. Inicie la aplicación de Next.js en el entorno de desarrollo:

    ```bash
    npm run dev
    ```

Vaya a `http://localhost:3000` para abrir la aplicación, donde debería ver el siguiente sitio web abierto en el explorador de su preferencia:

:::image type="content" source="media/deploy-nextjs/start-nextjs-app.png" alt-text="Inicio de la aplicación de Next.js":::

Al hacer clic en un marco/biblioteca, debería ver una página de detalles acerca del elemento seleccionado:

:::image type="content" source="media/deploy-nextjs/start-nextjs-details.png" alt-text="Página de detalles":::

## <a name="generate-a-static-website-from-nextjs-build"></a>Generación de un sitio web estático desde la compilación de Next.js

Al compilar un sitio de Next.js mediante `npm run build`, la aplicación se crea como aplicación web tradicional, no como sitio estático. Para generar un sitio estático, utilice la siguiente configuración de aplicación.

1. Para configurar rutas estáticas, cree un archivo denominado _next.config.js_ en la raíz del proyecto y agregue el código siguiente.

    ```javascript
    module.exports = {
      trailingSlash: true,
      exportPathMap: function() {
        return {
          '/': { page: '/' }
        };
      }
    };
    ```

      Esta configuración asigna `/` a la página de Next.js que se envía para la ruta `/`, y que es el archivo de paginación _pages/index.js_.

1. Actualice el script de compilación de _package.json_ para generar también un sitio estático después de la compilación, con el comando `next export`. El comando `export` genera un sitio estático.

    ```json
    "scripts": {
      "dev": "next dev",
      "build": "next build && next export",
    },
    ```

    Ahora, con este comando en contexto, Static Web Apps ejecutará el script `build` cada vez que se inserte una confirmación.

1. Genere un sitio estático:

    ```bash
    npm run build
    ```

    El sitio estático se genera y copia en una carpeta _out_ en la raíz del directorio de trabajo.

    > [!NOTE]
    > Esta carpeta aparece en el archivo _.gitignore_ porque debe generarlo CI/CD en el momento de la implementación.

## <a name="push-your-static-website-to-github"></a>Inserción del sitio web estático en GitHub

Azure Static Web Apps implementa la aplicación desde un repositorio de GitHub y lo sigue haciendo para cada confirmación insertada en una rama designada. Utilice los siguientes comandos para sincronizar los cambios en GitHub.

1. Agregue al "stage" todos los archivos cambiados.

    ```bash
    git add .
    ```

1. Confirme todos los cambios.

    ```bash
    git commit -m "Update build config"
    ```

1. Envíe los cambios a GitHub.

    ```bash
    git push origin main
    ```

## <a name="deploy-your-static-website"></a>Implementación del sitio web estático

En los pasos siguientes se muestra cómo vincular la aplicación que acaba de insertar en GitHub con Azure Static Web Apps. Una vez que esté en Azure, podrá implementar la aplicación en un entorno de producción.

### <a name="create-a-static-app"></a>Creación de una aplicación estática

1. Acceda a [Azure Portal](https://portal.azure.com).
1. Seleccione **Crear un recurso**.
1. Busque **Static Web Apps**.
1. Seleccione **Static Web Apps**.
1. Seleccione **Crear**.
1. En la pestaña _Datos básicos_, especifique los valores siguientes.

    | Propiedad | Value |
    | --- | --- |
    | _Suscripción_ | El nombre de la suscripción de Azure. |
    | _Grupos de recursos_ | **my-nextjs-group**  |
    | _Nombre_ | **my-nextjs-app** |
    | _Tipo de plan_ | **Gratis** |
    | _Región para la API y los entornos de ensayo de Azure Functions_ | Seleccione la región más cercana a la suya. |
    | _Origen_ | **GitHub** |

1. Seleccione **Iniciar sesión con GitHub** y autentíquese con GitHub.

1. Escriba los siguientes valores de GitHub.

    | Propiedad | Value |
    | --- | --- |
    | _Organización_ | Seleccione la organización de GitHub que quiera. |
    | _Repositorio_ | Seleccione **nextjs-starter**. |
    | _Rama_ | Seleccione **main** (principal). |

1. En la sección _Detalles de la compilación_, seleccione **Personalizado** en la lista desplegable _Valores preestablecidos de compilación_ y conserve los valores predeterminados.

1. En _Ubicación de la aplicación_, escriba **/** en el cuadro.
1. Deje en blanco el cuadro _Ubicación de la aplicación_.
1. En el cuadro _Ubicación del resultado_, escriba **fuera**.

### <a name="review-and-create"></a>Revisar y crear

1. Seleccione el botón **Revisar y crear** para comprobar que todos los detalles sean correctos.

1. Seleccione **Crear** para comenzar la creación de la aplicación web estática de App Service y aprovisionar una Acción de GitHub para la implementación.

1. Cuando se complete la implementación, haga clic en **Ir al recurso**.

1. En la ventana _Información general_, haga clic en el vínculo *Dirección URL* para abrir la aplicación implementada.

Si el sitio web no se carga de inmediato, el flujo de trabajo de Acciones de GitHub en segundo plano sigue en ejecución. Una vez que se complete el flujo de trabajo, puede actualizar el explorador para ver la aplicación web.
Si el sitio web no se carga de inmediato, el flujo de trabajo de Acciones de GitHub en segundo plano sigue en ejecución. Una vez que se complete el flujo de trabajo, puede actualizar el explorador para ver la aplicación web.

Para comprobar el estado de los flujos de trabajo de Acciones, navegue a Acciones del repositorio:

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nextjs-starter/actions
```

### <a name="sync-changes"></a>Sincronización de cambios

Al crear la aplicación, Azure Static Web Apps creó un archivo de flujo de trabajo de Acciones de GitHub en el repositorio. Debe poner este archivo en el repositorio local para que el historial de Git se sincronice.

Vuelva al terminal y ejecute el siguiente comando: `git pull origin main`.

## <a name="configure-dynamic-routes"></a>Configuración de rutas dinámicas

Vaya al sitio recién implementado y haga clic en uno de los logotipos de marco o biblioteca. En lugar de obtener una página de detalles, aparecerá una página de error 404.

:::image type="content" source="media/deploy-nextjs/404-in-production.png" alt-text="404 en rutas dinámicas":::

La razón de este error es que Next.js solo generó la página principal en función de la configuración de la aplicación.

## <a name="generate-static-pages-from-dynamic-routes"></a>Generación de páginas estáticas a partir de rutas dinámicas

1. Actualice el archivo _next.config.js_ para que Next.js use una lista de todos los datos disponibles para generar páginas estáticas para cada marco o biblioteca:

   ```javascript
   const data = require('./utils/projectsData');

   module.exports = {
     trailingSlash: true,
     exportPathMap: async function () {
       const { projects } = data;
       const paths = {
         '/': { page: '/' },
       };
  
       projects.forEach((project) => {
         paths[`/project/${project.slug}`] = {
           page: '/project/[path]',
           query: { path: project.slug },
         };
       });
  
       return paths;
     },
   };
   ```

   > [!NOTE]
   > La función `exportPathMap` es asincrónica, por lo que puede hacer una solicitud a una API en esta función y usar la lista devuelta para generar las rutas de acceso.

2. Envíe los nuevos cambios al repositorio de GitHub y espere unos minutos mientras las Acciones de GitHub compilan de nuevo el sitio. Una vez que se complete la compilación, el error 404 desaparecerá.

   :::image type="content" source="media/deploy-nextjs/404-in-production-fixed.png" alt-text="404 en rutas dinámicas corregido":::

> [!div class="nextstepaction"]
> [Configuración de un dominio personalizado](custom-domain.md)
