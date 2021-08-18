---
title: Programas de ejemplo y Tutoriales de Machine Learning
titleSuffix: Azure Data Science Virtual Machine
description: Mediante estos ejemplos y tutoriales, aprenda a administrar tareas comunes y escenarios con Data Science Virtual Machine.
keywords: herramientas de ciencia de datos, máquina virtual de ciencia de datos, herramientas para la ciencia de datos, ciencia de datos de linux
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: conceptual
ms.date: 05/12/2021
ms.openlocfilehash: d907be8262fdc403f1e7b550d57c1aeaf77491fa
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2021
ms.locfileid: "112369063"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Ejemplos de Azure Data Science Virtual Machines

Azure Data Science Virtual Machine (DSVM) incluye un conjunto completo de código de ejemplo. Estos ejemplos incluyen cuadernos de Jupyter Notebook y scripts en lenguajes como Python y R.
> [!NOTE]
> Para más información acerca de cómo ejecutar los cuadernos de Jupyter Notebook en Data Science Virtual Machine, consulte la sección [Acceso a Jupyter](#access-jupyter).

## <a name="prerequisites"></a>Prerrequisitos

Para ejecutar estos ejemplos, tiene que haber aprovisionado una [instancia de Data Science Virtual Machine de Ubuntu](./dsvm-ubuntu-intro.md).

## <a name="available-samples"></a>Ejemplos disponibles
| Categoría de ejemplos | Descripción | Ubicaciones |
| ------------- | ------------- | ------------- |
| Lenguaje Python  | Ejemplos que explican escenarios como la conexión con los almacenes de datos en la nube basados en Azure y cómo trabajar con Azure Machine Learning.  <br/> [Lenguaje Python](#python-language) | <br/>`~notebooks` <br/><br/>|
| Lenguaje Julia  | Proporciona una descripción detallada del trazado y el aprendizaje profundo en Julia. También explica cómo llamar a C y Python desde Julia. <br/> [Lenguaje Julia](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Explica cómo crear modelos de aprendizaje automático y aprendizaje profundo con Machine Learning. Implemente los modelos en cualquier lenguaje. Utilice el ajuste de hiperparámetros inteligente y el aprendizaje automático automatizado. También puede usar la administración de modelos y el aprendizaje distribuido. <br/> [Machine Learning](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| Cuadernos de PyTorch  | Ejemplos de aprendizaje profundo que usan redes neuronales basadas en PyTorch. Los cuadernos abarcan desde escenarios para principiantes a usuarios avanzados.  <br/> [Cuadernos de PyTorch](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Varios ejemplos diferentes de red neuronal y técnicas implementadas mediante la plataforma TensorFlow. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| H2O   | Ejemplos basados en Python que usan H2O para escenarios de problemas reales. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Lenguaje SparkML  | Ejemplos que usan características del kit de herramientas de Apache Spark MLLib mediante pySpark y MMLSpark: Microsoft Machine Learning para Apache Spark en Apache Spark 2.x.  <br/> [Lenguaje SparkML](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Ejemplos de aprendizaje automático estándar en XGBoost para escenarios como clasificación y regresión. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Acceso a Jupyter 

Para acceder a Jupyter, seleccione el icono de **Jupyter** en el escritorio o en el menú de la aplicación. También puede acceder a Jupyter en una edición de Linux de una instancia de DSVM. Para acceder remotamente desde un explorador web, vaya a `https://<Full Domain Name or IP Address of the DSVM>:8000` en Ubuntu.

Para agregar excepciones y hacer que el acceso a Jupyter esté disponible desde un explorador, utilice la guía siguiente:


![Habilitación de una excepción en Jupyter](./media/ubuntu-jupyter-exception.png)


Inicie sesión con la misma contraseña que ha usado para el inicio de sesión en Data Science Virtual Machine.
<br/>

**Página de inicio de Jupyter**
<br/>![Página de inicio de Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Lenguaje R 
<br/>![Ejemplos de R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Lenguaje Python
<br/>![Ejemplos de Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Lenguaje Julia 
<br/>![Ejemplos de Julia](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Azure Machine Learning 
<br/>![Ejemplos de Azure Machine Learning](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![Ejemplos de PyTorch](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Ejemplos de TensorFlow](./media/tensorflow-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Ejemplos de H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Ejemplos de SparkML](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Ejemplos de XGBoost](./media/xgboost-samples.png)<br/>