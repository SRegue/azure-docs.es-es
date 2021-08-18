---
title: 'De Db2 a SQL Server en VM de Azure: guía de migración'
description: En esta guía aprenderá a migrar bases de datos IBM Db2 a SQL Server en máquinas virtuales Azure mediante SQL Server Migration Assistant para Db2.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: chadam
ms.date: 05/14/2021
ms.openlocfilehash: d82d423051af6a0cea8ab8b8fa5646ee80accf51
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746616"
---
# <a name="migration-guide-ibm-db2-to-sql-server-on-azure-vm"></a>Guía de migración: de Db2 a SQL Server en VM de Azure
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Con esta guía de aprenderá a migrar bases de datos de usuario de IBM Db2 a SQL Server en VM de Azure mediante SQL Server Migration Assistant para Db2. 

Para ver otras guías de migración, consulte la [Guía de Azure Database Migration](/data-migration). 

## <a name="prerequisites"></a>Requisitos previos

Para migrar la base de datos de Db2 a SQL Server, necesita:

- Comprobar que el [entorno de origen sea compatible](/sql/ssma/db2/installing-ssma-for-Db2-client-Db2tosql#prerequisites).
- [SQL Server Migration Assistant (SSMA) para Db2](https://www.microsoft.com/download/details.aspx?id=54254).
- [Conectividad](../../virtual-machines/windows/ways-to-connect-to-sql.md) entre el entorno de origen y la VM de SQL Server en Azure. 
- Una instancia de destino de [SQL Server en una VM de Azure](../../virtual-machines/windows/create-sql-vm-portal.md). 

## <a name="pre-migration"></a>Antes de la migración

Una vez cumplidos los requisitos previos, estará a punto para detectar la topología de su entorno y evaluar la viabilidad de la migración. 

### <a name="assess"></a>Evaluar 

Use SSMA para Db2 para revisar datos y objetos de bases de datos, y evaluar las bases de datos para la migración. 

Para crear una evaluación, siga estos pasos:

1. Abra [SSMA para Db2](https://www.microsoft.com/download/details.aspx?id=54254). 
1. Seleccione **Archivo** > **Nuevo proyecto**. 
1. Proporcione un nombre de proyecto y una ubicación para guardar el proyecto. Después, seleccione un destino de migración de SQL Server en la lista desplegable y seleccione **Aceptar**.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/new-project.png" alt-text="Captura de pantalla que muestra los detalles del proyecto que se van a especificar.":::


1. En **Conectar a Db2**, escriba los valores de los detalles de conexión de Db2.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/connect-to-Db2.png" alt-text="Captura de pantalla que muestra las opciones para conectarse a la instancia de Db2.":::


1. Haga clic con el botón derecho en el esquema de Db2 que quiera migrar y, luego, elija **Create Report** (Crear informe). Se generará un informe HTML. También puede elegir **Crear informe** en la barra de navegación después de seleccionar el esquema.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/create-report.png" alt-text="Captura de pantalla que muestra cómo crear un informe.":::

1. Revise el informe en HTML para comprender las estadísticas de conversión, y los errores o advertencias. También puede abrir el informe en Excel para obtener un inventario de objetos Db2 y conocer el esfuerzo necesario para realizar las conversiones de esquema. La ubicación predeterminada del informe es la carpeta de informes dentro de *SSMAProjects*.

   Por ejemplo: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/report.png" alt-text="Captura de pantalla del informe que se debe revisar para identificar los errores o las advertencias.":::


### <a name="validate-data-types"></a>Validación de los tipos de datos

Valide las asignaciones de tipos de datos predeterminadas y cámbielas según los requisitos, si es necesario. Para hacerlo, siga estos pasos: 

1. Seleccione **Herramientas** en el menú principal. 
1. Seleccione **Configuración del proyecto**. 
1. Seleccione la pestaña **Type mappings** (Asignaciones de tipos).

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/type-mapping.png" alt-text="Captura de pantalla que muestra la selección del esquema y la asignación de tipos.":::

1. Puede cambiar la asignación de tipos de cada tabla seleccionando la tabla en el **Explorador de metadatos de Db2**. 

### <a name="convert-schema"></a>Convertir esquema 

Para convertir el esquema, siga estos pasos:

1. (Opcional) Agregue consultas dinámicas o ad hoc a las instrucciones. Haga clic con el botón derecho en el nodo y elija **Add statements** (Agregar instrucciones). 
1. Seleccione **Conectarse a SQL Server**. 
    1. Escriba los detalles de la conexión para conectarse a la instancia de SQL Server en la VM de Azure. 
    1. Elija esta opción para conectarse a una base de datos existente en el servidor de destino o especifique un nuevo nombre para crear una base de datos en el servidor de destino. 
    1. Proporcione los detalles de la autenticación. 
    1. Seleccione **Conectar**.

    :::image type="content" source="../../../../includes/media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png" alt-text="Captura de pantalla que muestra los detalles necesarios para conectarse a SQL Server en una VM de Azure.":::

1. Haga clic con el botón derecho en el esquema y elija **Convertir esquema**. También puede elegir **Convertir esquema** en la barra de navegación superior después de seleccionar el esquema.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/convert-schema.png" alt-text="Captura de pantalla que muestra la selección del esquema y la opción para convertirlo.":::

1. Una vez finalizada la conversión, compare y revise la estructura del esquema para identificar posibles problemas. Solucione los problemas en función de las recomendaciones. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-review-schema-structure.png" alt-text="Captura de pantalla que muestra la comparación y revisión de la estructura del esquema para identificar posibles problemas.":::

1. En el panel **Resultados**, seleccione **Revisar resultados**. En el panel **Lista de errores**, revise los errores. 
1. Guarde el proyecto localmente para un ejercicio de corrección de esquema sin conexión. En el menú **Archivo**, seleccione **Guardar proyecto**. Esto le ofrece la oportunidad de evaluar los esquemas de origen y de destino sin conexión, y hacer correcciones para poder publicar el esquema en SQL Server en una VM de Azure.

## <a name="migrate"></a>Migrar

Cuando haya terminado de evaluar las bases de datos y de resolver las discrepancias, el siguiente paso consiste en ejecutar el proceso de migración.

Para publicar el esquema y migrar los datos, siga estos pasos:

1. Publique el esquema. En el **Explorador de metadatos de SQL Server**, en el nodo **Bases de datos**, haga clic con el botón derecho en la base de datos. A continuación, seleccione **Sincronizar con la base de datos**.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/synchronize-with-database.png" alt-text="Captura de pantalla que muestra la opción para sincronizar con la base de datos.":::

1. Migre los datos. Haga clic con el botón derecho en la base de datos o el objeto que quiera migrar en el **Explorador de metadatos de Db2** y elija **Migrar datos**. Como alternativa, puede seleccionar **Migrar datos** en la barra de navegación. Para migrar datos de toda una base de datos, active la casilla situada junto al nombre de la base de datos. Para migrar datos de tablas concretas, expanda la base de datos, expanda **Tablas** y, a continuación, active la casilla que hay junto a la tabla. Para omitir datos de tablas concretas, desactive la casilla.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/migrate-data.png" alt-text="Captura de pantalla que muestra la selección del esquema y la elección de la opción Migrar datos.":::

1. Especifique los detalles de la conexión para las instancias de Db2 y SQL Server. 
1. Una vez terminada la migración, vea el **Informe de migración de datos**:  

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/data-migration-report.png" alt-text="Captura de pantalla que muestra dónde revisar el informe de migración de datos.":::

1.  Conéctese a la instancia de SQL Server en la VM de Azure mediante [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Para validar la migración, revise los datos y el esquema.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-schema-in-ssms.png" alt-text="Captura de pantalla que muestra la comparación del esquema en SQL Server Management Studio.":::

## <a name="post-migration"></a>Etapa posterior a la migración 

Cuando haya completado correctamente, deberá realizar una serie de tareas posteriores para asegurarse de que todo funcione de la forma más fluida y eficaz posible.

### <a name="remediate-applications"></a>Corrección de las aplicaciones 

Cuando se hayan migrado los datos al entorno de destino, todas las aplicaciones que antes utilizaban el origen deben empezar a utilizar el destino. Lograr esto requerirá en algunos casos realizar cambios en las aplicaciones.

### <a name="perform-tests"></a>Realización de pruebas

Las pruebas constan de las siguientes actividades:

1. **Desarrollar pruebas de validación**: para probar la migración de bases de datos, debe utilizar consultas SQL. Debe crear las consultas de validación para que se ejecuten en las bases de datos de origen y destino. Las consultas de validación deben abarcar el ámbito definido.
1. **Configurar el entorno de prueba**: el entorno de prueba debe contener una copia de la base de datos de origen y la base de datos de destino. Asegúrese de aislar el entorno de prueba.
1. **Ejecutar pruebas de validación**: ejecute las pruebas de validación en el origen y el destino y, luego, analice los resultados.
1. **Ejecutar pruebas de rendimiento**: ejecute la prueba de rendimiento en el origen y el destino y, luego, analice y compare los resultados.

## <a name="migration-assets"></a>Recursos de migración 

Para obtener más ayuda, consulte los siguientes recursos, que se desarrollaron como apoyo de la participación en un proyecto de migración en el mundo real:

|Recurso  |Descripción  |
|---------|---------|
|[Herramienta y modelo de evaluación de la carga de trabajo de datos](https://www.microsoft.com/download/details.aspx?id=103130)| Esta herramienta proporciona las plataformas de destino de ajuste perfecto sugeridas, la preparación para la nube, y el nivel de corrección de la aplicación o base de datos para una carga de trabajo determinada. Ofrece un cálculo sencillo con un solo clic y una función de generación de informes que ayuda a acelerar las evaluaciones de grandes volúmenes, ya que proporciona un proceso de toma de decisiones de plataforma de destino uniforme y automatizado.|
|[Paquete de detección y evaluación de recursos de datos de Db2 zOS](https://www.microsoft.com/download/details.aspx?id=103108)|Después de ejecutar el script de SQL en una base de datos, puede exportar los resultados a un archivo en el sistema de archivos. Se admiten varios formatos de archivo, incluido \*.csv, para que se puedan capturar los resultados en herramientas externas, como hojas de cálculo. Este método puede resultar útil si quiere compartir fácilmente los resultados con equipos que no tengan el área de trabajo instalada.|
|[Artefactos y scripts de inventario de IBM Db2 LUW](https://www.microsoft.com/download/details.aspx?id=103109)|Este recurso incluye una consulta SQL que accede a las tablas del sistema IBM Db2 LUW, versión 11.1, y proporciona un recuento de objetos por esquema y tipo de objeto, una estimación aproximada de "datos sin procesar" en cada esquema y el tamaño de las tablas de cada esquema, cuyos resultados se almacenan en formato CSV.|
|[Utilidad Comparación de bases de datos de IBM Db2 a SQL Server](https://www.microsoft.com/download/details.aspx?id=103016)|La utilidad Comparación de bases de datos es una aplicación de consola de Windows que se puede usar para comprobar que los datos son idénticos en la plataforma de origen y en la de destino. Puede usar la herramienta para comparar de forma eficaz los datos hasta el nivel de fila o de columna de todas las tablas, filas y columnas o únicamente en las seleccionadas.|

El equipo de ingeniería de datos SQL ha desarrollado estos recursos. El objetivo principal de este equipo es permitir y acelerar la modernización compleja de los proyectos de migración de la plataforma de datos a la de Azure, de Microsoft.

## <a name="next-steps"></a>Pasos siguientes

Después de la migración, revise la [Guía de optimización y validación posterior a la migración](/sql/relational-databases/post-migration-validation-and-optimization-guide). 

Para obtener los servicios y herramientas de Microsoft y de otros fabricantes que están disponibles para ayudarle en diversos escenarios de migración de datos y bases de datos, consulte [Servicios y herramientas de migración de datos](../../../dms/dms-tools-matrix.md).

Para ver el contenido de vídeo, consulte [Información general sobre el recorrido de migración](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).
