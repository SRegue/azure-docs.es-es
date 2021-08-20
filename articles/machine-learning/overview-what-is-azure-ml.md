---
title: ¿Qué es Azure Machine Learning?
description: Azure Machine Learning es una solución de ciencia de datos integrada para científicos de datos y MLops para modelar e implementar aplicaciones de aprendizaje automático a escala de nube.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.author: larryfr
author: BlackMist
ms.date: 04/08/2021
ms.custom: devx-track-python
adobe-target: true
ms.openlocfilehash: ac46134e2e74704f57142e2fcb8018ad82d7fe67
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2021
ms.locfileid: "114667245"
---
# <a name="what-is-azure-machine-learning"></a>¿Qué es Azure Machine Learning?

En este artículo, obtendrá información sobre Azure Machine Learning, un entorno en la nube que puede usar para entrenar, implementar, automatizar, administrar y realizar un seguimiento de los modelos de aprendizaje automático. 

Azure Machine Learning se puede usar para todos los tipos de aprendizaje automático, desde el clásico hasta el aprendizaje profundo, supervisado y no supervisado. Independientemente de que prefiera escribir código de Python o de R con el SDK o trabajar con las opciones de los tipos sin código/código bajo en [Studio](#build-ml-models-in-the-studio), puede crear, entrenar y realizar un seguimiento de los modelos tanto de aprendizaje automático como de aprendizaje profundo en un área de trabajo de Azure Machine Learning. 

Comience a entrenar en su máquina local y luego escale horizontalmente a la nube. 

El servicio también interopera con herramientas aprendizaje profundo y de código abierto populares, como PyTorch, TensorFlow, scikit-learn y Ray RLlib. 

> [!Tip]
> **Evaluación gratuita**  Si no tiene una suscripción a Azure, cree una cuenta gratuita antes de empezar. Pruebe hoy mismo la [versión gratuita o de pago de Azure Machine Learning](https://azure.microsoft.com/free/). Puede obtener créditos para gastarlos en servicios de Azure. Después de que se agoten los créditos, puede mantener la cuenta y usar los [servicios gratuitos de Azure](https://azure.microsoft.com/free/). No se le realizará ningún cargo en su tarjeta de crédito a menos que cambie explícitamente la configuración y lo solicite.


## <a name="what-is-machine-learning"></a>¿Qué es el aprendizaje automático?

El aprendizaje automático es una técnica de ciencia de datos que permite a los equipos utilizar datos existentes para prever tendencias, resultados y comportamientos futuros. Mediante el aprendizaje automático, los equipos aprenden sin necesidad de programarlos explícitamente.

Las previsiones o predicciones del aprendizaje automático pueden hacer que las aplicaciones y los dispositivos sean más inteligentes. Por ejemplo, cuando compra en línea, el aprendizaje automático ayuda a recomendar otros productos según lo que haya adquirido. O, cuando pasa su tarjeta de crédito, el aprendizaje automático compara la transacción con una base de datos de transacciones y ayuda a detectar fraudes. Y cuando la aspiradora robot aspira una sala, el aprendizaje automático le ayuda a decidir si se ha terminado el trabajo.

## <a name="machine-learning-tools-to-fit-each-task"></a>Herramientas de aprendizaje automático que se ajustan a cada tarea 

Azure Machine Learning proporciona todas las herramientas que los desarrolladores y científicos de datos necesitan para sus flujos de trabajo de aprendizaje automático, entre las que se incluyen:
+ El [diseñador de Azure Machine Learning](tutorial-designer-automobile-price-train-score.md): módulos de arrastrar y colocar para compilar los experimentos e implementar canalizaciones en un entorno de poco código.

+ Cuadernos de Jupyter Notebook: use nuestros [cuadernos de ejemplo](https://github.com/Azure/MachineLearningNotebooks) o cree los suyos propios para aprovechar los ejemplos del <a href="/python/api/overview/azure/ml/intro" target="_blank">SDK para Python</a> para el aprendizaje automático. 

+ La [extensión de Machine Learning para Visual Studio Code (versión preliminar)](how-to-set-up-vs-code-remote.md) proporciona un entorno de desarrollo completo para crear y administrar proyectos de aprendizaje automático.

+ [La CLI de Machine Learning](reference-azure-machine-learning-cli.md) es una extensión de la CLI de Azure que proporciona comandos para administrarlos con los recursos de Azure Machine Learning desde la línea de comandos.

+ [Integración con marcos de código abierto](concept-open-source.md) como PyTorch, TensorFlow y scikit-learn, entre muchos otros, para entrenar, implementar y administrar el proceso de aprendizaje automático de un extremo a otro.

+ [Aprendizaje de refuerzo](how-to-use-reinforcement-learning.md) con Ray RLlib

Incluso puede usar [MLflow para realizar un seguimiento de las métricas e implementar modelos](how-to-use-mlflow.md) o Kubeflow para [compilar canalizaciones de flujo de trabajo de un extremo a otro](https://www.kubeflow.org/docs/azure/).

## <a name="build-ml-models-with-the-python-sdk"></a>Creación de modelos de ML con el SDK de Python

Empiece el entrenamiento en la máquina local mediante el <a href="/python/api/overview/azure/ml/intro" target="_blank">SDK de Python </a> para Azure Machine Learning. Luego, puede escalar horizontalmente a la nube. 

Con muchos [destinos de proceso](how-to-create-attach-compute-studio.md) disponibles, por ejemplo, los procesos de Azure Machine Learning y [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks), y con los [servicios avanzados de ajuste de hiperparámetros](how-to-tune-hyperparameters.md), puede compilar mejores modelos de forma más rápida gracias al potencial de la nube.

También puede [automatizar el entrenamiento y optimización del modelo](tutorial-auto-train-models.md) mediante el SDK.

## <a name="build-ml-models-in-the-studio"></a>Creación de modelos de Machine Learning en Studio

[Azure Machine Learning Studio](https://studio.azureml.net) es un portal web de Azure Machine Learning para las opciones de los tipos código bajo y sin código del entrenamiento, la implementación y la administración de recursos. Studio se integra con el SDK de Azure Machine Learning para ofrecer una experiencia sin problemas. Para más información, consulte el artículo en el que se explica [qué es Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md).

+ **Diseñador de Azure Machine Learning**

  Use el [diseñador](concept-designer.md) para entrenar e implementar modelos de Machine Learning sin necesidad de escribir código. Pruebe el [tutorial del diseñador](tutorial-designer-automobile-price-train-score.md) para comenzar. 

  ![GIF animado de la interfaz de arrastrar y colocar del diseñador de Azure Machine Learning](media/concept-designer/designer-drag-and-drop.gif)

+ **Seguimiento de experimentos**

  Aprenda a [realizar un seguimiento los experimentos de la ciencia de datos](how-to-track-monitor-analyze-runs.md) y a visualizarlos en Studio. 

    :::image type="content" source="media/how-to-track-monitor-analyze-runs/run-history.png" alt-text="Detalles de la ejecución en Azure Machine Learning Studio":::


+ **Y mucho más...**

  Visite Azure Machine Learning Studio en [ml.azure.com](https://studio.azureml.net).


## <a name="mlops-deploy--lifecycle-management"></a>MLOps: Administración de la implementación y del ciclo de vida
Cuando tenga el modelo adecuado, podrá usarlo fácilmente en un servicio web, en un dispositivo de IoT o en Power BI. Para más información, consulte el artículo sobre [cómo y dónde llevar a cabo la implementación](how-to-deploy-and-where.md).

Luego, puede administrar los modelos implementados mediante el [SDK de Azure Machine Learning para Python](/python/api/overview/azure/ml/), [Azure Machine Learning Studio](https://ml.azure.com) o la [CLI de Machine Learning](reference-azure-machine-learning-cli.md).

Se pueden usar estos modelos para que devuelvan predicciones en [tiempo real](how-to-consume-web-service.md) o de forma [asincrónica](./tutorial-pipeline-batch-scoring-classification.md) para grandes cantidades de datos.

Y con las [canalizaciones de aprendizaje automático](concept-ml-pipelines.md) avanzadas, puede colaborar en cada paso desde la preparación de datos, el entrenamiento y la evaluación de modelos hasta su implementación. Las canalizaciones permiten:

* Automatizar el proceso de aprendizaje automático de un extremo a otro en la nube
* Reutilizar los componentes y volver a ejecutar los pasos cuando sea necesario
* Usar recursos de proceso diferentes en cada paso
* Ejecutar tareas de puntuación por lotes

Si desea usar scripts para automatizar el flujo de trabajo de aprendizaje automático, la [CLI de Machine Learning](reference-azure-machine-learning-cli.md) proporciona herramientas de línea de comandos que realizan tareas comunes, como el envío de una ejecución de entrenamiento o la implementación de un modelo.

Para comenzar a usar Azure Machine Learning, consulte la sección [Pasos siguientes](#next-steps).

## <a name="integration-with-other-services"></a>Integración con otros servicios

Azure Machine Learning funciona con otros servicios de la plataforma Azure y también se integra con herramientas de código abierto como Git y MLFlow.

+ Destinos de proceso, como __Azure Kubernetes Service__, __Azure Container Instances__, __Azure Databricks__, __Azure Data Lake Analytics__ y __de Azure HDInsight__. Para más información sobre los destinos de proceso, consulte [¿Qué son los destinos de proceso?](concept-compute-target.md).
+ __Azure Event Grid__. Para más información, consulte [Consumo de eventos de Azure Machine Learning](./how-to-use-event-grid.md).
+ __Azure Monitor__. Para más información, consulte [Supervisión de Azure Machine Learning](monitor-azure-machine-learning.md).
+ Almacenes de datos, como las __cuentas de Azure Storage__, __Azure Data Lake Storage__, __Azure SQL Database__, __Azure Database for PostgreSQL__ y __Azure Open Datasets__. Para más información, consulte [Acceso a los datos en los servicios de almacenamiento de Azure](how-to-access-data.md) y [Creación de conjuntos de datos con Azure Open Datasets](how-to-create-register-datasets.md).
+ __Redes virtuales de Azure__. Para más información, consulte [Información general sobre aislamiento y privacidad de redes virtuales](how-to-network-security-overview.md).
+ __Azure Pipelines__. Para más información, consulte [Entrenamiento e implementación de modelos de aprendizaje automático](/azure/devops/pipelines/targets/azure-machine-learning).
+ __Registros del repositorio de Git__. Para más información, consulte [Integración de Git](concept-train-model-git-integration.md).
+ __MLFlow__. Para más información, consulte[MLFlow para realizar un seguimiento de las métricas](how-to-use-mlflow.md) e [Implementar modelos de MLFlow como un servicio web](how-to-deploy-mlflow-models.md). 
+ __Kubeflow__. Para más información, consulte [Compilación de canalizaciones de flujo de trabajo de un extremo a otro](https://www.kubeflow.org/docs/azure/).

### <a name="secure-communications"></a>Comunicaciones seguras

La cuenta de Azure Storage, los destinos de proceso y otros recursos se pueden usar de forma segura dentro de una red virtual para entrenar modelos y realizar la inferencia. Para más información, consulte [Información general sobre aislamiento y privacidad de redes virtuales](how-to-network-security-overview.md).

## <a name="next-steps"></a>Pasos siguientes

Empiece con el artículo de [inicio rápido: introducción a Azure Machine Learning](quickstart-create-resources.md).  Después use estos recursos para crear su primer experimento con el método que prefiera:

  + [Ejecución de un script de Python "Hola mundo" (parte 1 de 3)](tutorial-1st-experiment-hello-world.md)
  + [Uso de un cuaderno de Jupyter para entrenar modelos de clasificación de imágenes](tutorial-train-models-with-aml.md)
  + [Uso del aprendizaje automático automatizado para entrenar e implementar modelos de aprendizaje automático](tutorial-first-experiment-automated-ml.md) 
  + [Administración de recursos en Visual Studio Code](how-to-manage-resources-vscode.md)
  + [Uso de Visual Studio Code para entrenar e implementar un modelo de clasificación de imágenes](tutorial-train-deploy-image-classification-model-vscode.md)
  + [Uso de las funcionalidades de arrastrar y colocar del diseñador para entrenar e implementar](tutorial-designer-automobile-price-train-score.md) 
  + [Uso de la CLI de Machine Learning para entrenar un modelo](how-to-train-cli.md)

- Aprenda sobre [la canalización de aprendizaje automático ](concept-ml-pipelines.md) para crear, optimizar y administrar los escenarios de aprendizaje automático.

- Lea el artículo detallado [Arquitectura y conceptos del servicio Azure Machine Learning](concept-azure-machine-learning-architecture.md).
