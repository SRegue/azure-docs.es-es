---
title: Conexión y consulta de Synapse SQL con Visual Studio y SSDT
description: Use Visual Studio para consultar el grupo de SQL dedicado mediante Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 7d534794737f93cd71ee9820f72e6c5ead08ca2b
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121861000"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Conexión a SQL de Synapse con Visual Studio y SSDT

> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Use Visual Studio para consultar el grupo de SQL dedicado mediante Azure Synapse Analytics. Este método usa la extensión SQL Server Data Tools (SSDT) de Visual Studio 2019. 

> [!NOTE]
> SSDT no admite el grupo de SQL sin servidor.

## <a name="prerequisites"></a>Prerrequisitos

Para usar este tutorial, debe contar con los siguientes componentes:

- Un área de trabajo de Synapse existente. Si necesita crear una, consulte [Creación de un área de trabajo de Synapse](../get-started-create-workspace.md).
- Un grupo de SQL dedicado. Para crearlo, consulte [Creación de un grupo de SQL dedicado](../get-started-analyze-sql-pool.md#create-a-dedicated-sql-pool).
- SSDT para Visual Studio. Si tiene Visual Studio, probablemente ya tenga este componente. Para ver instrucciones y opciones de instalación, consulte [Instalación de Visual Studio 2015 y SSDT para Almacenamiento de datos SQL](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?context=/azure/synapse-analytics/context/context).
- El nombre del servidor SQL completo. Para encontrar este nombre de servidor, consulte [Conexión a un grupo de SQL dedicado](connect-overview.md).

## <a name="1-connect-to-a-dedicated-sql-pool"></a>1. Conexión a un grupo de SQL dedicado
1. Abra Visual Studio 2019.
2. Abra el Explorador de objetos de SQL Server; para ello, seleccione **Ver** > **Explorador de objetos de SQL Server**.
   
    ![Explorador de objetos de SQL Server](./media/get-started-visual-studio/open-ssdt.png)
3. Haga clic en el botón **Agregar SQL Server** .
   
    ![Agregar SQL Server](./media/get-started-visual-studio/add-server.png)
4. Rellene los campos en la ventana Conectar al servidor.
   
    ![Conectar con el servidor](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Nombre del servidor**: Escriba el **nombre del servidor** definido anteriormente.
   * **Autenticación**: Seleccione **Autenticación de SQL Server** o **Autenticación integrada de Active Directory**:
   * **Nombre de usuario** y **contraseña**: Escriba su nombre de usuario y la contraseña si la autenticación de SQL Server se seleccionó anteriormente.
   * Haga clic en **Conectar**.
5. Para explorar, expanda su Azure SQL Server. Puede ver las bases de datos asociadas al servidor. Expanda AdventureWorksDW para ver las tablas de la base de datos de ejemplo.
   
    ![Explorar AdventureWorksDW](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Ejecución de una consulta de ejemplo
Ahora que se ha establecido una conexión a la base de datos, podrá escribir una consulta.

1. Haga clic con el botón derecho en la base de datos en el Explorador de objetos de SQL Server.
2. Seleccione **Nueva consulta**. Se abrirá una nueva ventana de consulta.
   
    ![Nueva consulta](./media/get-started-visual-studio/new-query2.png)
3. Copie la siguiente consulta T-SQL en la ventana de consulta:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Ejecute la consulta al hacer clic en la flecha verde o use el siguiente acceso directo: `CTRL`+`SHIFT`+`E`.
   
    ![Ejecutar consulta](./media/get-started-visual-studio/run-query.png)
5. Consulte los resultados de la consulta. En este ejemplo, la tabla FactInternetSales tiene 60398 filas.
   
    ![Resultados de la consulta](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>Pasos siguientes
Ahora que puede conectarse y realizar consultas, intente realizar la [Visualización de datos con Power BI](get-started-power-bi-professional.md).
Para configurar un entorno para la autenticación de Azure Active Directory, vea [Autenticación en el grupo de SQL dedicado](sql-authentication.md?tabs=provisioned).
 