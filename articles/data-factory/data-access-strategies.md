---
title: Estrategias de acceso a datos
description: Azure Data Factory admite ahora intervalos de direcciones IP estáticas.
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: 0b5d2921a59cc62ec4a58230a76eee33e4347ed0
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123250721"
---
# <a name="data-access-strategies"></a>Estrategias de acceso a datos

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Un objetivo de seguridad vital de una organización es proteger sus almacenes de datos del acceso aleatorio a través de Internet, puede ser un almacén de datos local o de nube o SaaS. 

Normalmente, un almacén de datos en la nube controla el acceso mediante los mecanismos siguientes:
* Vínculo privado de una red virtual a los orígenes de datos habilitados para el punto de conexión privado
* Reglas de firewall que limitan la conectividad mediante una dirección IP
* Mecanismos de autenticación que solicitan a los usuarios sus identidades
* Mecanismos de autorización que limitan los usuarios a acciones y datos específicos

> [!TIP]
> Con la [introducción del intervalo de direcciones IP estáticas](./azure-integration-runtime-ip-addresses.md), ahora puede permitir la lista de intervalos IP para la región del entorno de ejecución de integración de Azure determinada para asegurarse de que no tiene que permitir todas las direcciones IP de Azure en los almacenes de datos en la nube. De este modo, puede restringir las direcciones IP a las que se les permite el acceso a los almacenes de datos.

> [!NOTE] 
> Los intervalos de direcciones IP están bloqueados para Azure Integration Runtime y actualmente solo se usan para movimiento de datos, canalización y actividades externas. Azure Integration Runtime y los flujos de datos que habilitan la red virtual administrada ahora no usan estos intervalos IP. 

Esto debería funcionar en muchos escenarios, y sabemos que es deseable una dirección IP estática única por cada tiempo de ejecución de integración, pero actualmente esto no sería posible mediante el uso de Azure Integration Runtime, que está sin servidor. Si es necesario, siempre puede configurar un entorno de ejecución de integración autohospedado y usar su dirección IP estática. 

## <a name="data-access-strategies-through-azure-data-factory"></a>Estrategias de acceso a datos a través de Azure Data Factory

* **[Private Link](../private-link/private-link-overview.md)** : puede crear una instancia de Azure Integration Runtime en la red virtual administrada de Azure Data Factory y aprovechará los puntos de conexión privados para conectarse de forma segura a los almacenes de datos compatibles. El tráfico entre la red virtual administrada y los orígenes de datos viaja por la red troncal de Microsoft y no se exponen a la red pública.
* **[Servicio de confianza](../storage/common/storage-network-security.md#exceptions)** : Azure Storage (BLOB, ADLS Gen2) admite la configuración del firewall que permite seleccionar servicios de la plataforma de Azure de confianza para acceder a la cuenta de almacenamiento de forma segura. Los servicios de confianza aplican la autenticación de identidad administrada, lo que garantiza que ninguna otra factoría de datos pueda conectarse a este almacenamiento, a menos que tenga la aprobación para hacerlo mediante su identidad administrada. Puede encontrar más información en **[este blog](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)** . Por lo tanto, es muy seguro y recomendado. 
* **IP estática única**: deberá configurar una instancia de entorno de ejecución de integración autohospedado para obtener una dirección IP estática para los conectores de Data Factory. Este mecanismo garantiza que puede bloquear el acceso desde todas las demás direcciones IP. 
* **[Intervalo de direcciones IP estáticas](./azure-integration-runtime-ip-addresses.md)** : puede usar las direcciones IP de Azure Integration Runtime para permitir que se muestren en el almacenamiento (por ejemplo, S3, Salesforce, etc.). Ciertamente, restringe las direcciones IP que se pueden conectar a los almacenes de datos, pero también se basa en las reglas de autenticación y autorización.
* **[Etiqueta de servicio](../virtual-network/service-tags-overview.md)** : una etiqueta de servicio representa un grupo de prefijos de direcciones IP de un servicio de Azure determinado (como Azure Data Factory). Microsoft administra los prefijos de direcciones que la etiqueta de servicio incluye y actualiza automáticamente dicha etiqueta a medida que las direcciones cambian, lo que minimiza la complejidad de las actualizaciones frecuentes en las reglas de seguridad de red. Resulta útil al filtrar el acceso a los datos de los almacenes de datos hospedados por IaaS en Virtual Network.
* **Permitir servicios de Azure**: algunos servicios admiten permitir que todos los servicios de Azure se conecten a él en caso de que elija esta opción. 

Para obtener más información sobre los mecanismos de seguridad de red admitidos en los almacenes de datos de Azure Integration Runtime y Integration Runtime autohospedado, consulte las dos tablas siguientes.  
* **Azure Integration Runtime**

    | Almacenes de datos                  | Mecanismo de seguridad de red compatible con almacenes de datos | Private Link     | Servicio de confianza     | Rango de direcciones IP estáticas | Etiquetas de servicio | Permitir servicios de Azure |
    |------------------------------|-------------------------------------------------------------|---------------------|-----------------|--------------|----------------------|-----------------|
    | Almacenes de datos PaaS de Azure       | Azure Cosmos DB                                     | Sí              | -                   | Sí             | -            | Sí                  |
    |                              | Explorador de datos de Azure                                 | -                | -                   | Sí*            | Sí*         | -                    |
    |                              | Azure Data Lake Gen1                                | -                | -                   | Sí             | -            | Sí                  |
    |                              | Azure Database for MariaDB, MySQL y PostgreSQL       | -                | -                   | Sí             | -            | Sí                  |
    |                              | Azure Files                                  | Sí              | -                   | Sí             | -            | .                    |
    |                              | Azure Blob Storage y ADLS Gen2                     | Sí              | Sí (solo autenticación MSI) | Sí             | -            | .                    |
    |                              | Azure SQL DB, Azure Synapse Analytics), SQL Ml  | Sí (solo Azure SQL DB/DW)        | -                   | Sí             | -            | Sí                  |
    |                              | Azure Key Vault (para capturar secretos/cadena de conexión) | sí      | Sí                 | Sí             | -            | -                    |
    | Otros almacenes de datos de PaaS/SaaS | AWS S3, SalesForce, Google Cloud Storage, etc.    | -                | -                   | Sí             | -            | -                    |
    | Azure laaS                   | SQL Server, Oracle, etc.                          | -                | -                   | Sí             | Sí          | -                    |
    | LaaS local              | SQL Server, Oracle, etc.                          | -                | -                   | Sí             | -            | -                    |
    
    **Solo se aplica cuando Azure Data Explorer es la red virtual inyectada y se puede aplicar el intervalo IP en NSG/Firewall.* 

* **Entorno de ejecución de integración autohospedado (en Vnet/local)**
    
    | Almacenes de datos                  | Mecanismo de seguridad de red compatible con almacenes de datos         | IP estática | Servicios de confianza  |
    |--------------------------------|---------------------------------------------------------------|-----------|---------------------|
    | Almacenes de datos PaaS de Azure       | Azure Cosmos DB                                               | Sí       | -                   |
    |                                | Explorador de datos de Azure                                           | -         | -                   |
    |                                | Azure Data Lake Gen1                                          | Sí       | -                   |
    |                                | Azure Database for MariaDB, MySQL y PostgreSQL               | Sí       | -                   |
    |                                | Azure Files                                            | Sí       | -                   |
    |                                | Azure Blob Storage y ADLS Gen2                             | Sí       | Sí (solo autenticación MSI) |
    |                                | Azure SQL DB, Azure Synapse Analytics), SQL Ml          | Sí       | -                   |
    |                                | Azure Key Vault (para capturar secretos/cadena de conexión) | Sí       | Sí                 |
    | Otros almacenes de datos de PaaS/SaaS | AWS S3, SalesForce, Google Cloud Storage, etc.              | Sí       | -                   |
    | Azure laaS                     | SQL Server, Oracle, etc.                                  | Sí       | -                   |
    | LaaS local              | SQL Server, Oracle, etc.                                  | Sí       | -                   |    

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte los siguientes artículos:
* [Almacenes de datos compatibles](./copy-activity-overview.md#supported-data-stores-and-formats)
* [Servicios de confianza de Azure Key Vault](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)
* [Servicios de confianza de Microsoft Azure Storage](../storage/common/storage-network-security.md#trusted-microsoft-services)
* [Identidad administrada de Data Factory](./data-factory-service-identity.md)