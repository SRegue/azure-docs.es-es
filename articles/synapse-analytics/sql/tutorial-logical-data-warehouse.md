---
title: 'Tutorial: Uso de un grupo de SQL sin servidor para crear un almacenamiento de datos lógico'
description: En este tutorial se muestra cómo crear fácilmente un almacenamiento de datos lógico en orígenes de datos de Azure mediante un grupo de SQL sin servidor.
services: synapse-analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/28/2021
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: f0f2e63a32c30c807f865a46154123643809de74
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114442852"
---
# <a name="tutorial-create-logical-data-warehouse-with-serverless-sql-pool"></a>Tutorial: Creación de un almacenamiento de datos lógico con un grupo de SQL sin servidor

En este tutorial, aprenderá a crear un almacenamiento de datos lógico (LDW) sobre Azure Storage y Azure Cosmos DB.

El almacenamiento de datos lógico es una capa relacional creada sobre orígenes de datos de Azure, como Azure Data Lake Storage (ADLS), el almacenamiento analítico de Azure Cosmos DB o Azure Blob Storage.

## <a name="create-an-ldw-database"></a>Creación de una base de datos de almacenamiento de datos lógico

Debe crear una base de datos personalizada donde almacenará las tablas externas y las vistas que hacen referencia a orígenes de datos externos.

```sql
CREATE DATABASE Ldw
      COLLATE Latin1_General_100_BIN2_UTF8;
```

Esta intercalación proporcionará el rendimiento óptimo al leer Parquet y Cosmos DB. Si no desea especificar la intercalación de base de datos, asegúrese de especificar esta intercalación en la definición de columna.

## <a name="configure-data-sources-and-formats"></a>Configuración de orígenes de datos y formatos

Como primer paso, debe configurar el origen de datos y especificar el formato de archivo de los datos almacenados de forma remota.

### <a name="create-data-source"></a>Creación de un origen de datos

Los orígenes de datos representan información de la cadena de conexión que describe dónde se colocan los datos y cómo autenticarse en el origen de datos.

En el ejemplo siguiente se muestra un ejemplo de definición de origen de datos que hace referencia al [conjunto de datos público de Azure del Centro europeo para la prevención y el control de enfermedades (ECDC) sobre la COVID 19](/azure/open-datasets/dataset-ecdc-covid-cases):

```sql
CREATE EXTERNAL DATA SOURCE ecdc_cases WITH (
    LOCATION = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/'
);
```

Un autor de llamada puede acceder al origen de datos sin credencial si un propietario del origen de datos permitió el acceso anónimo o proporcionó acceso explícito a la identidad del autor de la llamada de Azure AD.

Puede definir explícitamente una credencial personalizada que se usará al acceder a los datos de un origen de datos externo.
- [Identidad administrada](develop-storage-files-storage-access-control.md?tabs=managed-identity) del área de trabajo de Synapse
- [Firma de acceso compartido](develop-storage-files-storage-access-control.md?tabs=shared-access-signature) de Azure Storage
- Clave de cuenta de Cosmos DB de solo lectura que permite leer el almacenamiento analítico de Cosmos DB.

Como requisito previo, deberá crear una clave maestra en la base de datos:
```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Setup you password - you need to create master key only once';
```

En el siguiente origen de datos externo, el grupo de Synapse SQL debe usar una identidad administrada del área de trabajo para acceder a los datos del almacenamiento.

```sql
CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity
WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE ecdc_cases WITH (
    LOCATION = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/',
    CREDENTIAL = WorkspaceIdentity
);
```

Para acceder al almacenamiento analítico de Cosmos DB, debe definir una credencial que contenga una clave de cuenta de Cosmos DB de solo lectura.

```sql
CREATE DATABASE SCOPED CREDENTIAL MyCosmosDbAccountCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
```

### <a name="define-external-file-formats"></a>Definición de formatos de archivo externos

Los formatos de archivo externos definen la estructura de los archivos almacenados en un origen de datos externo. Puede definir formatos de archivo externos para Parquet y CSV:

```sql
CREATE EXTERNAL FILE FORMAT ParquetFormat WITH (  FORMAT_TYPE = PARQUET );
GO
CREATE EXTERNAL FILE FORMAT CsvFormat WITH (  FORMAT_TYPE = DELIMITEDTEXT );
```

Para más información, [consulte este artículo](develop-tables-external-tables.md?tabs=native#syntax-for-create-external-file-format).

## <a name="explore-your-data"></a>Exploración de los datos

Una vez configurados los orígenes de datos, puede usar la función `OPENROWSET` para explorar los datos. La función [OPENROWSET](develop-openrowset.md) lee el contenido de un origen de datos remoto (por ejemplo, un archivo) y lo devuelve en forma de un conjunto de filas.

```sql
select top 10  *
from openrowset(bulk 'latest/ecdc_cases.parquet',
                data_source = 'ecdc_cases',
                format='parquet') as a
```

La función `OPENROWSET` le proporciona información sobre las columnas de los contenedores o archivos externos, y le permite definir un esquema de las tablas y vistas externas.

## <a name="create-external-tables-on-azure-storage"></a>Creación de tablas externas en Azure Storage

Una vez que descubra el esquema, puede crear tablas y vistas externas sobre sus orígenes de datos externos. El procedimiento recomendado es organizar las tablas y vistas en esquemas de bases de datos. En la consulta siguiente puede crear un esquema en el que colocará todos los objetos que acceden al conjunto de datos sobre la COVID del ECDC en Azure Data Lake Storage:

```sql
create schema ecdc_adls;
```

Los esquemas de la base de datos son útiles para agrupar los objetos y definir los permisos por esquema. 

Una vez definidos los esquemas, puede crear tablas externas que hagan referencia a los archivos.
La siguiente tabla externa hace referencia al archivo Parquet del conjunto de datos sobre la COVID de ECDC colocado en Azure Storage:

```sql
create external table ecdc_adls.cases (
    date_rep                   date,
    day                        smallint,
    month                      smallint,
    year                       smallint,
    cases                      smallint,
    deaths                     smallint,
    countries_and_territories  varchar(256),
    geo_id                     varchar(60),
    country_territory_code     varchar(16),
    pop_data_2018              int,
    continent_exp              varchar(32),
    load_date                  datetime2(7),
    iso_country                varchar(16)
) with (
    data_source= ecdc_cases,
    location = 'latest/ecdc_cases.parquet',
    file_format = ParquetFormat
);
```

Asegúrese de que use los tipos más pequeños posibles para las columnas de cadena y número para optimizar el rendimiento de las consultas.

## <a name="create-views-on-azure-cosmos-db"></a>Creación de vistas en Azure Cosmos DB

Como alternativa a las tablas externas, puede crear vistas sobre los datos externos. 

De forma similar a las tablas que se muestran en el ejemplo anterior, debe colocar las vistas en esquemas independientes:

```sql
create schema ecdc_cosmosdb;
```

Ahora puede crear una vista en el esquema que hace referencia a un contenedor de Cosmos DB:

```sql
CREATE OR ALTER VIEW ecdc_cosmosdb.Ecdc
AS SELECT *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) WITH
     ( date_rep varchar(20), 
       cases bigint,
       geo_id varchar(6) 
     ) as rows
```

Para optimizar el rendimiento, debe usar los tipos más pequeños posibles de la definición de esquema `WITH`.

> [!NOTE]
> Debe colocar la clave de cuenta de Azure Cosmos DB en unas credenciales independientes y hacer referencia a ellas a partir de la función `OPENROWSET`.
> No mantenga su clave de cuenta en la definición de la vista.

## <a name="access-and-permissions"></a>Acceso y permisos

Como último paso, debe crear usuarios de base de datos que puedan acceder al almacenamiento de datos lógico y concederles permisos para seleccionar datos de las tablas y vistas externas.
En el siguiente script puede ver cómo agregar un nuevo usuario que se autenticará mediante la identidad de Azure AD:

```sql
CREATE USER [jovan@contoso.com] FROM EXTERNAL PROVIDER;
GO
```

En lugar de entidades de seguridad de Azure AD, puede crear entidades de seguridad de SQL que se autentiquen con el nombre de inicio de sesión y la contraseña.

```sql
CREATE LOGIN [jovan] WITH PASSWORD = 'My Very strong Password ! 1234';
CREATE USER [jovan] FROM LOGIN [jovan];
```

En ambos casos, puede asignar permisos a los usuarios.

```sql
DENY ADMINISTER DATABASE BULK OPERATIONS TO [jovan@contoso.com]
GO
GRANT SELECT ON SCHEMA::ecdc_adls TO [jovan@contoso.com]
GO
GRANT SELECT ON OBJECT::ecdc_cosmosDB.cases TO [jovan@contoso.com]
GO
GRANT REFERENCES ON CREDENTIAL::MyCosmosDbAccountCredential TO [jovan@contoso.com]
GO
```

Las reglas de seguridad dependen de las directivas de seguridad. Estas son algunas directrices generales:
- Debe denegar el permiso `ADMINISTER DATABASE BULK OPERATIONS` a los nuevos usuarios porque deben poder leer datos solo mediante las tablas y vistas externas que ha preparado.
- Debe proporcionar el permiso `SELECT` solo a aquellas tablas que algún usuario debería poder usar.
- Si proporciona acceso a los datos mediante las vistas, debe conceder el permiso `REFERENCES` a la credencial que se usará para acceder al origen de datos externo.

Este usuario tiene los permisos mínimos necesarios para consultar datos externos. Si desea crear un usuario avanzado que pueda configurar permisos, tablas externas y vistas, puede conceder permiso de `CONTROL` al usuario:

```sql
GRANT CONTROL TO [jovan@contoso.com]
```

### <a name="role-based-security"></a>Seguridad basada en roles

En lugar de asignar permisos a los usos individuales, una buena práctica es organizar a los usuarios en roles y administrar los permisos en el nivel de rol.
El ejemplo de código siguiente crea un nuevo rol que representa a las personas que pueden analizar casos de COVID-19 y agrega tres usuarios a este rol:

```sql
CREATE ROLE CovidAnalyst;

ALTER ROLE CovidAnalyst ADD MEMBER [jovan@contoso.com];
ALTER ROLE CovidAnalyst ADD MEMBER [milan@contoso.com];
ALTER ROLE CovidAnalyst ADD MEMBER [petar@contoso.com];
```

Puede asignar los permisos a todos los usuarios que pertenecen al grupo:

```sql
GRANT SELECT ON SCHEMA::ecdc_cosmosdb TO [CovidAnalyst];
GO
DENY SELECT ON SCHEMA::ecdc_adls TO [CovidAnalyst];
GO
DENY ADMINISTER DATABASE BULK OPERATIONS TO [CovidAnalyst];
```

Este control de acceso de seguridad basada en roles puede simplificar la administración de las reglas de seguridad.

## <a name="next-steps"></a>Pasos siguientes

- Para aprender a conectar un grupo de SQL sin servidor a Power BI Desktop y crear informes, consulte el artículo [Conexión de un grupo de SQL sin servidor a Power BI Desktop y creación de informes](tutorial-connect-power-bi-desktop.md).
- Para más información sobre el uso de tablas externas en los grupos de SQL sin servidor, consulte [Uso de tablas externas con Synapse SQL](develop-tables-external-tables.md?tabs=sql-pool).
