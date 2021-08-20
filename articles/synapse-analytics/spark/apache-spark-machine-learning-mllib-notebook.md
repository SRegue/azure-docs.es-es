---
title: 'Tutorial: Compilación de una aplicación de aprendizaje automático con Apache Spark MLlib'
description: Tutorial sobre el uso de MLlib de Apache Spark para crear una aplicación de aprendizaje automático que analice un conjunto de datos con clasificación mediante una regresión logística.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 04/15/2020
ms.author: euang
ms.custom: subject-rbac-steps
ms.openlocfilehash: c0be8241b438a010a44c4b9dbabbb05d5ac290b3
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2021
ms.locfileid: "113217096"
---
# <a name="tutorial-build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Tutorial: Compilación de una aplicación de aprendizaje automático con MLlib de Apache Spark y Azure Synapse Analytics

En este artículo, aprenderá a usar [MLlib](https://spark.apache.org/mllib/) de Apache Spark para crear una aplicación de aprendizaje automático para efectuar análisis predictivos simples en un conjunto de datos abierto de Azure. Spark proporciona bibliotecas de aprendizaje automático integradas. En este ejemplo se usa la *clasificación* a través de la regresión logística.

SparkML y MLlib son bibliotecas básicas de Spark que proporcionan varias utilidades que resultan prácticas para las tareas de aprendizaje automático, incluidas las utilidades adecuadas para:

- clasificación
- Regresión
- Agrupación en clústeres
- Modelado de tema
- Descomposición en valores singulares (SVD) y Análisis de los componentes principales (PCA)
- Comprobación de hipótesis y cálculo de estadísticas de ejemplo

## <a name="understand-classification-and-logistic-regression"></a>Comprensión de la clasificación y la regresión logística

*Clasificación*, una tarea habitual en el aprendizaje automático, es el proceso de ordenación de datos de entrada en categorías. Es trabajo de un algoritmo de clasificación averiguar cómo asignar *etiquetas* a los datos de entrada que se proporcionan. Por ejemplo, piense en un algoritmo de aprendizaje automático que acepta información bursátil como entrada y divide las existencias en dos categorías: acciones que se deberían vender y acciones que se deberían conservar.

La *regresión logística* es un algoritmo que puede usar para la clasificación. La API de regresión logística de Spark es útil para la *clasificación binaria*, o para la clasificación de datos de entrada en uno de los dos grupos. Para más información acerca de la regresión logística, consulte la [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

En resumen, el proceso de regresión logística genera una *función logística* que se puede usar para predecir la probabilidad de que un vector de entrada pertenezca a un grupo o al otro.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Ejemplo de análisis predictivo en los datos de los taxis de Nueva York

En este ejemplo, se usa Spark para realizar un análisis predictivo de los datos de propinas de las carreras de taxi de Nueva York. Los datos están disponibles a través de [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Este subconjunto del conjunto de datos contiene información sobre las carreras de Yellow Taxi, incluida información sobre cada carrera, la hora y la ubicación de inicio y fin, el costo y otros atributos interesantes.

> [!IMPORTANT]
> Se pueden aplicar cargos adicionales por la extracción de estos datos de la ubicación de almacenamiento.

En los pasos siguientes, desarrollará un modelo para predecir si una carrera determinada incluye propina o no.

## <a name="create-an-apache-spark-machine-learning-model"></a>Creación de un modelo de Machine Learning de Apache Spark

1. Cree un cuaderno mediante el kernel de PySpark. Para obtener instrucciones, consulte [Creación de un cuaderno](../quickstart-apache-spark-notebook.md#create-a-notebook).
2. Importe los tipos necesarios para esta aplicación. Copie y pegue el siguiente código en una celda vacía y presione Mayús + Entrar. O bien, ejecute la celda con el icono de reproducción azul situado a la izquierda del código.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp, date_format, col, when
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    Debido a la existencia del kernel PySpark, no necesitará crear ningún contexto explícitamente. El contexto de Spark se crea automáticamente al ejecutar la primera celda de código.

## <a name="construct-the-input-dataframe"></a>Construcción de una trama de datos de entrada

Dado que los datos sin procesar están en formato de Parquet, puede usar el contexto de Spark para extraer el archivo a la memoria como trama de datos directamente. Aunque el código de los pasos siguientes usa las opciones predeterminadas, es posible forzar la asignación de tipos de datos y otros atributos de esquema, de ser necesario.

1. Ejecute las líneas siguientes para crear un marco de datos de Spark pegando el código en una nueva celda. Se recupera este paso mediante la API de Open Datasets. La extracción de todos estos datos genera aproximadamente 1500 millones de filas. 

   En función del tamaño del grupo de Apache Spark sin servidor, los datos sin procesar pueden ser demasiado grandes o tardar demasiado para poder trabajar con ellos. Puede filtrar estos datos hasta algo más pequeño. En el ejemplo de código siguiente se usa `start_date` y `end_date` para aplicar un filtro que devuelve un mes único de datos.

    ```python
    from azureml.opendatasets import NycTlcYellow

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
    ```

2. El inconveniente del filtrado sencillo es que, desde una perspectiva estadística, podría introducir sesgos en los datos. Otro enfoque consiste en usar el muestreo integrado en Spark. 

   El código siguiente reduce el conjunto de datos hasta aproximadamente 2000 filas, si se aplica después del código anterior. Puede usar este paso de muestreo en lugar del filtro simple o junto con él.

    ```python
    # To make development easier, faster, and less expensive, downsample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

3. Ahora es posible examinar los datos para ver qué se ha leído. Normalmente, es mejor revisar los datos con un subconjunto en lugar del conjunto completo, en función del tamaño del conjunto de datos. 

   El código siguiente ofrece dos maneras de ver los datos. La primera es básica. La segunda proporciona una experiencia de cuadrícula mucho más rica, junto con la capacidad de visualizar los datos gráficamente.

    ```python
    #sampled_taxi_df.show(5)
    display(sampled_taxi_df)
    ```

4. En función del tamaño del conjunto de datos generado y de la necesidad de experimentar o ejecutar el cuaderno muchas veces, podría querer almacenar en caché el conjunto de datos localmente en el área de trabajo. Hay tres maneras de realizar el almacenamiento en caché explícito:

   - Guardar el marco de datos localmente como un archivo
   - Usar el marco de datos como vista o tabla temporal
   - Guardar el marco de datos como tabla permanente

Los dos primeros enfoques se incluyen en los siguientes ejemplos de código.

La creación de una vista o tabla temporal ofrece diferentes rutas de acceso a los datos, pero solo durante la vigencia de la sesión de la instancia de Spark.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="prepare-the-data"></a>Preparación de los datos

Los datos en su formato sin procesar no suelen ser adecuados para pasar directamente a un modelo. Debe realizar una serie de acciones en los datos para llevarlos a un estado en el que el modelo pueda consumirlos.

En el código siguiente, se realizan cuatro clases de operaciones:

- La eliminación de valores atípicos o valores incorrectos a través del filtrado.
- La eliminación de columnas que no son necesarias.
- La creación de nuevas columnas derivadas de los datos sin procesar para que el modelo funcione de forma más eficaz. Esta operación a veces se denomina caracterización.
- Etiquetado. Como está llevando a cabo una clasificación binaria (habrá una propina o no en una carrera determinada), es necesario convertir el importe de la propina en un valor 0 o 1.

```python
taxi_df = sampled_taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                )\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0) & (sampled_taxi_df.tipAmount <= 25)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 100)\
                                & (sampled_taxi_df.rateCodeId <= 5)
                                & (sampled_taxi_df.paymentType.isin({"1", "2"}))
                                )
```

Después, realice un segundo paso sobre los datos para agregar las características finales.

```Python
taxi_featurised_df = taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'passengerCount'\
                                                , 'tripDistance', 'weekdayString', 'pickupHour','tripTimeSecs','tipped'\
                                                , when((taxi_df.pickupHour <= 6) | (taxi_df.pickupHour >= 20),"Night")\
                                                .when((taxi_df.pickupHour >= 7) & (taxi_df.pickupHour <= 10), "AMRush")\
                                                .when((taxi_df.pickupHour >= 11) & (taxi_df.pickupHour <= 15), "Afternoon")\
                                                .when((taxi_df.pickupHour >= 16) & (taxi_df.pickupHour <= 19), "PMRush")\
                                                .otherwise(0).alias('trafficTimeBins')
                                              )\
                                       .filter((taxi_df.tripTimeSecs >= 30) & (taxi_df.tripTimeSecs <= 7200))
```

## <a name="create-a-logistic-regression-model"></a>Crear un modelo de regresión logística

La última tarea consiste en convertir los datos etiquetados a un formato que se pueda analizar mediante la regresión logística. La entrada a un algoritmo de regresión logística debe ser un conjunto de *pares de vector de etiqueta-característica*, donde el *vector de característica* es un vector de números que representa el punto de entrada. 

Por lo tanto, es necesario convertir las columnas de categorías en números. En concreto, debe convertir las columnas `trafficTimeBins` y `weekdayString` en representaciones de enteros. Hay varios enfoques para realizar la conversión. En el ejemplo siguiente, se adopta el enfoque de `OneHotEncoder`, que es común.

```python
# Because the sample uses an algorithm that works only with numeric features, convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new DataFrame that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Esta acción da como resultado un nuevo marco de datos con todas las columnas en el formato correcto para entrenar un modelo.

## <a name="train-a-logistic-regression-model"></a>Entrenamiento de un modelo de regresión logística

La primera tarea consiste en dividir el conjunto de filas en un conjunto de entrenamiento y un conjunto de pruebas o validación. La división aquí es arbitraria. Experimente con diferentes configuraciones de división para ver si afectan al modelo.

```python
# Decide on the split between training and testing data from the DataFrame
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the DataFrame into test and training DataFrames
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Ahora que hay dos marcos de datos, la siguiente tarea consiste en crear la fórmula del modelo y ejecutarla en el marco de datos de entrenamiento. Después, puede realizar la validación en la trama de datos de la prueba. Experimente con versiones diferentes de la fórmula del modelo para ver el impacto de las distintas combinaciones.

> [!Note]
> Para guardar el modelo, asigne el rol de *colaborador de datos de Storage Blob* al ámbito del recurso de servidor de Azure SQL Database. Para acceder a los pasos detallados, vea [Asignación de roles de Azure mediante Azure Portal](../../role-based-access-control/role-assignments-portal.md). Solo los miembros con el privilegio de propietario pueden realizar este paso.

```python
## Create a new logistic regression object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create a logistic regression model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional, but it's another form of inter-session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset; evaluation using area under ROC
predictions = lrModel.transform(test_data_df)
predictionAndLabels = predictions.select("label","prediction").rdd
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)
```

La salida de esta celda es:

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>Creación de una representación visual de la predicción

Ahora se puede crear una visualización final para facilitar el análisis de los resultados de esta prueba. Una [curva ROC](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) es una manera de revisar el resultado.

```python
## Plot the ROC curve; no need for pandas, because this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![Gráfico que muestra la curva ROC para la regresión logística en el modelo de propina.](./media/apache-spark-machine-learning-mllib-notebook/nyc-taxi-roc.png)

## <a name="shut-down-the-spark-instance"></a>Apagado de la instancia de Spark

Después de finalizar la ejecución de la aplicación, cierre el cuaderno para liberar los recursos cerrando la pestaña. O bien, seleccione **Finalizar sesión** en el panel de estado en la parte inferior del cuaderno.

## <a name="see-also"></a>Consulte también

- [Información general: Apache Spark en Azure Synapse Analytics](apache-spark-overview.md)

## <a name="next-steps"></a>Pasos siguientes

- [Documentación de .NET para Apache Spark](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)
- [Documentación oficial de Apache Spark](https://spark.apache.org/docs/2.4.5/)

>[!NOTE]
> Parte de la documentación oficial de Apache Spark se basa en el uso de la consola de Spark, que no está disponible en Apache Spark en Azure Synapse Analytics. En su lugar, use las experiencias de [cuaderno](../quickstart-apache-spark-notebook.md) o [IntelliJ](../spark/intellij-tool-synapse.md).