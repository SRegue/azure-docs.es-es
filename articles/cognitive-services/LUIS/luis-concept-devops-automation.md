---
title: Flujos de trabajo de integración continua y entrega continua para aplicaciones de LUIS
description: Procedimiento para implementar flujos de trabajo de CI/CD de DevOps para Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/01/2021
ms.author: aahi
author: aahill
ms.manager: nitinme
ms.openlocfilehash: 7079c1ee309db9563142c54eea88ccd4ba6f6e28
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112463646"
---
# <a name="continuous-integration-and-continuous-delivery-workflows-for-luis-devops"></a>Flujos de trabajo de integración continua y entrega continua de DevOps para LUIS

Los ingenieros de software que desarrollan una aplicación de Language Understanding (LUIS) pueden aplicar prácticas de DevOps en torno al [control de código fuente](luis-concept-devops-sourcecontrol.md), las [compilaciones automatizadas](luis-concept-devops-automation.md), las [pruebas](luis-concept-devops-testing.md) y la [administración de versiones](luis-concept-devops-automation.md#release-management). En este artículo se describen conceptos sobre la implementación de compilaciones automatizadas para LUIS.

## <a name="build-automation-workflows-for-luis"></a>Creación de flujos de trabajo de automatización para LUIS

![Flujos de trabajo de CI](./media/luis-concept-devops-automation/luis-automation.png)

En su sistema de administración de código fuente (SCM), configure las canalizaciones de compilaciones automatizadas para que se ejecuten en los siguientes eventos:

1. Desencadenamiento de un **flujo de trabajo de PR** al generar una [solicitud de incorporación de cambios](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) (PR). Este flujo de trabajo valida el contenido de la PR *antes* de que las actualizaciones se combinen en la rama principal.
1. Desencadenamiento del **flujo de trabajo de CI/CD** al insertar las actualizaciones en la rama principal; por ejemplo, al combinar los cambios de una PR. Este flujo de trabajo garantiza la calidad de todas las actualizaciones de la rama principal.

El **flujo de trabajo de CI/CD** combina dos procesos de desarrollo complementarios:

* La [integración continua](/devops/develop/what-is-continuous-integration) (CI) es la práctica de ingeniería que consiste en confirmar con frecuencia el código en un repositorio compartido y realizar una compilación automatizada con él. Junto con un enfoque de [pruebas](luis-concept-devops-testing.md) automatizado, la integración continua nos permite comprobar que, para cada actualización, el origen de LUDown siga siendo válido y se pueda importar a una aplicación de LUIS, y también que pase un grupo de pruebas que comprueben que la aplicación entrenada pueda reconocer las intenciones y las entidades necesarias para la solución.

* La [entrega continua](/devops/deliver/what-is-continuous-delivery) (CD) va más allá de la integración continua, ya que implementa la aplicación automáticamente en un entorno en el que es posible realizar pruebas más exhaustivas. La CD nos permite saber con anticipación si se ha producido algún imprevisto a raíz de los cambios lo más rápidamente posible. También nos permite obtener información sobre las deficiencias de nuestra cobertura de pruebas.

El objetivo de la integración continua y entrega continua es garantizar que el "elemento principal siempre se pueda enviar". En el caso de una aplicación de LUIS, esto significa que podríamos tomar cualquier versión de la aplicación de LUIS de la rama principal y enviarla a producción si es necesario.

### <a name="tools-for-building-automation-workflows-for-luis"></a>Herramientas para crear flujos de trabajo de automatización para LUIS

> [!TIP]
> En el [repositorio de plantillas de LUIS DevOps](#apply-devops-to-luis-app-development-using-github-actions) puede encontrar una solución completa para implementar DevOps.

Hay diferentes tecnologías de automatización de compilaciones disponibles para crear flujos de trabajo de automatización de compilaciones. Todas ellas requieren que se puedan crear scripts para los pasos mediante una interfaz de línea de comandos (CLI) o llamadas de REST para que puedan ejecutarse en un servidor de compilación.

Use las siguientes herramientas para crear flujos de trabajo de automatización para LUIS:

* [Bot Framework Tools LUIS CLI](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) para trabajar con las aplicaciones y las versiones de LUIS, así como entrenarlas, probarlas y publicarlas en el servicio de LUIS.

* [CLI de Azure](/cli/azure/) para consultar las suscripciones de Azure y capturar claves de predicción y creación de LUIS, así como para crear una [entidad de servicio](/cli/azure/ad/sp) de Azure que se use para la autenticación de la automatización.

* Herramienta [NLU.DevOps](https://github.com/microsoft/NLU.DevOps) para [probar una aplicación de LUIS](luis-concept-devops-testing.md) y para analizar los resultados de la prueba.

### <a name="the-pr-workflow"></a>Flujo de trabajo de PR

Tal y como hemos mencionado, configure este flujo de trabajo para que se ejecute cuando un desarrollador genere una PR para proponer la combinación de los cambios de una rama de características con la principal. Su finalidad es comprobar la calidad de los cambios en la PR antes de combinarlos en la rama principal.

Este flujo de trabajo debe realizar lo siguiente:

* Crear una aplicación de LUIS temporal importando el origen de `.lu` en la PR.
* Entrenar la versión de la aplicación de LUIS y publicarla.
* Ejecutar en esta todas las [pruebas unitarias](luis-concept-devops-testing.md).
* Pasar el flujo de trabajo si se superan todas las pruebas; de lo contrario, deberá generarse un error.
* Limpiar y eliminar la aplicación temporal.

Si el Administrador de control de servicios lo admite, configure las reglas de protección de ramas para que este flujo de trabajo se complete correctamente antes de que se pueda completar la PR.

### <a name="the-main-branch-cicd-workflow"></a>Flujo de trabajo de CI/CD de la rama principal

Configure este flujo de trabajo para que se ejecute una vez que las actualizaciones de la PR se hayan combinado en la rama principal. Su finalidad es mantener alto el nivel de calidad de la rama principal mediante pruebas en las actualizaciones. Si las actualizaciones cumplen el nivel de calidad, este flujo de trabajo implementará la nueva versión de la aplicación de LUIS en un entorno en el que podrá realizar pruebas más exhaustivas.

Este flujo de trabajo debe realizar lo siguiente:

* Crear una nueva versión en la aplicación de LUIS principal (la aplicación que mantiene para la rama principal) con el código fuente actualizado.

* Entrenar la versión de la aplicación de LUIS y publicarla.

  > [!NOTE]
  > Como se explica en [Ejecución de pruebas en un flujo de trabajo de compilación automatizada](luis-concept-devops-testing.md#running-tests-in-an-automated-build-workflow), debe publicar la versión de la aplicación de LUIS en fase de prueba para que herramientas como NLU.DevOps puedan acceder a ella. LUIS solo admite dos espacios de publicación con nombre, *almacenamiento provisional* y *producción*, para una aplicación de LUIS, pero también puede [publicar una versión directamente](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish) y [realizar una consulta por versión](./luis-migration-api-v3.md#changes-by-slot-name-and-version-name). Use la publicación de versiones directa en los flujos de trabajo de automatización para evitar verse limitado al uso de los espacios de publicación con nombre.

* Ejecute todas las [pruebas unitarias](luis-concept-devops-testing.md).

* Si quiere, ejecute [pruebas por lotes](luis-concept-devops-testing.md#how-to-do-unit-testing-and-batch-testing) para medir la calidad y la precisión de la versión de la aplicación de LUIS y compararla con alguna línea de base.

* Si las pruebas se completan correctamente:
  * Etiquete el origen en el repositorio.
  * Ejecute el trabajo de entrega continua (CD) para implementar la versión de la aplicación de LUIS en entornos para realizar más pruebas.

### <a name="continuous-delivery-cd"></a>Entrega continua (CD)

El trabajo de CD en un flujo de trabajo de CI/CD se ejecuta de manera condicional en la realización correcta de la compilación y las pruebas unitarias automatizadas. Su trabajo consiste en implementar automáticamente la aplicación de LUIS en un entorno en el que pueda realizar más pruebas.

No hay ninguna solución que se recomiende más que otra para la implementación de la aplicación de LUIS. Además, deberá implementar el proceso adecuado para su proyecto. El repositorio de plantillas [de DevOps para LUIS](https://github.com/Azure-Samples/LUIS-DevOps-Template) implementa una solución sencilla para esto, que consiste en la [publicación de la nueva versión de la aplicación de LUIS](./luis-how-to-publish-app.md) en el espacio de publicación *producción*. Esto es adecuado para una configuración simple. Sin embargo, si necesita admitir varios entornos de producción diferentes al mismo tiempo, como *desarrollo*, *almacenamiento provisional* y *Pruebas de aceptación de usuario*, el límite de dos espacios de publicación con nombre por aplicación no será suficiente.

Otras opciones para implementar una versión de la aplicación son las siguientes:

* Dejar la versión de la aplicación publicada en el punto de conexión de la versión directa e implementar un proceso para configurar los entornos de producción de nivel inferior con el punto de conexión de la versión directa según sea necesario.
* Mantener diferentes aplicaciones de LUIS para cada entorno de producción y escribir los pasos de automatización para importar el elemento `.lu` a una nueva versión de la aplicación de LUIS para el entorno de producción de destino, para entrenarlo y publicarlo.
* Exportar la versión de la aplicación de LUIS comprobada a un [contenedor de Docker de LUIS](./luis-container-howto.md?tabs=v3) e implementar el contenedor de LUIS en Azure [Container Instances](../../container-instances/index.yml).

## <a name="release-management"></a>Administración de versiones

Por lo general, se recomienda realizar la entrega continua solo a los entornos que no sean de producción, como los de desarrollo y almacenamiento provisional. La mayoría de los equipos requieren un proceso de revisión y aprobación manual para implementarse un entorno de producción. En el caso de una implementación de producción, quizá quiera asegurarse de que se produce cuando las personas clave del equipo de desarrollo están disponibles para dar soporte técnico, o durante períodos de tráfico bajo.


## <a name="apply-devops-to-luis-app-development-using-github-actions"></a>Aplicación de DevOps al desarrollo de aplicaciones de LUIS mediante Acciones de GitHub

Vaya al [repositorio de plantillas de DevOps para LUIS](https://github.com/Azure-Samples/LUIS-DevOps-Template), donde obtendrá una solución completa que implementará los procedimientos recomendados de ingeniería de software y DevOps para LUIS. Este repositorio de plantillas se puede usar para crear un propio repositorio con compatibilidad integrada con los flujos de trabajo y procedimientos de CI/CD que habilitan el [control de código fuente](luis-concept-devops-sourcecontrol.md), las compilaciones automatizadas, las [pruebas](luis-concept-devops-testing.md) y la administración de versiones con LUIS para su propio proyecto.

En el [repositorio de plantillas de DevOps para LUIS](https://github.com/Azure-Samples/LUIS-DevOps-Template) se explican los procedimientos para realizar lo siguiente:

* **Clonar el repositorio de plantillas**: copie la plantilla en su propio repositorio de GitHub.
* **Configurar los recursos de LUIS**: cree los [recursos de creación y predicción de LUIS en Azure](./luis-how-to-azure-subscription.md) que usarán los flujos de trabajo de integración continua.
* **Configurar los flujos de trabajo de CI/CD**: configure los parámetros de los flujos de trabajo de CI/CD y almacénelos en [Secretos de GitHub](https://help.github.com/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets).
* **Comprender el [bucle interno de desarrollo](/dotnet/architecture/containerized-lifecycle/design-develop-containerized-apps/docker-apps-inner-loop-workflow)** : el desarrollador realiza actualizaciones en una aplicación de LUIS de ejemplo mientras trabaja en una rama de desarrollo, prueba las actualizaciones y, a continuación, genera una solicitud de incorporación de cambios para proponer cambios y para buscar la aprobación de la revisión.
* **Ejecutar flujos de trabajo de CI/CD**: ejecute [flujos de trabajo de integración continua para compilar y probar una aplicación de LUIS](#build-automation-workflows-for-luis) mediante Acciones de GitHub.
* **Realizar pruebas automatizadas**: realice [pruebas por lotes automatizadas para una aplicación de LUIS](luis-concept-devops-testing.md) a fin de evaluar la calidad de esta.
* **Implementar la aplicación de LUIS**: ejecute un [trabajo de entrega continua (CD)](#continuous-delivery-cd) para publicar la aplicación de LUIS.
* **Usar el repositorio con su propio proyecto**: use el repositorio con su propia aplicación de LUIS.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre cómo escribir un [flujo de trabajo de Acciones de GitHub con NLU.DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template/blob/master/docs/4-pipeline.md).

* Use el [repositorio de plantillas de DevOps para LUIS](https://github.com/Azure-Samples/LUIS-DevOps-Template) con el fin de aplicar DevOps con su propio proyecto.
* [Control de código fuente y estrategias de ramificación para LUIS](luis-concept-devops-sourcecontrol.md)
* [Pruebas de DevOps para LUIS](luis-concept-devops-testing.md)
