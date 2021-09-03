---
title: 'ML Studio (clásico): consumo de un servicio web en Excel con Azure'
description: Machine Learning Studio (clásico) facilita la llamada a servicios web directamente desde Excel sin necesidad de escribir código.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: b5394302d3b1028ff6746e9a655028fdb82d9142
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695024"
---
# <a name="consuming-an-machine-learning-studio-classic-web-service-from-excel"></a>Consumo de un servicio web de Machine Learning Studio (clásico) desde Excel

**SE APLICA A:**  ![Se aplica a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (clásico)   ![No se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

[!INCLUDE [ML Studio (classic) retirement](../../../includes/machine-learning-studio-classic-deprecation.md)]

Machine Learning Studio (clásico) facilita la llamada a servicios web directamente desde Excel sin necesidad de escribir código.

Si utiliza Excel 2013 (o posterior) o Excel Online, le recomendamos que use el [complemento de Excel](excel-add-in-for-web-services.md).

## <a name="steps"></a>Pasos
Publicar un servicio web. [Tutorial 3: Deploy credit risk model](tutorial-part3-credit-risk-deploy.md) (Implementar el modelo de riesgo de crédito) se explica cómo hacerlo. Actualmente, la función de libro de Excel solo se admite para los servicios de solicitud/respuesta que tienen una salida única (es decir, una etiqueta de puntuación única). 

Una vez que tenga un servicio web, haga clic en la sección **SERVICIOS WEB** que se encuentra a la izquierda del estudio y, luego, seleccione el servicio web que se va a usar desde Excel.

**Servicio web clásico**

1. En la pestaña **PANEL** del servicio web se encuentra una fila para el servicio **SOLICITUD/RESPUESTA**. Si este servicio tenía una salida única, deberá consultar el vínculo **Descargar el libro de Excel** de esa fila.

    ![Descarga del libro de Excel mediante el portal de servicio web de Studio (clásico)](./media/consuming-from-excel/excellink.png)
2. Haga clic en **Descargar el libro de Excel**.

**Servicio web nuevo**

1. En el portal de servicios web de Machine Learning, seleccione **Consume** (Consumo).
2. En la página de consumo, en la sección **Web service consumption options** (Opciones de consumo del servicio web), haga clic en el icono de Excel.

**Uso del libro**

1. Abra el libro.
2. Aparecerá una advertencia de seguridad; haga clic en el botón **Habilitar edición** .

    ![Habilitar la edición para quitar la advertencia de seguridad de la vista protegida](./media/consuming-from-excel/enableeditting.png)
3. Aparecerá una advertencia de seguridad. Haga clic en el botón **Habilitar contenido** para ejecutar macros en la hoja de cálculo.

    ![Habilitar el contenido para descartar la advertencia de seguridad que deshabilita las macros](./media/consuming-from-excel/enablecontent.png)
4. Una vez que las macros están habilitadas, se generará una tabla. Las columnas en azul son necesarias como entrada al servicio web RRS, o como **PARÁMETROS**. Tenga en cuenta la salida del servicio RRS, los **VALORES PREDICHOS** en verde. Cuando se llenan todas las columnas de una fila determinada, el libro llama a la API de puntuación automáticamente y muestra los resultados con puntuación.

    ![Tabla para entradas de parámetro y valores predichos resultantes](./media/consuming-from-excel/sampletable.png)
5. Para puntuar más de una fila, rellene la segunda fila con datos y se generarán los valores predichos. Incluso puede pegar varias filas a la vez.

Puede utilizar cualquiera de las funciones de Excel (gráficos, asignación de energía, formato condicional, etc.) con los valores de predicción como ayuda para visualizar los datos.

## <a name="sharing-your-workbook"></a>Compartir el libro
Para que las macros funcionen, la clave de API debe formar parte de la hoja de cálculo. Esto significa que debe compartir el libro con las entidades e individuos de su confianza.

## <a name="automatic-updates"></a>Actualizaciones automáticas
En las siguientes dos situaciones se realiza una llamada a RRS:

1. La primera vez que una fila tiene contenido en todos sus **PARÁMETROS**
2. Cada vez que cualquiera de los **PARÁMETROS** cambia en una fila en la que se habían especificado todos sus **PARÁMETROS**.