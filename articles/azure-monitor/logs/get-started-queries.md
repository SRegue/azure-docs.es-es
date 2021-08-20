---
title: Introducción a las consultas de registro en Azure Monitor | Microsoft Docs
description: En este artículo se proporciona un tutorial de introducción a la escritura de consultas de registro en Azure Monitor.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/24/2019
ms.openlocfilehash: ff65a473391f8a2435b20b88cacb6a0e18b90ba3
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113360747"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>Introducción a las consultas de registro en Azure Monitor

> [!NOTE]
> Puede trabajar en este ejercicio en su propio entorno si va a recopilar datos de al menos una máquina virtual. Si no es así, use nuestro [entorno de demostración](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade), que incluye gran cantidad de datos de ejemplo.  Si ya sabe cómo realizar consultas en KQL, pero solo necesita crear rápidamente consultas útiles basadas en tipos de recursos, vea el [panel de consultas de ejemplo guardado](../logs/queries.md).

En este tutorial aprenderá a escribir consultas de registro en Azure Monitor. Le mostrará cómo:

- Comprender la estructura de las consultas
- Ordenar los resultados de la consulta
- Filtrar los resultados de la consulta
- Especificar un intervalo de tiempo
- Seleccionar los campos que incluir en los resultados
- Definir y usar campos personalizados
- Agregar y agrupar los resultados

Para obtener un tutorial sobre el uso de Log Analytics en Azure Portal, consulte [Introducción a Log Analytics de Azure Monitor](./log-analytics-tutorial.md).<br>
Para más información sobre las consultas de registro en Azure Monitor, consulte [Introducción a las consultas de registro en Azure Monitor](../logs/log-query-overview.md).

Siga con una versión en vídeo de este tutorial:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE42pGX]

## <a name="writing-a-new-query"></a>Escribir una nueva consulta

Las consultas pueden comenzar por un nombre de tabla o el comando *search*. Debe empezar por un nombre de tabla, ya que define un ámbito claro para la consulta y mejora el rendimiento de las consultas y la pertinencia de los resultados.

> [!NOTE]
> El lenguaje de consulta de Kusto que usa Azure Monitor distingue entre mayúsculas y minúsculas. Las palabras clave del lenguaje se suelen escribir en minúsculas. Al usar nombres de tablas o columnas en una consulta, asegúrese de usar las mayúsculas y minúsculas correctas, tal como se muestra en el panel de esquema.

### <a name="table-based-queries"></a>Consultas basadas en tablas

Azure Monitor organiza los datos de registro en tablas, compuestas de varias columnas. Todas las tablas y columnas se muestran en el panel de esquema en Log Analytics en el portal de Analytics. Identifique una tabla que le interese y observe algunos datos:

```Kusto
SecurityEvent
| take 10
```

La consulta anterior devuelve 10 resultados de la tabla *SecurityEvent*, sin orden específico. Se trata de una forma muy común para echar un vistazo a una tabla y comprender su estructura y contenido. Vamos a examinar su estructura:

* La consulta comienza por el nombre de la tabla, *SecurityEvent*; esta parte define el ámbito de la consulta.
* El carácter de barra vertical (|) separa los comandos,de manera que la salida del primero es la entrada del siguiente. Puede agregar cualquier cantidad de elementos canalizados.
* Después de la canalización se encuentra el comando **take**, que devuelve un número concreto de registros arbitrarios de la tabla.

En realidad podríamos ejecutar la consulta incluso sin agregar `| take 10`, seguiría siendo válida, pero podría devolver hasta 10 000 resultados.

### <a name="search-queries"></a>Consultas de búsqueda

Las consultas de búsqueda están menos estructuradas y por lo general son más adecuadas para buscar registros con un valor específico en cualquiera de sus columnas:

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

Esta consulta busca en la tabla *SecurityEvent* los registros que contienen la expresión "Cryptographic". De esos registros, se devuelven y se muestran 10. Si se omite la parte `in (SecurityEvent)` y solo se ejecuta `search "Cryptographic"`, la búsqueda recorrerá *todas* las tablas, lo que podría tardar más tiempo y ser menos eficiente.

> [!WARNING]
> Las consultas de búsqueda son normalmente más lentas que las consultas basadas en tablas porque tienen que procesar más datos. 

## <a name="sort-and-top"></a>sort y top
Mientras que **take** resulta útil para obtener algunos registros, los resultados no se seleccionan ni se muestran en un orden concreto. Para obtener una vista ordenada a partir de la columna preferida, use **sort**:

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

No obstante, esto podría devolver demasiados resultados y tardar un tiempo. En la consulta anterior se ordena *toda* la tabla SecurityEvent a partir de la columna TimeGenerated. En el portal de Analytics se limita la presentación para mostrar solo 10 000 registros. Es obvio que este enfoque no es el mejor.

La mejor manera de obtener solo los 10 registros más recientes es usar **top**, que ordena toda la tabla en el servidor y devuelve los registros principales:

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

El criterio de ordenación descendente es el valor predeterminado, por lo que se suele omitir el argumento **desc**. El resultado tendrá un aspecto similar al siguiente:

![Los 10 principales](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>where: filtrado por una condición
Los filtros, tal como indica su nombre, filtran los datos por una condición específica. Se trata de la manera más común para limitar los resultados de la consulta a la información pertinente.

Para agregar un filtro a una consulta, use el operador **where** seguido por una o varias condiciones. Por ejemplo, la siguiente consulta devuelve solo los registros de *SecurityEvent* donde _Level_ sea igual a _8_:

```Kusto
SecurityEvent
| where Level == 8
```

Al escribir las condiciones de filtro, puede usar las siguientes expresiones:

| Expression | Descripción | Ejemplo |
|:---|:---|:---|
| == | Coincidencia con igualdad<br>(distingue mayúsculas y minúsculas) | `Level == 8` |
| =~ | Coincidencia con igualdad<br>(no distingue mayúsculas y minúsculas) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=, <> | Coincidencia sin igualdad<br>(ambas expresiones son idénticas) | `Level != 4` |
| *and*, *or* | Necesario entre condiciones| `Level == 16 or CommandLine != ""` |

Para filtrar con varias condiciones, puede usar **and**:

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

o canalizar varios elementos **where** uno tras otro:

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> Los valores pueden ser de tipos distintos, por lo que quizá deba convertirlos para realizar la comparación en el tipo correcto. Por ejemplo, la columna *Level* de SecurityEvent es de tipo cadena, por lo que debe convertirla a un tipo numérico, como *int* o *long* para usar operadores numéricos: `SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Especificar un intervalo de tiempo

### <a name="time-picker"></a>Selector de hora

El selector de hora está junto al botón Ejecutar e indica que solo consultamos registros de las últimas 24 horas. Este es el intervalo de tiempo predeterminado que se aplica a todas las consultas. Para obtener solo los registros de la última hora, seleccione _Last hour_ (Última hora) y vuelva a ejecutar la consulta.

![Selector de hora](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>Filtro de tiempo en las consultas

También puede definir su propio intervalo de tiempo mediante la incorporación de un filtro de tiempo a la consulta. Lo mejor es colocar el filtro de tiempo inmediatamente después del nombre de la tabla: 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

En el filtro de tiempo anterior, `ago(30m)` significa "hace 30 minutos", por lo que esta consulta devuelve solo los registros de los últimos 30 minutos. Otras unidades de tiempo son los días (2 d), los minutos (25 m) y los segundos (10 s).


## <a name="project-and-extend-select-and-compute-columns"></a>project y extend: seleccionar y procesar columnas

Use **project** para seleccionar columnas concretas que incluir en los resultados:

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

En el ejemplo anterior se genera esta salida:

![Resultados del proyecto de consulta](media/get-started-queries/project.png)

También puede usar **project** para cambiar el nombre de las columnas y definir otras nuevas. En el ejemplo siguiente se utiliza project para hacer lo siguiente:

* Seleccionar solo las columnas originales *Computer* y *TimeGenerated*.
* Mostrar la columna *Activity* como *EventDetails*.
* Crear una nueva columna denominada *EventCode*. La función **substring()** se utiliza para obtener solo los primeros cuatro caracteres del campo Activity.


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

**extend** mantiene todas las columnas originales en el conjunto de resultados y define otras adicionales. La consulta siguiente usa **extend** para agregar la columna *EventCode*. Tenga en cuenta que es posible que esta columna no se muestre al final de los resultados de la tabla, en cuyo caso sería necesario expandir los detalles de un registro para verla.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="summarize-aggregate-groups-of-rows"></a>summarize: incorporación de grupos de filas
Use **summarize** para identificar grupos de registros, según una o varias columnas, y aplicarles agregaciones. El uso más habitual de **summarize** es con *count*, lo que devuelve el número de resultados de cada grupo.

En la consulta siguiente se revisan todos los registros *Perf* de la última hora, se agrupan por *ObjectName* y se cuentan los registros de cada grupo: 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

A veces tiene sentido definir los grupos según varias dimensiones. Cada combinación única de estos valores define un grupo independiente:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

Otro uso común es para realizar cálculos matemáticos o estadísticos en cada grupo. Por ejemplo, a continuación se calcula el promedio de *CounterValue* para cada equipo:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Lamentablemente, los resultados de esta consulta no tienen sentido, ya que hemos mezclado diferentes contadores de rendimiento. Para que esto tenga más sentido, debemos calcular el promedio por separado para cada combinación de *CounterName* y *Computer*:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Resumen por una columna de tiempo
La agrupación de resultados también puede basarse en una columna de tiempo o con cualquier otro valor continuo. Sin embargo, simplemente resumir por `by TimeGenerated` crearía grupos para cada milisegundo del intervalo de tiempo, ya que son valores únicos. 

Para crear grupos basados en valores continuos es mejor dividir el intervalo en unidades manejables mediante **bin**. En la siguiente consulta se analizan los registros *Perf* que miden la memoria libre (*Available MBytes*) en un equipo específico. Se calcula el valor promedio de cada período de 1 hora durante los últimos 7 días:

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

Para que la salida sea más clara, elija mostrarla como gráfico de tiempo, que muestre la memoria disponible a lo largo del tiempo:

![Consulta de memoria a lo largo del tiempo](media/get-started-queries/chart.png)



## <a name="next-steps"></a>Pasos siguientes

- Puede encontrar más información sobre el uso de datos de cadena en una consulta de registro en [Trabajo con cadenas en las consultas de registro de Azure Monitor](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations).
- Puede encontrar más información sobre la agregación de datos en una consulta de registro en [Agregaciones avanzadas en las consultas de registro de Azure Monitor](/azure/data-explorer/write-queries#advanced-aggregations).
- Obtenga información sobre cómo combinar datos de varias tablas en [Combinaciones en consultas de registros de Azure Monitor](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins).
- Obtenga la documentación completa sobre el lenguaje de consulta de Kusto en [Referencia del lenguaje KQL](/azure/kusto/query/).
