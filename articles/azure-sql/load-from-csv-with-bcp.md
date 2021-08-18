---
title: Carga de datos desde un archivo .csv en una base de datos (bcp)
description: Para un tamaño de datos pequeño, utiliza bcp para importar datos en Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: dzsquared
ms.author: drskwier
ms.reviewer: mathoma
ms.date: 01/25/2019
ms.openlocfilehash: 6fe0fdf53235ce1ec42a2b46e95291c111ed2dff
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743692"
---
# <a name="load-data-from-csv-into-azure-sql-database-or-sql-managed-instance-flat-files"></a>Carga de datos desde CSV en Azure SQL Database o Instancia administrada de SQL (archivos planos)
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Puede utilizar la herramienta de línea de comandos bcp para importar datos desde un archivo CSV a Azure SQL Database o Instancia administrada de Azure SQL.

## <a name="before-you-begin"></a>Antes de empezar

### <a name="prerequisites"></a>Requisitos previos

Para completar los pasos de este artículo, necesitará lo siguiente:

* Una base de datos de Azure SQL Database
* La utilidad de línea de comandos bcp instalada
* La utilidad de línea de comandos sqlcmd instalada

Puede descargar las utilidades bcp y sqlcmd de la [documentación de sqlcmd de Microsoft](/sql/tools/sqlcmd-utility?view=sql-server-ver15&preserve-view=true).

### <a name="data-in-ascii-or-utf-16-format"></a>Datos en los formatos ASCII o UTF-16

Si va a probar este tutorial con sus propios datos, estos deben utilizar la codificación ASCII o UTF-16, ya que bcp no admite UTF-8.

## <a name="1-create-a-destination-table"></a>1. Creación de una tabla de destino.

Defina una tabla en SQL Database como tabla de destino. Las columnas de la tabla deben corresponder con los datos de cada fila del archivo de datos.

Para crear una tabla, abra un símbolo del sistema y use sqlcmd.exe para ejecutar el comando siguiente:

```cmd
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
"
```

## <a name="2-create-a-source-data-file"></a>2. Creación de un archivo de datos de origen

Abra el Bloc de notas y copie las líneas de datos siguientes en un nuevo archivo de texto y, después, guarde este archivo en el directorio temporal local, C:\Temp\DimDate2.txt. Estos datos están en formato ASCII.

```txt
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

(Opcional) Para exportar sus propios datos desde una base de datos de SQL Server, abra un símbolo del sistema y ejecute el comando siguiente. Reemplace TableName, ServerName, DatabaseName, Username y Password por su propia información.

```cmd
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ,
```

## <a name="3-load-the-data"></a>3. Carga de los datos

Para cargar los datos, abra un símbolo del sistema y ejecute el comando siguiente, pero reemplace los valores de nombre de servidor, nombre de base de datos, nombre de usuario y contraseña por su propia información.

```cmd
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ,
```

Utilice este comando para comprobar que los datos se cargaron correctamente

```cmd
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

El resultado debería ser similar a este:

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

## <a name="next-steps"></a>Pasos siguientes

Para migrar una base de datos de SQL Server, consulte el artículo sobre la [migración de una base de datos de SQL Server](database/migrate-to-database-from-sql-server.md).

<!--MSDN references-->
[bcp]: /sql/tools/bcp-utility
[CREATE TABLE syntax]: /sql/t-sql/statements/create-table-azure-sql-data-warehouse

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
