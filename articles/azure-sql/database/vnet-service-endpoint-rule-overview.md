---
title: Puntos de conexión y reglas de red virtual para bases de datos de Azure SQL Database
description: Marque una subred como punto de conexión de servicio de red virtual. Después, agregue el punto de conexión como una regla de red virtual a la lista de control de acceso de la base de datos. La base de datos aceptará la comunicación de todas las máquinas virtuales y otros nodos de la subred.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 05/26/2021
ms.openlocfilehash: b1748de761ad5180e2ddb670f31874620e4c5ae8
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111972003"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Uso de reglas y puntos de conexión de servicio de red virtual para servidores de Azure SQL Database

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Las *reglas de red virtual* son una característica de seguridad del firewall que controla si el servidor de las bases de datos y de los grupos elásticos de [Azure SQL Database](sql-database-paas-overview.md) o de las bases de datos del grupo de SQL dedicado de [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) acepta las comunicaciones que se envían desde subredes específicas de redes virtuales. En este artículo se explica por qué las reglas de redes virtuales a veces son la mejor opción para permitir la comunicación de forma segura con la base de datos de SQL Database y Azure Synapse Analytics.

> [!NOTE]
> Este artículo es aplicable a SQL Database y a Azure Synapse Analytics. Para simplificar, el término *base de datos* hace referencia a las bases de datos de SQL Database y a las de Azure Synapse Analytics. Del mismo modo, todas las referencias a *servidor* indican el [servidor de SQL Server lógico](logical-servers.md) que hospeda SQL Database y Azure Synapse Analytics.

Para crear una regla de red virtual, primero debe existir un [punto de conexión de servicio de red virtual][vm-virtual-network-service-endpoints-overview-649d] para la regla a la que hacer referencia.

## <a name="create-a-virtual-network-rule"></a>Creación de una regla de red virtual

Si solo desea crear una regla de red virtual, puede ir directamente a los pasos y la explicación que hay [más adelante en este artículo](#anchor-how-to-by-using-firewall-portal-59j).

## <a name="details-about-virtual-network-rules"></a>Detalles sobre las reglas de red virtual

En esta sección se describen varios detalles acerca de las reglas de red virtual.

### <a name="only-one-geographic-region"></a>Solo una región geográfica

Cada punto de conexión de servicio de red virtual es aplicable solo a una región de Azure. El punto de conexión no permite que otras regiones acepten la comunicación de la subred.

Cualquier regla de red virtual se limita a la región a la que se aplica su punto de conexión subyacente.

### <a name="server-level-not-database-level"></a>Nivel de servidor y no de base de datos

Cada regla de red virtual se aplica a todo el servidor y no solo a una base de datos determinada del servidor. En otras palabras, la regla de red virtual se aplica en el nivel de servidor y no en el nivel de base de datos.

En cambio, se pueden aplicar reglas IP en cualquier nivel.

### <a name="security-administration-roles"></a>Roles de administración de la seguridad

Existe una separación de los roles de seguridad en la administración de puntos de conexión de servicio de red virtual. Se requiere una acción de cada uno de los roles siguientes:

- **Rol Administrador de red ([Colaborador de red](../../role-based-access-control/built-in-roles.md#network-contributor)):** &nbsp;Active el punto de conexión.
- **Rol Administrador de base de datos ([Colaborador de SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor)):** &nbsp;Actualiza la lista de control de acceso (ACL) que se va a agregar a la subred proporcionada en el servidor.

#### <a name="azure-rbac-alternative"></a>Alternativa a RBAC de Azure

Los roles de administrador de red y de base de datos tienen más funcionalidades de las que se necesitan para administrar las reglas de red virtual. Solo se necesita un subconjunto de sus capacidades.

Si quiere, puede optar por usar el [control de acceso basado en rol (RBAC)][rbac-what-is-813s] en Azure para crear un rol personalizado único que tenga solo el subconjunto necesario de funcionalidades. Se podría usar el rol personalizado en lugar del administrador de red o el administrador de la base de datos. El área expuesta de la exposición de seguridad es inferior si agrega un usuario a un rol personalizado, en lugar de agregar el usuario a los otros dos roles de administrador principales.

> [!NOTE]
> En algunos casos, la base de datos de SQL Database y la subred de red virtual se encuentran en suscripciones diferentes. En estos casos debe garantizar las siguientes configuraciones:
>
> - Ambas suscripciones deben estar en el mismo inquilino de Azure Active Directory (Azure AD).
> - El usuario tiene los permisos necesarios para iniciar operaciones como habilitar los puntos de conexión de servicio y agregar una subred de red virtual al servidor especificado.
> - Las dos suscripciones necesitan tener registrado el proveedor Microsoft.Sql.

## <a name="limitations"></a>Limitaciones

Para SQL Database, la característica de regla de red virtual tiene las siguientes limitaciones:

- En el firewall de la base de datos de SQL Database, cada regla de red virtual hace referencia a una subred. Todas estas subredes a las que se hace referencia deben estar hospedadas en la misma región geográfica que hospeda la base de datos.
- Cada servidor puede tener hasta 128 entradas de ACL para cualquier red virtual.
- Las reglas de red virtual solo se aplican a las redes virtuales de Azure Resource Manager, y no a las redes del [modelo de implementación clásica][arm-deployment-model-568f].
- La activación de puntos de conexión de servicio de red virtual en SQL Database también habilita los puntos de conexión de Azure DB for MySQL y Azure Database for PostgreSQL. Con los puntos de conexión **ACTIVADOS**, es posible que se produzca un error al intentar conectarse desde los puntos de conexión con las instancias de Azure Database for MySQL o Azure Database for PostgreSQL.
  - El motivo subyacente es que es probable que Azure Database for MySQL y Azure Database for PostgreSQL no tengan una regla de red virtual configurada. Debe configurar una regla de red virtual de Azure Database for MySQL y Azure Database for PostgreSQL y la conexión se realizará correctamente.
  - Para definir las reglas de firewall de red virtual en un servidor lógico de SQL que ya está configurado con puntos de conexión privados, establezca **Deny public network access** (Denegar acceso a la red pública) en **No**.
- En el firewall, los intervalos de direcciones IP se aplican a los siguientes elementos de red, pero no las reglas de red virtual:
  - [Red privada virtual (VPN) de sitio a sitio (S2S)][vpn-gateway-indexmd-608y]
  - En el entorno local mediante [Express Route](../../expressroute/index.yml)

### <a name="considerations-when-you-use-service-endpoints"></a>Consideraciones al usar los puntos de conexión de servicio

Al utilizar los puntos de conexión de servicio para SQL Database, revise las consideraciones siguientes:

- **Se requiere la salida a direcciones IP públicas de Azure SQL Database.** Los grupos de seguridad de red deben estar abiertos en las direcciones IP de SQL Database para permitir la conectividad. Puede hacerlo mediante el uso de [etiquetas de servicio](../../virtual-network/network-security-groups-overview.md#service-tags) de grupos de seguridad de red para SQL Database.

### <a name="expressroute"></a>ExpressRoute

Si usa [ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) desde el entorno local para el emparejamiento público o el emparejamiento de Microsoft, tendrá que identificar las direcciones IP de NAT que se usan. Para el emparejamiento público, cada circuito ExpressRoute usa de forma predeterminada dos direcciones IP de NAT que se aplican al tráfico del servicio de Azure cuando el tráfico entra en la red troncal de Microsoft Azure. Para el emparejamiento de Microsoft, las direcciones IP de NAT que se utilizan las proporciona el cliente o el proveedor de servicios. Para permitir el acceso a los recursos de servicio, tiene que permitir estas direcciones IP públicas en la configuración del firewall de IP de recursos. Para encontrar las direcciones de IP de circuito de ExpressRoute de los pares públicos, [abra una incidencia de soporte técnico con ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) a través de Azure Portal. Para más información sobre NAT para el emparejamiento de Microsoft y el emparejamiento público de ExpresRoute, consulte [Requisitos de NAT para el emparejamiento público de Azure](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

Para permitir la comunicación desde el circuito a SQL Database, necesita crear reglas de red IP para las direcciones IP públicas de NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-virtual-network-service-endpoints-with-azure-storage"></a>Efectos del uso de puntos de conexión de servicio de red virtual con Azure Storage

Azure Storage ha implementado la misma característica que permite limitar la conectividad con la cuenta de Azure Storage. Si decide utilizar esta característica con una cuenta de Azure Storage que SQL Database esté usando, es posible que se produzcan errores. A continuación se muestra una lista de las características de SQL Database y Azure Synapse Analytics afectadas por esto.

### <a name="azure-synapse-analytics-polybase-and-copy-statement"></a>PolyBase de Azure Synapse Analytics e instrucción COPY

PolyBase y la instrucción COPY se suelen usar para cargar datos en Azure Synapse Analytics desde cuentas de Azure Storage para la ingesta de datos de alto rendimiento. Si la cuenta de Azure Storage desde la que se cargan los datos limita el acceso a solo un conjunto de subredes de red virtual, se interrumpirá la conectividad cuando se utilice PolyBase y la instrucción COPY con la cuenta de almacenamiento. Para habilitar los escenarios de importación y exportación mediante COPY y PolyBase con la conexión de Azure Synapse Analytics a una instancia de Azure Storage protegida en una red virtual, siga los pasos que se indican en esta sección.

#### <a name="prerequisites"></a>Requisitos previos

- Instale Azure PowerShell mediante [esta guía](/powershell/azure/install-az-ps).
- Si tiene una cuenta de uso general v1 o de Azure Blob Storage, primero debe actualizar a una cuenta de uso general v2 mediante los pasos que se indican en [Actualización a una cuenta de almacenamiento de uso general v2](../../storage/common/storage-account-upgrade.md).
- Debe activar **Permitir que los servicios de Microsoft de confianza accedan a esta cuenta de almacenamiento** en el menú de configuración **Firewalls y redes virtuales** de la cuenta de Azure Storage. La habilitación de esta configuración permitirá que PolyBase y la instrucción COPY se conecten a la cuenta de almacenamiento mediante la autenticación sólida en la que el tráfico de red permanece en la red troncal de Azure. Para más información, consulte [esta guía](../../storage/common/storage-network-security.md#exceptions).

> [!IMPORTANT]
> El módulo de Azure Resource Manager para PowerShell todavía es compatible con SQL Database, pero todo el desarrollo futuro se realizará para el módulo Az.Sql. El módulo de AzureRM continuará recibiendo correcciones de errores hasta diciembre de 2020 como mínimo. Los argumentos para los comandos del módulo Az y los módulos AzureRm son esencialmente idénticos. Para obtener más información sobre la compatibilidad, vea [Presentación del nuevo módulo Az de Azure PowerShell](/powershell/azure/new-azureps-module-az).

#### <a name="steps"></a>Pasos

1. Si tiene un grupo de SQL dedicado independiente, registre el servidor SQL Server con Azure AD mediante PowerShell:

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Este paso no es necesario con grupos de SQL dedicados que se encuentran en un área de trabajo de Azure Synapse Analytics.

1. Si tiene un área de trabajo de Azure Synapse Analytics, registre la identidad administrada por el sistema de esa área de trabajo:

   1. Vaya al área de trabajo de Azure Synapse Analytics en Azure Portal.
   2. Vaya al panel **Identidades administradas**.
   3. Asegúrese de que la opción **Allow Pipelines** (Permitir canalizaciones) esté habilitada.
   
1. Cree una **cuenta de almacenamiento de uso general v2** mediante los pasos que se indican en [Crear una cuenta de almacenamiento](../../storage/common/storage-account-create.md).

   > [!NOTE]
   >
   > - Si tiene una cuenta de uso general v1 o de Blob Storage, *primero debe actualizar a una cuenta de uso general v2* mediante los pasos que se indican en [Actualización a una cuenta de almacenamiento de uso general v2](../../storage/common/storage-account-upgrade.md).
   > - Para más información sobre los problemas conocidos con Azure Data Lake Storage Gen2, consulte [Problemas conocidos con Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-known-issues.md).

1. En la cuenta de almacenamiento, vaya a **Access Control (IAM)** y seleccione **Agregar asignación de roles**. Asigne el rol de Azure **Colaborador de datos de blobs de almacenamiento** al servidor o al área de trabajo que hospedan el grupo de SQL dedicado que ha registrado con Azure AD.

   > [!NOTE]
   > Solo los miembros con el privilegio Propietario sobre la cuenta de almacenamiento pueden realizar este paso. Para conocer los distintos roles integrados de Azure, consulte [Roles integrados de Azure](../../role-based-access-control/built-in-roles.md).
  
1. Para habilitar la conectividad de PolyBase a la cuenta de Azure Storage:

   1. Cree una [clave maestra](/sql/t-sql/statements/create-master-key-transact-sql) de base de datos si no la ha creado anteriormente.

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Cree una credencial con un ámbito de base de datos con **IDENTITY = "Managed Service Identity"** .

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - No es necesario especificar SECRET con una clave de acceso de Azure Storage porque este mecanismo usa la [identidad administrada](../../active-directory/managed-identities-azure-resources/overview.md) en segundo plano.
       > - El nombre de IDENTITY debe ser **"Managed Service Identity"** (Identidad de servicio administrado) para que la conectividad de PolyBase funcione con una cuenta de Azure Storage protegida en una red virtual.

   1. Cree un origen de datos externo con el esquema `abfss://` para conectarse a la cuenta de almacenamiento de uso general v2 mediante PolyBase.

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - Si ya tiene tablas externas asociadas con una cuenta de uso general v1 o de Blob Storage, primero debe quitarlas. Después, quite el origen de datos externo correspondiente. A continuación, cree un origen de datos externo con el esquema `abfss://` que se conecte a una cuenta de almacenamiento de uso general v2 mediante PolyBase como se ha indicado anteriormente. A continuación, vuelva a crear todas las tablas externas mediante este nuevo origen de datos externo. Puede usar el [Asistente para generar y publicar scripts](/sql/ssms/scripting/generate-and-publish-scripts-wizard) para generar scripts de creación para todas las tablas externas con facilidad.
       > - Para más información sobre el esquema `abfss://`, consulte [Uso del URI de Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md).
       > - Para más información sobre CREATE EXTERNAL DATA SOURCE, consulte [esta guía](/sql/t-sql/statements/create-external-data-source-transact-sql).

   1. Realice una consulta normal mediante las [tablas externas](/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="sql-database-blob-auditing"></a>Auditoría de blobs de SQL Database

La auditoría de Azure SQL puede escribir registros de auditoría de SQL en su propia cuenta de almacenamiento. Si esta cuenta de almacenamiento usa la característica de puntos de conexión de servicio de red virtual, consulte cómo [escribir una auditoría en una cuenta de almacenamiento situada detrás de la red virtual y un firewall](./audit-write-storage-account-behind-vnet-firewall.md).

## <a name="add-a-virtual-network-firewall-rule-to-your-server"></a>Incorporación de una regla de firewall de red virtual al servidor

Mucho antes de mejorar esta característica, era necesario activar los puntos de conexión de servicio de red virtual para poder implementar una regla dinámica de red virtual en el firewall. Los puntos de conexión relacionaban una subred de red virtual determinada con una base de datos de SQL Database. A partir de enero de 2018, puede evitar este requisito si establece la marca **IgnoreMissingVNetServiceEndpoint**. Ahora, puede agregar una regla de firewall de red virtual al servidor sin activar los puntos de conexión de servicio de red virtual.

Si solo establece una regla de firewall, no tendrá el servidor protegido. También debe activar los puntos de conexión de servicio de red virtual para que la seguridad surta efecto. Cuando activa los puntos de conexión de servicio, la subred de la red virtual experimenta cierto tiempo de inactividad hasta que se completa la transición de desactivado a activado. Este período de inactividad es especialmente significativo en el contexto de redes virtuales de gran tamaño. Puede usar la marca **IgnoreMissingVNetServiceEndpoint** para reducir o eliminar el tiempo de inactividad durante la activación.

Puede establecer la marca **IgnoreMissingVNetServiceEndpoint** mediante PowerShell. Para más información, consulte [PowerShell para crear una regla y un punto de conexión de servicio de red virtual para SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Errores 40914 y 40615

El error de conexión 40914 se relaciona con *reglas de red virtual*, tal y como se especifica en el panel **Firewall** de Azure Portal. El error 40615 es similar, excepto en que se relaciona con las *reglas de direcciones IP* del firewall.

### <a name="error-40914"></a>Error 40914

**Texto del mensaje:** "No se puede abrir el servidor *[server-name]* solicitado por el inicio de sesión. Un cliente no puede acceder al servidor."

**Descripción del error:** el cliente está en una subred que tiene puntos de conexión de servidor de red virtual. Aun así, el servidor tiene ninguna regla de red virtual que conceda a la subred el derecho para comunicarse con la base de datos.

**Resolución de errores:** en el panel **Firewall** de Azure Portal, use el control de reglas de red virtual para [agregar una regla de red virtual](#anchor-how-to-by-using-firewall-portal-59j) para la subred.

### <a name="error-40615"></a>Error 40615

**Texto del mensaje:** "No se puede abrir el servidor {0} solicitado por el inicio de sesión. El cliente con la dirección IP {1} no tiene permiso para acceder al servidor".

**Descripción del error:** el cliente intenta conectarse desde una dirección IP que no tiene autorización para conectarse al servidor. El firewall de servidor no tiene ninguna regla de dirección IP que permita que un cliente se comunique con la dirección IP dada a la base de datos.

**Resolución de errores:** escriba la dirección IP del cliente como una regla de IP. Use el panel **Firewall** de Azure Portal para realizar este paso.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="use-the-portal-to-create-a-virtual-network-rule"></a>Uso del portal para crear una regla de red virtual

En esta sección se muestra cómo se puede usar [Azure Portal][http-azure-portal-link-ref-477t] para crear una *regla de red virtual* en la base de datos de SQL Database. La regla indica a la base de datos que acepte la comunicación procedente de una subred concreta que se ha etiquetado como *punto de conexión de servicio de red virtual*.

> [!NOTE]
> Asegúrese de que los puntos de conexión de servicio estén activados para la subred si desea agregar un punto de conexión de servicio a las reglas de firewall de red virtual de su servidor.
>
> Si los puntos de conexión de servicio no están activados para la subred, el portal le pedirá que los habilite. Haga clic en el botón **Habilitar** del mismo panel en el que agrega la regla.

## <a name="powershell-alternative"></a>Alternativa de PowerShell

Un script también puede crear reglas de red virtual mediante el cmdlet de PowerShell **New-AzSqlServerVirtualNetworkRule** o [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Si le interesa, consulte [PowerShell para crear una regla y un punto de conexión de servicio de red virtual para SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>Alternativa a la API REST

Internamente, los cmdlets de PowerShell para acciones de red virtual de SQL llaman a las API REST. Puede llamar directamente a las API REST.

- [Reglas de red virtual: Operaciones][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Requisitos previos

Ya debe tener una subred que esté etiquetada con el *nombre de tipo* concreto del punto de conexión de servicio de red virtual correspondiente a SQL Database.

- El nombre de tipo de punto de conexión pertinente es **Microsoft.Sql**.
- Si es posible que su subred no se pueda etiquetar con el nombre de tipo, consulte [Comprobar que su subred es un punto de conexión][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Pasos de Azure Portal

1. Inicie sesión en [Azure Portal][http-azure-portal-link-ref-477t].

1. Busque y seleccione **Servidores SQL Server** y, después, seleccione el servidor. En **Seguridad**, seleccione **Firewalls y redes virtuales**.

1. Establezca **Permitir el acceso a servicios de Azure** en **DESACTIVADO**.

    > [!IMPORTANT]
    > Si deja el control establecido en **ACTIVADO**, el servidor aceptará las comunicaciones desde cualquier subred dentro de los límites de Azure. Es decir, las comunicaciones que se originan en una de las direcciones IP que se reconocen como pertenecientes a los intervalos definidos para los centros de datos de Azure. Si deja el control establecido en **ACTIVADO**, el número de accesos podría ser excesivo desde un punto de vista de seguridad. La característica de punto de conexión de servicio de red virtual de Microsoft Azure, junto con la característica de regla de red virtual de SQL Database, pueden reducir el área expuesta de seguridad.

1. Seleccione **+ Agregar existente**, en la sección **Redes virtuales**.

    ![Captura de pantalla que muestra la selección de + Agregar existente (punto de conexión de subred, como una regla SQL).][image-portal-firewall-vnet-add-existing-10-png]

1. En el nuevo panel **Crear/Actualizar**, rellene los cuadros con los nombres de los recursos de Azure.

    > [!TIP]
    > Debe incluir el prefijo de dirección correcto de la subred. Puede encontrar el valor **Prefijo de dirección** en el portal. Vaya a **Todos los recursos** &gt; **Todos los tipos** &gt; **Redes virtuales**. El filtro muestra sus redes virtuales. Seleccione la red virtual y, después, seleccione **Subredes**. La columna **INTERVALO DE DIRECCIONES** tiene el prefijo de dirección que necesita.

    ![Captura de pantalla que muestra los cuadros rellenos para la nueva regla.][image-portal-firewall-create-update-vnet-rule-20-png]

1. Seleccione el botón **Aceptar** situado cerca de la parte inferior del panel.

1. Consulte la regla de red virtual resultante en el panel **Firewall**.

    ![Captura de pantalla que muestra la nueva regla en el panel Firewall.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Se aplican los siguientes estados a las reglas:
>
> - **Listo**: indica que la operación que se ha iniciado se ha realizado correctamente.
> - **Error**: indica que se ha producido un error en la operación que se ha iniciado.
> - **Eliminado**: solo se aplica a la operación de eliminación e indica que se ha eliminado la regla y que ya no es aplicable.
> - **InProgress**: indica que la operación está en curso. Se aplica la regla anterior mientras la operación está en este estado.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>Artículos relacionados

- [Punto de conexión de servicio de red virtual de Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Reglas de firewall de nivel de servidor y de base de datos][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>Pasos siguientes

- [Use PowerShell para crear un punto de conexión de servicio de red virtual y, a continuación, una regla de red virtual Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].
- [Reglas de red virtual: Operaciones][rest-api-virtual-network-rules-operations-862r] con API REST

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]:../index.yml
[rbac-what-is-813s]:../../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]:firewall-configure.md
[sql-db-vnet-service-endpoint-rule-powershell-md-52d]:scripts/vnet-service-endpoint-rule-powershell-create.md
[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]:scripts/vnet-service-endpoint-rule-powershell-create.md#a-verify-subnet-is-endpoint-ps-100
[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[vm-virtual-network-service-endpoints-overview-649d]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[vpn-gateway-indexmd-608y]: ../../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
[rest-api-virtual-network-rules-operations-862r]: /rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON
- Azure CLI
- ARM templates
-->