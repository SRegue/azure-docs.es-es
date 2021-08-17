---
title: 'Tutorial: Migración de SQL Server a SQL Managed Instance'
titleSuffix: Azure Database Migration Service
description: Aprenda a migrar de SQL Server a una instancia administrada de Azure SQL Database mediante Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: b77b242d34986e423bf87d6be0eda2074cd7df36
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2021
ms.locfileid: "114689075"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-offline-using-dms"></a>Tutorial: Migración de SQL Server a Instancia administrada de Azure SQL sin conexión con Database Migration Service

Azure Database Migration Service se puede usar para migrar las bases de datos de una instancia de SQL Server a una [Instancia administrada de Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md). Para ver otros métodos que pueden requerir algún esfuerzo manual, vea el artículo [De SQL Server a Azure SQL Managed Instance](../azure-sql/migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).

En este tutorial, migrará la base de datos **Adventureworks2012** desde una instancia local de SQL Server hasta una instancia administrada de SQL Database mediante Azure Database Migration Service.

En este tutorial, aprenderá a:
> [!div class="checklist"]
>
> - Crear una instancia de Azure Database Migration Service.
> - Crear un proyecto de migración mediante Azure Database Migration Service.
> - Ejecutar la migración.
> - Supervisar la migración
> - Descargar un informe de migración.

> [!IMPORTANT]
> En las migraciones sin conexión de SQL Server a Instancia administrada de SQL, Azure Database Migration Service puede crear los archivos de copia de seguridad. Como alternativa, puede proporcionar la última copia de seguridad completa de la base de datos en el recurso compartido de red de SMB, que será la que use el servicio para migrar las bases de datos. Cada copia de seguridad se puede escribir en un archivo de copia de seguridad independiente o en varios archivos de copia de seguridad. Sin embargo, no se admite la anexación de varias copias de seguridad en un único medio de copia de seguridad. Tenga en cuenta que también puede usar copias de seguridad comprimidas para reducir la probabilidad de experimentar posibles problemas con la migración de copias de seguridad de gran tamaño.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

En este artículo se describe una migración en línea desde SQL Server hasta una Instancia administrada de SQL. Para migraciones en línea, consulte [Migración de SQL Server a una instancia administrada de SQL en línea mediante DMS](tutorial-sql-server-managed-instance-online.md).

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesita:

- Cree una instancia de Azure Virtual Network para Azure Database Migration Service mediante el modelo de implementación de Azure Resource Manager, que proporciona conectividad de sitio a sitio a los servidores de origen local mediante [ExpressRoute](../expressroute/expressroute-introduction.md) o [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). [Obtenga información sobre topologías de red para migraciones de Instancia administrada de SQL mediante Azure Database Migration Service](./resource-network-topologies.md). Para más información sobre la creación de una red virtual, consulte la documentación de [Virtual Network](../virtual-network/index.yml)y, especialmente, los artículos de inicio rápido con detalles paso a paso.

    > [!NOTE]
    > Durante la configuración de la red virtual, si usa ExpressRoute con emparejamiento de red a Microsoft, agregue los siguientes [puntos de conexión](../virtual-network/virtual-network-service-endpoints-overview.md) de servicio a la subred en la que se aprovisionará el servicio:
    > - Punto de conexión de base de datos de destino (por ejemplo, punto de conexión de SQL, punto de conexión de Cosmos DB, etc.)
    > - Punto de conexión de Storage
    > - Punto de conexión de Service Bus
    >
    > Esta configuración es necesaria porque Azure Database Migration Service no tiene conexión a Internet.

- Asegúrese de que las reglas del grupo de seguridad de red de la red virtual no bloquean el puerto de salida 443 de ServiceTag para ServiceBus, Storage y AzureMonitor. Para más información sobre el filtrado del tráfico con grupos de seguridad de red para redes virtuales, vea el artículo [Filtrado del tráfico de red con grupos de seguridad de red](../virtual-network/virtual-network-vnet-plan-design-arm.md).
- Configure el [Firewall de Windows para acceder al motor de base de datos de origen](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Abra el firewall de Windows para que Azure Database Migration Service pueda acceder al servidor SQL Server de origen; de forma predeterminada, es el puerto TCP 1433. Si la instancia predeterminada está escuchando en otro puerto, agréguelo al firewall.
- Si se ejecutan varias instancias con nombre de SQL Server con puertos dinámicos, puede ser conveniente habilitar el servicio SQL Browser y permitir el acceso al puerto UDP 1434 mediante los firewalls para que Azure Database Migration Service pueda conectarse a una instancia con nombre en el servidor de origen.
- Si va a usar un dispositivo de firewall delante de las bases de datos de origen, puede que sea necesario agregar reglas de firewall para permitir que Azure Database Migration Service acceda a las bases de datos de origen para realizar la migración, así como archivos a través del puerto SMB 445.
- Cree una Instancia administrada de SQL mediante los pasos que se describen en el artículo [Creación de una Instancia administrada de SQL en Azure Portal](../azure-sql/managed-instance/instance-create-quickstart.md).
- Asegúrese de que los inicios de sesión usados para conectar SQL Server de origen y SQL Managed Instance de destino son miembros del rol de servidor sysadmin.

    >[!NOTE]
    >De forma predeterminada, Azure Database Migration Service solo admite la migración de inicios de sesión de SQL. Sin embargo, puede habilitar la capacidad de migrar inicios de sesión de Windows de la siguiente manera:
    >
    >- Asegúrese de que la instancia de SQL Managed Instance de destino tenga acceso de lectura de AAD, que pueda configurar un usuario con el rol **Administrador global** mediante Azure Portal.
    >- Configure la instancia de Azure Database Migration Service para habilitar las migraciones de inicio de sesión de usuario o grupo de Windows, que se configuran mediante Azure Portal, en la página Configuración. Después de habilitar esta configuración, reinicie el servicio para que los cambios se apliquen.
    >
    > Después de reiniciar el servicio, los inicios de sesión de usuario o grupo de Windows aparecen en la lista de inicios de sesión disponibles para la migración. En el caso de los inicios de sesión de usuario o grupo de Windows que migre, se le pedirá que proporcione el nombre de dominio asociado. No se admiten las cuentas de usuario de servicio (cuenta con el nombre de dominio NT AUTHORITY) y las cuentas de usuario virtual (nombre de cuenta con el nombre de dominio NT SERVICE).

- Cree un recurso compartido de red que pueda usar Azure Database Migration Service para hacer copia de seguridad de la base de datos de origen.
- Asegúrese de que la cuenta de servicio que ejecuta la instancia de SQL Server de origen tiene privilegios de escritura en el recurso compartido de red que haya creado, y que la cuenta del equipo del servidor de origen tiene acceso de lectura y escritura para el mismo recurso compartido.
- Anote un usuario de Windows (y su contraseña) que tenga privilegios de control total sobre el recurso compartido de red que creó anteriormente. Azure Database Migration Service suplanta la credencial de usuario para cargar los archivos de copia de seguridad en el contenedor de Azure Storage para la operación de restauración.
- Cree un contenedor de blobs y recupere su URI de SAS mediante los pasos del artículo [Administración de recursos Azure Blob Storage con el Explorador de Storage](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container), asegúrese de seleccionar todos los permisos (lectura, escritura, eliminación, lista) en la ventana de directiva al crear el URI de SAS. Estos detalles proporcionan a Azure Database Migration Service acceso al contenedor de cuentas de almacenamiento para cargar los archivos de copia de seguridad usados para migrar bases de datos a la instancia administrada de SQL.

    > [!NOTE]
    > Azure Database Migration Service no admite el uso de un token de SAS de nivel de cuenta al configurar la cuenta de almacenamiento durante el paso para [configurar las opciones de migración](#configure-migration-settings).
    
## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registro del proveedor de recursos Microsoft.DataMigration

1. Inicie sesión en Azure Portal, seleccione **Todos los servicios** y, después, **Suscripciones**.

    ![Mostrar suscripciones en el portal](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)

2. Seleccione la suscripción en la que quiere crear la instancia de Azure Database Migration Service y después seleccione **Proveedores de recursos**.

    ![Mostrar los proveedores de recursos](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. Busque la migración y después, a la derecha de **Microsoft.DataMigration**, seleccione **Registrar**.

    ![Registro del proveedor de recursos](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Creación de una instancia de Azure Database Migration Service

1. En Azure Portal, seleccione **+ Crear un recurso**, busque **Azure Database Migration Service** y, a continuación, seleccione **Azure Database Migration Service** en la lista desplegable.

    ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. En la pantalla **Azure Database Migration Service**, seleccione **Crear**.

    ![Creación de una instancia de Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. En la pantalla **Crear el servicio de migración**, especifique un nombre para el servicio, la suscripción y un grupo de recursos nuevo o existente.

4. Seleccione la ubicación en la que desea crear la instancia de DMS.

5. Seleccione una red virtual existente o cree una.

    La red virtual proporciona a Azure Database Migration Service acceso al servidor SQL Server de origen y a la Instancia administrada de SQL de destino.

    Para más información sobre cómo crear una red virtual en Azure Portal, consulte el artículo [Creación de una red virtual mediante Azure Portal](../virtual-network/quick-create-portal.md).

    Para más información, consulte el artículo [Topologías de red para migraciones de Azure SQL Managed Instance mediante Azure Database Migration Service](./resource-network-topologies.md).

6. Seleccione un plan de tarifa.

    Para más información sobre los costos y planes de tarifa, vea la [página de precios](https://aka.ms/dms-pricing).

    ![Creación de servicio DMS](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. Seleccione **Crear** para crear el servicio.

## <a name="create-a-migration-project"></a>Creación de un proyecto de migración

Después de crear una instancia del servicio, búsquela en Azure Portal, ábrala y cree un proyecto de migración.

1. En Azure Portal, seleccione **Todos los servicios**, busque Azure Database Migration Service y, luego, elija **Azure Database Migration Services**.

    ![Búsqueda de todas las instancias de Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. En la pantalla **Azure Database Migration Service**, busque el nombre de la instancia que ha creado y selecciónela.

3. Seleccione **+ New Migration Project** (+ Nuevo proyecto de migración).

4. En la pantalla **New migration project** (Nuevo proyecto de migración), especifique el nombre del proyecto; en el cuadro de texto **Source server type** (Tipo de servidor de origen), seleccione **SQL Server**; en el cuadro de texto **Target server type** (Tipo de servidor de destino), seleccione **Azure SQL Database Managed Instance** (Instancia administrada de Azure SQL) y, finalmente, en **Choose type of activity** (Elegir tipo de actividad), seleccione **Offline data migration** (Migración de datos sin conexión).

   ![Creación de proyecto DMS](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. Seleccione **Crear** para crear el proyecto.

## <a name="specify-source-details"></a>Especificación de los detalles de origen

1. En la pantalla **Detalles del origen de la migración**, especifique los detalles de conexión de SQL Server de origen.

2. Si no ha instalado ningún certificado de confianza en el servidor, seleccione la casilla **Certificado de servidor de confianza**.

    Si no hay ningún certificado de confianza instalado, SQL Server genera un certificado autofirmado cuando se inicia la instancia. Este certificado se usa para cifrar las credenciales de las conexiones del cliente.

    > [!CAUTION]
    > Las conexiones TLS cifradas mediante un certificado autofirmado no proporcionan una gran seguridad. Son susceptibles de sufrir ataques de tipo "Man in the middle". No debe confiar en TLS con certificados autofirmados en un entorno de producción, ni en servidores conectados a Internet.

   ![Detalles del origen](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. Seleccione **Guardar**.

4. En la pantalla **Seleccionar las bases de datos de origen**, seleccione la base de datos **Adventureworks2012** para la migración.

   ![Selección de las bases de datos de origen](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > Si usa SQL Server Integration Services (SSIS), DMS no admite actualmente la migración de la base de datos de catálogo de los proyectos o paquetes de SSIS (SSISDB) desde SQL Server hasta la Instancia administrada de SQL. Sin embargo, puede aprovisionar SSIS en Azure Data Factory (ADF) y volver a implementar los proyectos o paquetes de SSIS en la SSISDB de destino que hospeda la Instancia administrada de SQL. Para más información acerca de la migración de paquetes de SSIS, consulte el artículo [Migración de paquetes de SQL Server Integration Services a Azure](./how-to-migrate-ssis-packages.md).

5. Seleccione **Guardar**.

## <a name="specify-target-details"></a>Especificación de los detalles de destino

1. En la pantalla **Detalles del destino de la migración**, especifique los detalles de conexión del destino, que es la instancia administrada de SQL aprovisionada previamente a la que se migra la base de datos **AdventureWorks2012**.

    Si aún no ha aprovisionado la Instancia administrada de SQL, seleccione el [vínculo](../azure-sql/managed-instance/instance-create-quickstart.md) para hacerlo. Aun así, puede continuar con la creación del proyecto y, a continuación, cuando la instancia administrada de SQL esté lista, regresar a este proyecto específico para ejecutar la migración.

    ![Selección del destino](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. Seleccione **Guardar**.

## <a name="select-source-databases"></a>Selección de las bases de datos de origen

1. En la pantalla **Seleccionar las bases de datos de origen**, seleccione las bases de datos de origen que quiera migrar.

    ![Selección de las bases de datos de origen](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. Seleccione **Guardar**.

## <a name="select-logins"></a>Selección de inicios de sesión

1. En la pantalla **Seleccionar inicios de sesión**, seleccione los inicios de sesión que desea migrar.

    >[!NOTE]
    >De forma predeterminada, Azure Database Migration Service solo admite la migración de inicios de sesión de SQL. Para habilitar la compatibilidad con la migración de inicios de sesión de Windows, consulte la sección **Requisitos previos** de este tutorial.

    ![Selección de inicios de sesión](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. Seleccione **Guardar**.

## <a name="configure-migration-settings"></a>Configuración de valores de migración

1. En la pantalla **Configurar los valores de la migración**, proporcione los detalles siguientes:

    | Parámetro | Descripción |
    |--------|---------|
    |**Elegir la opción de copia de seguridad de origen** | Elija la opción **I will provide latest backup files** (Proporcionaré los archivos de copia de seguridad más recientes) cuando tenga archivos de la copia de seguridad completa disponibles para que DMS los use para la migración de la base de datos. Elija la opción **I will let Azure Database Migration Service create backup files** (Dejaré que Azure Database Migration Service cree archivos de copia de seguridad) cuando desee que DMS realice una copia de seguridad completa de la base de datos de origen al principio y usarla para la migración. |
    |**Recurso compartido de la ubicación de red** | El recurso compartido de red de SMB local en el que Azure Database Migration Service puede realizar copias de seguridad de la base de datos de origen. La cuenta de servicio que ejecuta la instancia de SQL Server de origen debe tener privilegios de escritura en este recurso compartido de red. Proporcione un FQDN o direcciones IP del servidor en el recurso compartido de red como, por ejemplo, "\\\servername.domainname.com\backupfolder" o "\\\IP address\backupfolder".|
    |**Nombre de usuario** | Asegúrese de que el usuario de Windows tiene privilegio de control total sobre el recurso compartido de red que especificó anteriormente. Azure Database Migration Service suplanta la credencial de usuario para cargar los archivos de copia de seguridad en el contenedor de Azure Storage para la operación de restauración. Si las bases de datos con TDE habilitado se seleccionan para la migración, el usuario de Windows anterior debe ser la cuenta de administrador integrada y se debe deshabilitar [User Account Control](/windows/security/identity-protection/user-account-control/user-account-control-overview) para que Azure Database Migration Service se cargue y elimine los archivos de certificados. |
    |**Contraseña** | Contraseña del usuario. |
    |**Configuración de cuentas de almacenamiento** | Identificador URI de SAS que proporciona a Azure Database Migration Service acceso al contenedor de la cuenta de almacenamiento en el que el servicio carga los archivos de copia de seguridad y que se usa para la migración de bases de datos a la instancia administrada de SQL. [Más información sobre cómo obtener el identificador URI de SAS para el contenedor de blobs](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container). Este URI de SAS debe ser para el contenedor de blobs, no para la cuenta de almacenamiento.|
    |**Configuración TDE** | Si va a migrar las bases de datos de origen con el cifrado de datos transparente (TDE) habilitado, deberá tener privilegios de escritura en la instancia administrada de SQL de destino.  En el menú desplegable, seleccione la suscripción en la que se aprovisiona la instancia administrada de SQL.  En el menú desplegable, seleccione la **Instancia administrada de Azure SQL Database** de destino. |

    ![Configuración de valores de migración](media/tutorial-sql-server-to-managed-instance/dms-configure-migration-settings3.png)

2. Seleccione **Guardar**.

## <a name="review-the-migration-summary"></a>Examen del resumen de la migración

1. En la pantalla **Migration summary** (Resumen de migración), en el cuadro de texto **Nombre de actividad**, especifique un nombre para la actividad de migración.

2. Expanda la sección **Opción de validación** para que se muestre la pantalla **Elegir la opción de validación**, en la que se especifica si quiere validar las bases de datos migradas para la exactitud de la consulta y, a continuación, seleccione **Guardar**.

3. Revise y compruebe los detalles relacionados con el proyecto de migración.

    ![Resumen del proyecto de migración](media/tutorial-sql-server-to-managed-instance/dms-project-summary2.png)

4. Seleccione **Guardar**.

## <a name="run-the-migration"></a>Ejecución de la migración

- Seleccione **Ejecutar migración**.

  Aparecerá la ventana de actividad de migración y el estado de la actividad será **Pendiente**.

## <a name="monitor-the-migration"></a>Supervisión de la migración

1. En la pantalla de la actividad de migración, seleccione **Actualizar** para actualizar la pantalla.

   ![Captura de pantalla que muestra la pantalla actividad de migración y el botón Actualizar.](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    Puede ampliar aún más las bases de datos y las categorías de inicio de sesión para supervisar el estado de migración de los respectivos objetos de servidor.

   ![Actividad de migración en curso](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. Una vez concluida la migración, seleccione **Descargar informe** para obtener un informe que muestre los detalles relacionados con el proceso de migración.

3. Compruebe la base de datos de destino en el entorno de destino de la instancia administrada de SQL.

## <a name="next-steps"></a>Pasos siguientes

- Para consultar un tutorial que muestra cómo migrar una base de datos a SQL Managed Instance mediante el comando T-SQL RESTORE, consulte [Restauración de una copia de seguridad para SQL Managed Instance mediante el comando Restore](../azure-sql/managed-instance/restore-sample-database-quickstart.md).
- Para más información sobre SQL Managed Instance, consulte [¿Qué es SQL Managed Instance?](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).
- Para más información sobre cómo conectar las aplicaciones a SQL Managed Instance, consulte [Conexión de aplicaciones](../azure-sql/managed-instance/connect-application-instance.md).
