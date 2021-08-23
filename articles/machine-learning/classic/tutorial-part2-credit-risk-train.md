---
title: 'Tutorial 2 de ML Studio (clásico): Entrenamiento de modelos de riesgo crediticio: Azure'
description: Este tutorial es la segunda parte de las tres partes que conforman la serie de tutoriales de Machine Learning Studio (clásico). Muestra cómo entrenar y evaluar modelos.
keywords: riesgo de crédito, solución de análisis predictivo, evaluación de riesgos
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 0d0cdab6529f95de2936b32dda590f1f0f75e53c
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2021
ms.locfileid: "112580753"
---
# <a name="tutorial-2-train-credit-risk-models---machine-learning-studio-classic"></a>Tutorial 2: Entrenamiento de modelos de riesgo crediticio: Machine Learning Studio (clásico)

**SE APLICA A:**  ![Esta es una marca de verificación, lo que significa que este artículo se aplica a Machine Learning Studio (clásico).](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clásico)   ![Esta es una X, lo que significa que este artículo no se aplica a Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

En este tutorial se explica con detalle el proceso de desarrollo de una solución de análisis predictivo. Va a desarrollar un modelo sencillo en Machine Learning Studio (clásico).  Después puede implementar el modelo como un servicio web de Machine Learning.  Este modelo implementado puede hacer predicciones con datos nuevos. Se trata de la **segunda parte de un tutorial de tres**.

Suponga que necesita predecir el riesgo de crédito de un individuo en función de la información que se proporcionó en una solicitud de crédito.  

La evaluación de riesgos crediticios es un problema complejo, pero en este tutorial se simplificará un poco. Se utilizará como ejemplo de cómo puede crear una solución de análisis predictivo con Machine Learning Studio (clásico). En esta solución se usará Machine Learning Studio (clásico) y un servicio web Machine Learning.  

En este tutorial de tres partes, vamos a comenzar con los datos de riesgo crediticio disponibles públicamente.  Después, desarrollaremos y entrenaremos un modelo predictivo.  Finalmente, vamos a implementar el modelo como servicio web.

En la [parte uno del tutorial](tutorial-part1-credit-risk.md), creó un área de trabajo de Machine Learning Studio (clásico), cargó datos y creó un experimento.

En esta parte del tutorial, se va a ver lo siguiente:
 
> [!div class="checklist"]
> * Entrenamiento de varios modelos
> * Puntuación y evaluación de modelos


En la [parte tres del tutorial](tutorial-part3-credit-risk-deploy.md) se implementará el modelo como servicio web.

## <a name="prerequisites"></a>Requisitos previos

Completar la [parte uno del tutorial](tutorial-part1-credit-risk.md).

## <a name="train-multiple-models"></a><a name="train"></a>Entrenamiento de varios modelos

Una de las ventajas del uso de Machine Learning Studio (clásico) para crear modelos de aprendizaje automático es la posibilidad de probar más de un tipo de modelo a la vez en un solo experimento y comparar los resultados. Este tipo de experimentación ayuda a encontrar la mejor solución al problema.

En el experimento que vamos a crear en este tutorial, crearemos dos tipos diferentes de modelos y después compararemos los resultados de su puntuación para decidir qué algoritmo usar en nuestro experimento final.  

Existen varios modelos entre los que se puede elegir. Para ver cuáles están disponibles, expanda el nodo **Machine Learning** de la paleta de módulos y luego expanda **Initialize Model** (Inicializar modelo) y los nodos que incluye. Teniendo en cuenta el objetivo de este experimento, seleccione los módulos [Two-Class Support Vector Machine][two-class-support-vector-machine] (Máquina de vectores de soporte de dos clases, SVM) y [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Árbol de decisión promovido por dos clases).


Agregará tanto el módulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Árbol de decisión promovido por dos clases) como el módulo [Two-Class Support Vector Machine][two-class-support-vector-machine] (Máquina de vectores dos clases) en este experimento.

### <a name="two-class-boosted-decision-tree"></a>Two-Class Boosted Decision Tree (Árbol de decisión ampliado de dos clases).

En primer lugar, configure el modelo del árbol de decisión ampliado.

1. Busque el módulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Árbol de decisión promovido por dos clases) en la paleta de módulos y arrástrelo al lienzo.

1. Busque el módulo [Train Model][train-model] (Entrenar modelo), arrástrelo al lienzo y conecte la salida del módulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Árbol de decisión ampliados de dos clases) al puerto de entrada izquierdo del módulo [Train Model][train-model] (Entrenar modelo).
   
   El módulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Árbol de decisión promovido por dos clases) inicializa el modelo genérico, y [Entrenar modelo][train-model] usa los datos de entrenamiento para entrenar el modelo. 

1. Conecte la salida izquierda del módulo [Execute R Script (Ejecutar script R)][execute-r-script] izquierdo al puerto de entrada de la derecha del módulo [Train Model (Entrenar modelo)][train-model] (en este tutorial [usó los datos procedentes del lado izquierdo](#train) del módulo Split Data [Dividir datos] para el entrenamiento).
   
   > [!TIP]
   > No necesita dos de las entradas y una de las salidas del módulo [Execute R Script][execute-r-script] (Ejecutar script R) para este experimento, así que las puede dejar desconectadas. 
   > 
   > 

Esta parte del experimento tiene ahora un aspecto similar al siguiente:  

![Training a model](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

Ahora es necesario indicar al módulo [Train Model][train-model] (Entrenar modelo) que desea que el modelo prediga el valor del riesgo crediticio.

1. Seleccione el módulo [Train Model][train-model] (Entrenar modelo). En el panel **Propiedades**, haga clic en **Launch column selector** (Iniciar el selector de columnas).

1. En el cuadro de diálogo **Select a single column** (Seleccionar una sola columna), escriba "riesgo de crédito" en el campo de búsqueda en **Columnas disponibles**, seleccione "Riesgo de crédito" a continuación y haga clic en el botón de la flecha derecha ( **>** ) para mover "Riesgo de crédito" a **Columnas seleccionadas**. 

    ![Seleccione la columna de riesgo de crédito para el módulo Entrenar modelo](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. Haga clic en la marca de verificación **Aceptar**.

### <a name="two-class-support-vector-machine"></a>Máquina de vectores de soporte de dos clases

A continuación, configure el modelo SVM.  

En primer lugar, una breve explicación sobre SVM. Los árboles de decisión ampliados funcionan bien con características de todo tipo. Sin embargo, dado que el módulo SVM genera un clasificador lineal, el modelo que genera tiene el mejor error de prueba cuando todas las características numéricas tienen la misma escala. Para convertir todas las características numéricas a la misma escala, utilice una transformación "Tanh", con el módulo [Normalize Data][normalize-data] (Normalizar datos). Esto transforma los números en el intervalo [0,1]. El módulo SVM convierte las características de cadena en características categóricas y luego en características binarias 0/1. Por lo tanto, no hace falta transformar manualmente las características de cadena. Además, no queremos transformar la columna Credit Risk (Riesgo crediticio, columna 21): es numérica, pero es el valor sobre cuya predicción estamos entrenando al modelo; por tanto, es necesario dejarla tal cual.  

Para configurar el modelo SVM, realice lo siguiente:

1. Busque el módulo [Two-Class Support Vector Machine][two-class-support-vector-machine] (Máquina de vectores de soporte de dos clases) en la paleta de módulos y arrástrelo al lienzo.

1. Haga clic con el botón derecho en el módulo [Train Model][train-model] (Entrenar modelo), seleccione **Copy** (Copiar), haga clic con el botón derecho en el lienzo y seleccione **Paste** (Pegar). La copia del módulo [Train Model][train-model] (Entrenar modelo) tiene la misma selección de columnas que el original.

1. Conecte la salida del módulo [Máquina de vectores de soporte de dos clases][two-class-support-vector-machine] al puerto de entrada izquierdo del módulo [Train Model][train-model] (Entrenar modelo).

1. Busque el módulo [Normalizar datos][normalize-data] y arrástrelo al lienzo.

1. Conecte la salida de la izquierda del módulo [Ejecutar script R][execute-r-script] de la izquierda a la entrada de este módulo (tenga en cuenta que el puerto de salida de un módulo puede estar conectado a más de un módulo distinto).

1. Conecte el puerto de salida izquierdo del módulo [Normalize Data][normalize-data] (Normalizar datos) al puerto de entrada derecho del segundo módulo [Entrenar modelo][train-model].

Esta parte de nuestro experimento debería tener ahora un aspecto similar al siguiente:  

![Training the second model](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

Configure ahora el módulo [Normalize Data][normalize-data] (Normalizar datos):

1. Haga clic para seleccionar el módulo [Normalize Data][normalize-data] (Normalizar datos). En el panel **Propiedades**, seleccione **Tanh** para el parámetro **Transformation method** (Método de transformación).

1. Haga clic en **Launch column selector** (Iniciar el selector de columnas), seleccione "No columns" (Sin columnas) en **Comenzar con**, seleccione **Incluir** en el primer menú desplegable, **Tipo de columna** en el segundo y **Numérica** en el tercero. Esto especifica que todas las columnas numéricas (y solo numéricas) se deben transformar.

1. Haga clic en el signo más (+) a la derecha de esta fila; de esta forma, se crea una fila de menús desplegables. Seleccione **Excluir** en la primera lista desplegable y **Nombres de columna** en la segunda, y escriba "Riesgo de crédito" en el campo de texto. Esto especifica que se debe ignorar la columna Credit Risk (Riesgo crediticio) (debemos hacerlo porque se trata de una columna numérica y, de lo contrario, se transformaría).

1. Haga clic en la marca de verificación **Aceptar**.  

    ![Seleccionar columnas para el módulo Normalize Data (Normalizar datos)](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


El módulo [Normalize Data][normalize-data] (Normalizar datos) está configurado ahora para realizar una transformación Tanh en todas las columnas numéricas excepto en la columna de riesgo de crédito.  

## <a name="score-and-evaluate-the-models"></a>Puntuación y evaluación de modelos

Se utilizan los datos de prueba que se separaron mediante el módulo [Split Data][split] (Dividir datos) para puntuar los modelos entrenados. A continuación podremos comparar los resultados de los dos modelos para ver cuál de ellos generó mejores resultados.  

### <a name="add-the-score-model-modules"></a>Agregar los módulos Score Model (Puntuar modelo)

1. Busque el módulo [Score Model][score-model] (Puntuar modelo) y arrástrelo al lienzo.

1. Conecte el módulo [Train Model][train-model] (Entrenar modelo) que está conectado al módulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Árbol de decisión ampliado de dos clases) al puerto de entrada izquierdo del módulo [Score Model][score-model] (Puntuar modelo).

1. Conecte el módulo derecho [Ejecutar script R][execute-r-script] (los datos de prueba) al puerto de entrada derecho del módulo [Score Model][score-model] (Puntuar modelo).

    ![Módulo Score Model (Puntuar modelo) conectado](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   El módulo [Score Model][score-model] (Puntuar modelo) ahora puede utilizar la información de crédito de los datos de prueba, ejecutarla a través del modelo y comparar las predicciones que el modelo genera con la columna de riesgo de crédito real de los datos de prueba.

1. Copie y pegue el módulo [Score Model][score-model] (Puntuar modelo) para crear una segunda copia.

1. Conecte la salida del modelo SVM; es decir, el puerto de salida del módulo [Train Model][train-model] (Entrenar modelo) que está conectado al módulo [Two-Class Support Vector Machine][two-class-support-vector-machine] (Máquina de vectores de soporte de dos clases), al puerto de entrada del segundo módulo [Score Model][score-model] (Puntuar modelo).

1. En cuanto al modelo SVM, tiene que realizar la misma transformación en los datos de prueba que la que realizó con los datos de entrenamiento. Así pues, copie y pegue el módulo [Normalize Data][normalize-data] (Normalizar datos) para crear una segunda copia y conéctelo al módulo derecho [Ejecutar script R][execute-r-script].

1. Conecte la salida izquierda del segundo módulo [Normalize Data][normalize-data] (Normalizar datos) al puerto de salida derecho del segundo módulo [Score Model][score-model] (Puntuar modelo).

    ![Ambos módulos Score Model (Puntuar modelo) conectados](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>Agregar el módulo Evaluate Model (Evaluar modelo)

Para evaluar los dos resultados de puntuación y compararlos, use un módulo [Evaluate Model][evaluate-model] (Evaluar modelo).  

1. Busque el módulo [Evaluate Model][evaluate-model] (Evaluar modelo) y arrástrelo al lienzo.

1. Conecte el puerto de salida del módulo [Score Model][score-model] (Puntuar modelo) asociado al modelo del árbol de decisión ampliado al puerto de entrada izquierdo del módulo [Evaluate Model][evaluate-model] (Evaluar modelo).

1. Conecte el otro módulo [Score Model][score-model] (Puntuar modelo) al puerto de entrada derecho.  

    ![Módulo Evaluate Model (Evaluar modelo) conectado](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>Ejecutar el experimento y comprobar los resultados

Para ejecutar el experimento, haga clic en el botón **EJECUTAR** bajo el lienzo. Esto puede tardar unos minutos. Aparece un indicador giratorio en cada módulo para indicar que está en ejecución y, cuando el módulo acaba, aparece una marca de verificación de color verde. Cuando todos los módulos tengan una marca de verificación, habrá finalizado la ejecución del experimento.

El experimento debería tener ahora un aspecto similar al siguiente:  

![Evaluating both models](./media/tutorial-part2-credit-risk-train/final-experiment.png)


Para comprobar los resultados, haga clic en el puerto de salida del módulo [Evaluate Model][evaluate-model] (Evaluar modelo) y seleccione **Visualizar**.  

El módulo [Evaluate Model][evaluate-model] (Evaluar modelo) produce un par de curvas y métricas que permiten comparar los resultados de los dos modelos de puntuación. Puede ver los resultados como curvas de características operativas del receptor (ROC), curvas de precisión/sensibilidad o curvas de elevación. También se muestran otros datos como la matriz de confusión y los valores del área bajo la curva (AUC) acumulados, entre otras métricas. También puede cambiar el valor del umbral moviendo el control deslizante a la izquierda o a la derecha, y comprobar cómo afecta esta acción al conjunto de métricas.  

A la derecha del gráfico, haga clic en **Scored dataset** (Conjunto de datos puntuados) o en **Scored dataset to compare** (Conjunto de datos puntuados para comparar) con el fin de resaltar la curva asociada y mostrar debajo las métricas asociadas. En la leyenda de las curvas, "Conjunto de datos puntuados" corresponde al puerto de entrada izquierdo del módulo [Evaluate Model][evaluate-model] (Evaluar modelo); en este caso, se trata del modelo del árbol de decisión ampliado. "Conjunto de datos puntuados para comparar" corresponde al puerto de entrada derecho (el modelo SVM en nuestro caso). Al hacer clic en una de estas etiquetas, la curva del modelo correspondiente se resalta y muestra las métricas correspondientes tal y como se muestra en el gráfico siguiente.  

![ROC curves for models](./media/tutorial-part2-credit-risk-train/roc-curves.png)

Si examina estos valores, podrá decidir cuál es el modelo que más se acerca a ofrecerle los resultados que busca. Puede volver y repetir el experimento cambiando valores de parámetros en los diferentes modelos. 

La ciencia y el arte de interpretar estos resultados y de ajustar el rendimiento del modelo están fuera del ámbito de este tutorial. Para obtener ayuda adicional, puede leer los artículos siguientes:
- [Procedimientos para evaluar el rendimiento de un modelo en Machine Learning Studio (clásico)](evaluate-model-performance.md)
- [Selección de parámetros para optimizar los algoritmos de Machine Learning Studio (clásico)](algorithm-parameters-optimize.md)
- [Interpretación de los resultados de un modelo en Machine Learning Studio (clásico)](interpret-model-results.md)

> [!TIP]
> Cada vez que ejecute el experimento, se guardará un registro de esa iteración en el Historial de ejecuciones. Puede ver estas iteraciones y volver a cualquiera de ellas haciendo clic en **VER HISTORIAL DE EJECUCIÓN** bajo el lienzo. También puede hacer clic en **Prior Run** (Ejecución anterior) en el panel **Propiedades** para volver a la iteración inmediatamente anterior a la que ha abierto.
> 
> Puede hacer una copia de cualquier iteración de su experimento si hace clic en **GUARDAR COMO** bajo el lienzo. 
> Utilice las propiedades **Resumen** y **Descripción** para mantener un registro de lo que ha tratado de hacer en las iteraciones del experimento.
> 
> Para más información, consulte [Administrar iteraciones de experimentos en Machine Learning Studio (clásico)](manage-experiment-iterations.md).  
> 
> 

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha completado estos pasos: 
 
> [!div class="checklist"]
> * Creación de un experimento
> * Entrenamiento de varios modelos
> * Puntuación y evaluación de modelos

Ahora está listo para implementar modelos para estos datos.

> [!div class="nextstepaction"]
> [Tutorial 3: Implementación de modelos](tutorial-part3-credit-risk-deploy.md)


<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[split]: /azure/machine-learning/studio-module-reference/split-data
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[normalize-data]: /azure/machine-learning/studio-module-reference/normalize-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[two-class-boosted-decision-tree]: /azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree
[two-class-support-vector-machine]: /azure/machine-learning/studio-module-reference/two-class-support-vector-machine
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/