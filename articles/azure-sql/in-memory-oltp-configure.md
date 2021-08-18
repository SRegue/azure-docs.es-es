---
title: OLTP en memoria mejora el rendimiento de transacciones SQL
description: Adopte OLTP en memoria para mejorar el rendimiento transaccional en una base de datos SQL ya existente en Azure SQL Database e Instancia administrada de Azure SQL.
services: sql-database
ms.service: sql-database
ms.custom: sqldbrb=2
ms.subservice: performance
ms.topic: how-to
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: mathoma
ms.date: 11/07/2018
ms.openlocfilehash: 2d1e00059948b6b3347c41910f8c1f75d9635da5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752214"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-azure-sql-database-and-azure-sql-managed-instance"></a>Uso de OLTP en memoria para mejorar el rendimiento de aplicación en Azure SQL Database e Instancia administrada de Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

[OLTP en memoria](in-memory-oltp-overview.md) puede utilizarse para mejorar el rendimiento del procesamiento de transacciones, la ingesta de datos y los escenarios de datos transitorios, en bases de datos [de los planes Premium y Crítico para la empresa](database/service-tiers-vcore.md) sin aumentar el plan de tarifa.

> [!NOTE]
> Más información sobre cómo [Quorum duplica cargas de trabajo clave de las bases de datos a la vez que reduce las DTU en un 70 % con Azure SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

Siga estos pasos para adoptar In-Memory OLTP en la base de datos existente.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>Paso 1: Asegúrese de que está utilizando una base de datos de nivel Prémium o Crítico para la empresa

OLTP en memoria solo se admite en bases de datos de nivel Premium o Crítico para la empresa. Se admite In-Memory si el resultado devuelto es 1 (no 0):

```sql
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* son las siglas de *Extreme Transaction Processing*.

## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Paso 2: Identifique los objetos para migrar a OLTP en memoria

SSMS incluye un informe de **información general del análisis de rendimiento de transacciones** que se pueden ejecutar en una base de datos con una carga de trabajo activo. El informe identifica las tablas y los procedimientos almacenados que son candidatos para la migración a In-Memory OLTP.

En SSMS, para generar el informe:

* En el **Explorador de objetos**, haga clic con el botón derecho en el nodo de la base de datos.
* Haga clic en **Informes** > **Informes estándar** > **Información general de análisis de rendimiento de transacciones**.

Para obtener más información, consulte [Determinar si una tabla o un procedimiento almacenado se debe pasar a In-Memory OLTP](/sql/relational-databases/in-memory-oltp/determining-if-a-table-or-stored-procedure-should-be-ported-to-in-memory-oltp).

## <a name="step-3-create-a-comparable-test-database"></a>Paso 3: Cree una base de datos de prueba comparable

Supongamos que el informe indica que la base de datos tiene una tabla que se beneficiaría de convertirse en una tabla optimizada para memoria. Se recomienda que la pruebe primero para confirmar la indicación.

Necesitará una copia de prueba de la base de datos de producción. La base de datos de prueba debe estar en el mismo nivel de servicio que la base de datos de producción.

Para facilitar las pruebas, ajuste la base de datos de prueba de la forma siguiente:

1. Conéctese a la base de datos de prueba con SSMS.
2. Para evitar la necesidad de usar la opción WITH (SNAPSHOT) en las consultas, establezca la opción de base de datos tal como se muestra en la siguiente instrucción T-SQL:

   ```sql
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Paso 4: Migre las tablas

Debe crear y rellenar una copia optimizada para memoria de la tabla que desea probar. Se puede crear mediante:

* El práctico Asistente para optimización de memoria en SSMS.
* T-SQL manual.

### <a name="memory-optimization-wizard-in-ssms"></a>Asistente para optimización de memoria en SSMS

Para usar esta opción de migración:

1. Conéctese a la base de datos de prueba con SSMS.
2. En el **Explorador de objetos**, haga clic con el botón derecho en la tabla y después haga clic en **Asistente de optimización de memoria**.

   Se muestra el asistente **Asesor del optimizador de memoria de tablas** .
3. En el asistente, haga clic en **Validación de migración** (o en el botón **Siguiente**) para ver si la tabla tiene las características no admitidas en las tablas optimizadas para memoria. Para más información, consulte:

   * La *lista de comprobación de optimización de memoria* en [Asesor de optimización de memoria](/sql/relational-databases/in-memory-oltp/memory-optimization-advisor).
   * [Construcciones de transact-SQL no admitidas por In-Memory OLTP](/sql/relational-databases/in-memory-oltp/transact-sql-constructs-not-supported-by-in-memory-oltp).
   * [Migración a In-Memory OLTP](/sql/relational-databases/in-memory-oltp/plan-your-adoption-of-in-memory-oltp-features-in-sql-server).
4. Si la tabla no tiene características no admitidas, el asesor puede realizar el esquema real y la migración de datos.

### <a name="manual-t-sql"></a>T-SQL manual

Para usar esta opción de migración:

1. Conéctese a la base de datos de prueba mediante SSMS (o una utilidad similar).
2. Obtenga el script T-SQL completo para la tabla y sus índices.

   * En SSMS, haga clic con el botón derecho en el nodo de tabla.
   * Haga clic en **Incluir tabla como** > **Crear en** > **Nueva ventana de consulta**.
3. En la ventana de script, agregue WITH (MEMORY_OPTIMIZED = ON) a la instrucción CREATE TABLE.
4. Si hay un índice CLUSTERD, cámbielo a NONCLUSTERED.
5. Cambie el nombre de la tabla existente mediante SP_RENAME.
6. Cree la nueva copia de la tabla optimizada para memoria mediante la ejecución del script CREATE TABLE editado.
7. Copie los datos en la tabla optimizada en memoria mediante INSERT... SELECT * INTO:

```sql
INSERT INTO [<new_memory_optimized_table>]
        SELECT * FROM [<old_disk_based_table>];
```

## <a name="step-5-optional-migrate-stored-procedures"></a>Paso 5 (opcional): Migre los procedimientos almacenados

La característica In-Memory también puede modificar un procedimiento almacenado para mejorar el rendimiento.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Consideraciones con procedimientos almacenados compilados de forma nativa

Un procedimiento almacenado compilado de forma nativa debe tener las siguientes opciones en su cláusula T-SQL WITH:

* NATIVE_COMPILATION
* SCHEMABINDING: son las tablas cuyas definiciones de columna no puede cambiar de ninguna forma el procedimiento almacenado que pueda afectar al procedimiento almacenado, a menos que quite el procedimiento almacenado.

Un módulo nativo debe usar un gran [bloque ATOMIC](/sql/relational-databases/in-memory-oltp/atomic-blocks-in-native-procedures) para la administración de transacciones. No hay ningún rol para una instrucción BEGIN TRANSACTION o ROLLBACK TRANSACTION explícita. Si el código detecta una infracción de una regla de negocio, puede finalizar el bloque ATOMIC con una instrucción [THROW](/sql/t-sql/language-elements/throw-transact-sql) .

### <a name="typical-create-procedure-for-natively-compiled"></a>CREATE PROCEDURE típico para compilar de forma nativa

Normalmente el T-SQL para crear un procedimiento almacenado compilado de forma nativa es similar a la siguiente plantilla:

```sql
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

* Para TRANSACTION_ISOLATION_LEVEL, SNAPSHOT es el valor más común para el procedimiento almacenado compilado de forma nativa. Sin embargo, también se admite un subconjunto de los demás valores:
  
  * REPEATABLE READ
  * SERIALIZABLE
* El valor LANGUAGE debe estar presente en la vista sys.languages.

### <a name="how-to-migrate-a-stored-procedure"></a>Migración de un procedimiento almacenado

Los pasos de migración son los siguientes:

1. Obtenga el script CREATE PROCEDURE para el procedimiento almacenado regular interpretado.
2. Vuelva a escribir el encabezado para que coincida con la plantilla anterior.
3. Determine si el código T-SQL del procedimiento almacenado usa las características que no se admiten para los procedimientos almacenados compilados de forma nativa. Implemente soluciones alternativas si es necesario.

   Para obtener información detallada, consulte [Problemas de migración para los procedimientos almacenados compilados de forma nativa](/sql/relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables).
4. Cambie el nombre del procedimiento almacenado anterior por SP_RENAME. O bien, simplemente quítelo con la instrucción DROP.
5. Ejecute el script CREATE PROCEDURE T-SQL editado.

## <a name="step-6-run-your-workload-in-test"></a>Paso 6: Ejecute la carga de trabajo en la prueba

Ejecutar una carga de trabajo en la base de datos de prueba es similar a la carga de trabajo que se ejecuta en la base de datos de producción. Esto debería mostrar la mejora del rendimiento conseguida mediante el uso de la característica In-Memory para tablas y procedimientos almacenados.

Los atributos principales de la carga de trabajo son los siguientes:

* Número de conexiones simultáneas.
* Relación de lectura/escritura.

Para personalizar y ejecutar la carga de trabajo de prueba, considere el uso de la práctica herramienta `ostress.exe`, que se muestra en este artículo de [tecnologías en memoria](in-memory-oltp-overview.md).

Para minimizar la latencia de red, ejecute la prueba en la misma región geográfica de Azure donde existe la base de datos.

## <a name="step-7-post-implementation-monitoring"></a>Paso 7: Supervise después de la implementación

Considere la posibilidad de supervisar los efectos de rendimiento de las implementaciones In-Memory en producción:

* [Supervisión del almacenamiento de In-Memory](in-memory-oltp-monitor-space.md).
* [Supervisión mediante vistas de administración dinámica](database/monitoring-with-dmvs.md)

## <a name="related-links"></a>Vínculos relacionados

* [In-Memory OLTP (optimización In-Memory)](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
* [Introducción a los procedimientos almacenados compilados de forma nativa](/sql/relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables)
* [Asesor de optimización en memoria](/sql/relational-databases/in-memory-oltp/memory-optimization-advisor)
