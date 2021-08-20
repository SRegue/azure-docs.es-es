---
title: Examen de la factura de una suscripción individual a Azure
description: Aprenda a interpretar la factura y el uso de los recursos y a comprobar los cargos de su suscripción de Azure, incluido el pago por uso.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: tutorial
ms.date: 05/17/2021
ms.author: banders
ms.custom: contperf-fy21q2
ms.openlocfilehash: a8eb9ec2b71495011dfa7ebe9dbf1dcf8cd5d19e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449424"
---
# <a name="tutorial-review-your-individual-azure-subscription-bill"></a>Tutorial: Examen de la factura de una suscripción individual a Azure

Este artículo le ayuda a comprender y a examinar la factura de su suscripción de pago por uso o a Visual Studio Azure, que incluye pago por uso y Visual Studio. Lo normal es recibir una factura por email de cada período de facturación. Esta es una representación de su factura de Azure. En Azure Portal está disponible la misma información sobre los costos de la factura. En este tutorial se compara la factura con el archivo de uso diario detallado y con el análisis de costos de Azure Portal.

Este tutorial se aplica solo a los clientes de Azure con una suscripción individual. Las suscripciones individuales comunes son aquellas con las tarifas de pago por uso compradas directamente desde el sitio web de Azure.

Si necesita ayuda para comprender los cargos inesperados, consulte [Análisis de cargos inesperados](analyze-unexpected-charges.md). O bien, si necesita cancelar su suscripción a Azure, consulte [Cancelación de la suscripción a Azure](../manage/cancel-azure-subscription.md).

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Comparación de los cargos facturados con el archivo de uso
> * Comparación de los cargos y el uso en el análisis de costos

## <a name="prerequisites"></a>Requisitos previos

Debe tener una cuenta de facturación de pago del *Programa de Microsoft Online Services*. La cuenta se crea al registrarse en Azure mediante el sitio web de Azure. Por ejemplo, si tiene una cuenta con tarifas de pago por uso o es suscriptor de Visual Studio.

Las facturas para las cuentas gratuitas de Azure se crean solo cuando se supera el importe de crédito mensual.

Deben haber transcurrido más de 30 días desde el momento de suscripción a Azure. Azure le cobra al final del período de facturación.

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

- Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

## <a name="compare-billed-charges-with-your-usage-file"></a>Comparación de los cargos facturados con su archivo de uso

<a name="charges"></a>

El primer paso para comparar el uso y los costos es descargar la factura y los archivos de uso. El archivo CSV de uso detallado proporciona los cargos por período de facturación y el uso diario. No incluye información fiscal. Para descargar los archivos, debe ser administrador de la cuenta o tener el rol de propietario.

En Azure Portal, escriba *suscripciones* en el cuadro de búsqueda y haga clic en **Suscripciones**.

[![Vaya a Suscripciones](./media/review-individual-bill/navigate-subscriptions.png)](./media/review-individual-bill/navigate-subscriptions.png#lightbox)

En la lista de suscripciones, haga clic en la suscripción.

En **Facturación**, seleccione **Facturas**.

En la lista de facturas, busque la que desea descargar y haga clic en el símbolo de descarga. Para visualizar facturas anteriores, quizá deba cambiar el intervalo de tiempo. La generación del archivo de detalles de uso y la factura puede tardar unos minutos.

![Captura de pantalla que muestra los períodos de facturación, la opción de descarga y los cargos totales para cada período de facturación](./media/review-individual-bill/download-invoice.png)

En la ventana Descargar uso y cargos, haga clic en **Descargar CSV** y **Descargar factura**.

![Captura de pantalla que muestra la página Descargar factura y uso](./media/review-individual-bill/usageandinvoice.png)

Si aparece el mensaje **No está disponible**, hay varias razones para que no vea detalles de uso o una factura:

- Han transcurrido menos de 30 días desde el día que se suscribió a Azure.
- No hay uso para el período de facturación.
- La factura no se ha generado aún. Espere hasta el final del período de facturación.
- No tiene permiso para ver facturas. A menos que sea administrador de cuenta, no verá las facturas anteriores.
- Si tiene una evaluación gratuita o una cantidad de crédito mensual con la suscripción que no ha superado, no obtendrá una factura a menos que tenga un contrato de cliente de Microsoft.

Ahora puede revisar los cargos. En la factura se muestran valores para los impuestos y los gastos de uso.

![Ejemplo de factura de Azure](./media/review-individual-bill/invoice-usage-charge.png)

Abra el archivo de uso de CSV que ha descargado. Al final del archivo, sume el valor de todos los elementos de la columna *Costo*.

![Ejemplo de archivo de uso con los costos sumados](./media/review-individual-bill/usage-file-usage-charges.png)

 La suma de los valores de *Costo* debe coincidir exactamente con los costos por *gastos de uso* de la factura.

Los cargos de uso se muestran en el nivel de medidor. Los siguientes términos significan lo mismo en la factura y en el archivo de uso detallado. Por ejemplo, el ciclo de facturación en la factura es equivalente al período de facturación que se muestra en el archivo de uso detallado.

| Factura (PDF) | Uso detallado (CSV)|
| --- | --- |
|Ciclo de facturación | BillingPeriodStartDate BillingPeriodEndDate |
|Nombre |Categoría de medidor |
|Tipo |Subcategoría de medidor |
|Recurso |MeterName |
|Region |MeterRegion |
|Consumida | Cantidad |
|Se incluye |Cantidad incluida |
|Facturable |Cantidad de superávit |
|Tarifa | EffectivePrice|
| Value | Coste |

La sección de **cargos de uso** de la factura muestra el valor total (costo) de cada medidor que se consumió durante el período de facturación. Por ejemplo, en la imagen siguiente se muestra un gasto de uso del servicio Azure Storage para el recurso *P10 Disks*.

![Cargos de uso de la factura](./media/review-individual-bill/invoice-usage-charges.png)

En el archivo de uso de CSV, filtre por *MeterName* para el recurso correspondiente que se muestra en la factura. A continuación, sume el valor de *Costo* de los elementos de la columna. Este es un ejemplo que se centra en el nombre del medidor (discos P10) que corresponde al mismo elemento de línea de la factura.

Para conciliar los cargos de compra de reserva, en el archivo CSV de uso, filtre por *ChargeType* como Compra, y se mostrarán todos los cargos de compras de reserva del mes. Puede comparar estos cargos mediante la observación de *MeterName* y *MeterSubCategory* en el archivo de uso con Recurso y Tipo respectivamente en la factura.

![Suma de los valores del archivo de uso para MeterName](./media/review-individual-bill/usage-file-usage-charge-resource.png)

La suma de los valores de *Costo* debe coincidir exactamente con el costo de los *gastos de uso* del recurso individual que se cobra en la factura.

## <a name="compare-billed-charges-and-usage-in-cost-analysis"></a>Comparación de los cargos facturados y el uso en el análisis de costos

El análisis de los costos en Azure Portal también puede ayudarle a comprobar los cargos. Para obtener una visión general rápida del uso y los cargos facturados, seleccione su suscripción en la página Suscripciones de Azure Portal. Después, haga clic en **Análisis de costos** y, en la lista de vistas, haga clic en **Detalles de la factura**.

![Ejemplo que muestra la selección de Detalles de la factura](./media/review-individual-bill/cost-analysis-select-invoice-details.png)

A continuación, en la lista de intervalos de fechas, seleccione un período de tiempo para la factura. Agregue un filtro para el número de factura y seleccione el valor de InvoiceNumber que coincida con el de la factura. En Análisis de costos se muestran los detalles de costo de los elementos facturados.

![Ejemplo que muestra los detalles de los costos facturados en el análisis de costos](./media/review-individual-bill/cost-analysis-service-usage-charges.png)

Los costos que aparecen en el análisis de costos deben coincidir exactamente con el costo de los *gastos de uso* del recurso individual que se cobra en la factura.

![Cargos de uso de la factura](./media/review-individual-bill/invoice-usage-charges.png)

## <a name="external-marketplace-services"></a>Servicios de externos de Marketplace

<a name="external"></a>

Los servicios externos, o cargos de Azure Marketplace, son para aquellos recursos creados por proveedores de software de otros fabricantes. Estos recursos están disponibles para su uso desde Azure Marketplace. Por ejemplo, un firewall Barracuda es un recurso de Azure Marketplace que ofrecen otros fabricantes. Todos los cargos por el firewall y sus medidores correspondientes aparecerán como cargos por servicios externos.

Los cargos por servicios externos aparecen en una factura independiente.

### <a name="resources-are-billed-by-usage-meters"></a>Los recursos se facturan por medidores de uso

Azure no factura directamente en función del costo del recurso. Los gastos de un recurso se calculan mediante uno o varios medidores. Los medidores se usan para realizar un seguimiento del uso de un recurso durante su vigencia. Estos medidores se usan entonces para calcular la factura.

Cuando se crea un único recurso de Azure, como una máquina virtual, se crean una o varias instancias de medidores. Los medidores se utilizan para realizar el seguimiento de la utilización del recurso con el tiempo. Cada medidor emite registros de uso que Azure utiliza para calcular la factura.

Por ejemplo, una sola máquina virtual creada en Azure puede tener los siguientes medidores creados para realizar el seguimiento de su uso:

- Horas de proceso
- Horas de dirección IP
- Transferencia de datos de entrada
- Transferencia de datos de salida
- Disco administrado Estándar
- Operaciones de disco administrado estándar
- E/S estándar: disco
- E/S estándar: lectura de blobs en bloques
- E/S estándar: escritura de blobs en bloques
- E/S estándar: Eliminación de blob en bloques

Cuando se crea la máquina virtual, cada medidor comienza a emitir registros de uso. Este uso y el precio del medidor se registra en el sistema de medición de Azure.

Puede ver los medidores que se usaron para calcular la factura en el archivo CSV de uso, como en el ejemplo anterior.

## <a name="pay-your-bill"></a>Pagar la factura

<a name="payment"></a>

Si ha configurado una tarjeta de crédito como forma de pago, el pago se cargará automáticamente en un plazo de diez días después de finalizar el período de facturación. En el extracto de la tarjeta de crédito, el elemento de línea diría **MSFT Azure**.

Para cambiar la tarjeta de crédito o débito en la que se efectuará el cobro, consulte [Agregar, actualizar o quitar una tarjeta de crédito o débito para Azure](../manage/change-credit-card.md).

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a:

> [!div class="checklist"]
> * Comparación de los cargos facturados con el archivo de uso
> * Comparación de los cargos y el uso en el análisis de costos

Complete el inicio rápido para empezar a usar el análisis de costos.

> [!div class="nextstepaction"]
> [Inicio del análisis de costos](../costs/quick-acm-cost-analysis.md)
