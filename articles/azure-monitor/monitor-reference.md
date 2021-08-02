---
title: Qué supervisa Azure Monitor
description: Referencia de todos los servicios y otros recursos supervisados por Azure Monitor.
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 08/15/2020
ms.openlocfilehash: 3fa167233c5afe7b50b2794f6c0fff5458e9acf7
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110089063"
---
# <a name="what-is-monitored-by-azure-monitor"></a>¿Qué supervisa Azure Monitor?
En este artículo se describen las distintas aplicaciones y servicios que se supervisan mediante Azure Monitor. 

## <a name="insights-and-core-solutions"></a>Conclusiones y soluciones principales
Las conclusiones y soluciones principales se consideran parte de Azure Monitor y siguen los acuerdos de nivel de servicio y soporte técnico de Azure. Se admiten en todas las regiones de Azure donde Azure Monitor está disponible.

### <a name="insights"></a>Información detallada

Insights proporciona una experiencia de supervisión personalizada para determinadas aplicaciones y servicios. Recopilan y analizan tanto los registros como las métricas.

| Conclusión | Descripción |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Servicio de administración del rendimiento de aplicaciones (APM) extensible para supervisar una aplicación web activa en cualquier plataforma. |
| [Container Insights](containers/container-insights-overview.md) | Supervisa el rendimiento de las cargas de trabajo de contenedor implementadas en Azure Container Instances o en clústeres de Kubernetes administrados hospedados en Azure Kubernetes Service (AKS). |
| [Información sobre Cosmos DB](insights/cosmosdb-insights-overview.md) | Proporciona una vista del rendimiento general, los errores, la capacidad y el estado operativo de todos los recursos de Azure Cosmos DB en una experiencia interactiva unificada. |
| [Información de redes (versión preliminar)](insights/network-insights-overview.md) | Proporciona una vista completa del estado y las métricas de todos los recursos de red. La capacidad de búsqueda avanzada ayuda a identificar dependencias de recursos, lo que habilita escenarios como la identificación de recursos que hospedan el sitio web con una simple búsqueda del nombre del sitio web. |
[Información del grupo de recursos (versión preliminar)](insights/resource-group-insights.md) |  Clasifica y diagnostica cualquier problema que encuentren sus recursos individuales, a la vez que ofrece un contexto en cuanto al estado y el rendimiento del grupo de recursos como un todo. |
| [Información de almacenamiento](insights/storage-insights-overview.md) | Proporciona una supervisión completa de las cuentas de Azure Storage al ofrecer una vista unificada del rendimiento, la capacidad y la disponibilidad de los servicios de Azure Storage. |
| [VM Insights](vm/vminsights-overview.md) | Supervisa las máquinas virtuales (VM) y los conjuntos de escalado de máquinas virtuales de Azure. El servicio analiza el rendimiento y el estado de las VM Windows y Linux, y supervisa sus procesos y dependencias en otros recursos y procesos externos. |
| [Información de Azure Key Vault (versión preliminar)](./insights/key-vault-insights-overview.md) | Proporciona una supervisión completa de los almacenes de claves proporcionando una vista unificada del rendimiento, los errores, la latencia y las solicitudes de Key Vault. |
| [Información de Azure Cache for Redis (versión preliminar)](insights/redis-cache-insights-overview.md) |  Proporciona una vista unificada e interactiva del rendimiento general, los errores, la capacidad y el mantenimiento operativo. |


### <a name="core-solutions"></a>Soluciones principales

Las soluciones se basan en consultas de registros y vistas personalizadas para una aplicación o servicio en particular. Recopilan y analizan solo los registros y están cayendo en desuso a favor de las conclusiones.

| Solución | Descripción |
|:---|:---|
| [Estado del agente](insights/solution-agenthealth.md) | Analice el estado y la configuración de los agentes de Log Analytics. |
| [Administración de alertas](insights/alert-management-solution.md) | Analice las alertas recopiladas de System Center Operations Manager, Nagios o Zabbix. |
| [Mapa de servicio](vm/service-map.md) | Detecta automáticamente los componentes de la aplicación en sistemas Windows y Linux, y asigna la comunicación entre servicios. |



## <a name="azure-services"></a>Servicios de Azure
En la tabla siguiente se enumeran los servicios de Azure y los datos que estos recopilan en Azure Monitor. 

- Métricas: El servicio recopila automáticamente métricas en Métricas de Azure Monitor. 
- Registros: El servicio admite la configuración de diagnóstico que puede recopilar registros y métricas de la plataforma para Registros de Azure Monitor.
- Insight: Hay una conclusión disponible para el servicio, que proporciona una experiencia de supervisión personalizada para el servicio.

| Servicio | Métricas | Registros | Conclusión | Notas |
|:---|:---|:---|:---|:---|
|Active Directory | No | Sí | [Sí](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | No | No | No |  |
|Active Directory Domain Services | No | Sí | No |  |
|Registro de actividades | No | Sí | No | |
|Protección contra amenazas avanzada | No | No | No |  |
|Advisor | No | No | No |  |
|AI Builder | No | No | No |  |
|Analysis Services | Sí | Sí | No |  |
|API for FHIR | No | No | No |  |
|API Management | Sí | Sí | No |  |
|App Service | Sí | Sí | No |  |
|AppConfig | No | No | No |  |
|Application Gateway | Sí | Sí | No |  |
|Servicio de atestación | No | No | No |  |
|Automation | Sí | Sí | No |  |
|Azure Service Manager (RDFE) | No | No | No |  |
|Copia de seguridad | No | Sí | No |  |
|Bastion | No | No | No |  |
|Batch | Sí | Sí | No |  |
|Batch AI | No | No | No |  |
|Blockchain Service | No | Sí | No |  |
|Planos técnicos | No | No | No |  |
|Servicio de bots | No | No | No |  |
|Cloud Services | Sí | Sí | No | Agente necesario para supervisar los flujos de trabajo y el sistema operativo invitado.  |
|Cloud Shell | No | No | No |  |
|Cognitive Services | Sí | Sí | No |  |
|Azure Container Instances | Sí | No | No |  |
|Container Registry | Sí | Sí | No |  |
|Content Delivery Network (CDN) | No | Sí | No |  |
|Cosmos DB | Sí | Sí | [Sí](insights/cosmosdb-insights-overview.md) |  |
|Administración de costos | No | No | No |  |
|Data Box | No | No | No |  |
|Data Catalog Gen2 | No | No | No |  |
|Data Explorer | Sí | Sí | No |  |
|Data Factory | Sí | Sí | No |  |
|Data Factory v2 | No | Sí | No |  |
|Recurso compartido de datos | No | No | No |  |
|Database for MariaDB | Sí | Sí | No |  |
|Database for MySQL | Sí | Sí | No |  |
|Database for PostgreSQL | Sí | Sí | No |  |
|Database Migration Service | No | No | No |  |
|Databricks | No | Sí | No |  |
|Protección contra DDOS | Sí | Sí | No |  |
|DevOps | No | No | No |  |
|DNS | Sí | No | No |  |
|Nombres de dominio | No | No | No |  |
|DPS | No | No | No |  |
|Dynamics 365 Customer Engagement | No | No | No |  |
|Dynamics 365 Finance y Operations | No | No | No |  |
|Event Grid | Sí | No | No |  |
|Event Hubs | Sí | Sí | No |  |
|ExpressRoute | Sí | Sí | No |  |
|Firewall | Sí | Sí | No |  |
|Front Door | Sí | Sí | No |  |
|Functions | Sí | Sí | No |  |
|HDInsight | No | Sí | No |  |
|HPC Cache | No | No | No |  |
|Information Protection | No | Sí | No |  |
|Intune | No | Sí | No |  |
|IoT Central | No | No | No |  |
|IoT Hub | Sí | Sí | No |  |
|Key Vault | Sí | Sí | [Sí](./insights/key-vault-insights-overview.md) |  |
|Kubernetes Service (AKS) | No | No | [Sí](containers/container-insights-overview.md)  |  |
|Load Balancer | Sí | No | No |  |
|Logic Apps | Sí | Sí | No |  |
|Machine Learning Service | No | No | No |  |
|Aplicaciones administradas  | No | No | No |  |
|Mapas  | No | No | No |  |
|Media Services | Sí | Sí | No |  |
|Escritorio administrado de Microsoft | No | No | No |  |
|Microsoft PowerApps | No | No | No |  |
|Microsoft Social Engagement | No | No | No |  |
|Microsoft Stream | Sí | Sí | No |  |
|Migrar | No | No | No |  |
|Multi-Factor Authentication | No | Sí | No |  |
|Network Watcher | Sí | Sí | No |  |
|Notification Hubs | Sí | No | No |  |
|Open Datasets | No | No | No |  |
|Directiva | No | No | No |  |
|Power Automate | No | No | No |  |
|Power BI Embedded | Sí | Sí | No |  |
|Private Link | No | No | No |  |
|Plataforma de comunicación de Project Spool | No | No | No |  |
|Red Hat OpenShift | No | No | No |  |
|Redis Cache | Sí | Sí | [Sí](insights/redis-cache-insights-overview.md) | |
|Gráfico de recursos | No | No | No |  |
|Resource Manager | No | No | No |  |
|Búsqueda minorista: por Bing | No | No | No |  |
|Search | Sí | Sí | No |  |
|Azure Service Bus | Sí | Sí | No |  |
|Service Fabric | No | Sí | No | Agente necesario para supervisar los flujos de trabajo y el sistema operativo invitado.  |
|Portal de suscripción | No | No | No |  |
|Site Recovery | No | Sí | No |  |
|Servicio Spring Cloud | No | No | No |  |
|Azure Synapse Analytics | Sí | Sí | No |  |
|SQL Database | Sí | Sí | No |  |
|SQL Server Stretch Database | Sí | Sí | No |  |
|Pila | No | No | No |  |
|Storage | Sí | No | [Sí](insights/storage-insights-overview.md) |  |
|Almacenamiento caché | No | No | No |  |
|Servicios de sincronización de almacenamiento | No | No | No |  |
|Stream Analytics | Sí | Sí | No |  |
|Time Series Insights | Sí | Sí | No |  |
|TINA | No | No | No |  |
|Traffic Manager | Sí | Sí | No |  |
|Impresión universal | No | No | No |  |
|Virtual Machine Scale Sets | No | Sí | [Sí](vm/vminsights-overview.md) | Agente necesario para supervisar los flujos de trabajo y el sistema operativo invitado. |
|Virtual Machines | Sí | Sí | [Sí](vm/vminsights-overview.md) | Agente necesario para supervisar los flujos de trabajo y el sistema operativo invitado. |
|Virtual Network | Sí | Sí | [Sí](insights/network-insights-overview.md) |  |
|Virtual Network: registros de flujo de NSG | No | Sí | No |  |
|VPN Gateway | Sí | Sí | No |  |
|Windows Virtual Desktop | No | Sí | No |  |

## <a name="virtual-machine-agents"></a>Agentes de máquina virtual
En la tabla siguiente se enumeran los agentes que pueden recopilar datos del sistema operativo invitado de máquinas virtuales y enviar datos a Monitor. Cada agente puede recopilar datos diferentes y enviarlos a métricas o registros en Azure Monitor. 

Consulte [Información general sobre los agentes de Azure Monitor](agents/agents-overview.md) para obtener más información sobre los datos que puede recopilar cada agente.

| Agente |  Métricas | Registros |
|:---|:---|:---|:---|
| [Agente de Azure Monitor (versión preliminar)](agents/azure-monitor-agent-overview.md) | Sí | Sí |
| [Agente de Log Analytics](agents/log-analytics-agent.md) | No | Sí|
| [Extensión de diagnóstico](agents/diagnostics-extension-overview.md) | Sí | No |
| [Agente Telegraf](essentials/collect-custom-metrics-linux-telegraf.md) | Sí | No |
| [Dependency Agent](vm/vminsights-enable-overview.md) | No | Sí |


## <a name="product-integrations"></a>Integraciones de productos
Los servicios y las soluciones de la tabla siguiente almacenan sus datos en un área de trabajo de Log Analytics para que se puedan analizar con otros datos de registro recopilados por Azure Monitor.

| Producto o servicio | Descripción |
|:---|:---|
| [Azure Automation](../automation/index.yml) | Administre las actualizaciones del sistema operativo y realice un seguimiento de los cambios en equipos Windows y Linux. Consulte [Change Tracking](../automation/change-tracking/overview.md) y [Update Management](../automation/update-management/overview.md). |
| [Azure Information Protection ](/azure/information-protection/) | Clasifique y, opcionalmente, proteja documentos y correos electrónicos. Consulte [Informes centrales para Azure Information Protection](/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Azure Security Center](../security-center/index.yml) | Recopile y analice eventos de seguridad y complete análisis de amenazas. Consulte [Recopilación de datos en Azure Security Center](../security-center/security-center-enable-data-collection.md). |
| [Azure Sentinel](../sentinel/index.yml) | Se conecta a orígenes diferentes, incluido Office 365 y Amazon Web Services CloudTrail. Consulte [Conexión con orígenes de datos](../sentinel/connect-data-sources.md). |
| [Microsoft Intune](/intune/) | Cree una configuración de diagnóstico para enviar registros a Azure Monitor. Consulte [Envío de datos de registro al almacenamiento, a Event Hubs o a Log Analytics en Intune (versión preliminar)](/intune/fundamentals/review-logs-using-azure-monitor).  |
| Red  | [Network Performance Monitor](insights/network-performance-monitor.md): Supervisa la conectividad de red y el rendimiento de los puntos de conexión de servicios y aplicaciones.<br>[Azure Application Gateway](insights/azure-networking-analytics.md#azure-application-gateway-analytics): Analice registros y métricas de Azure Application Gateway.<br>[Análisis de tráfico](../network-watcher/traffic-analytics.md): Analiza los registros de flujo del grupo de seguridad de red (NSG) de Network Watcher para proporcionar conclusiones sobre el flujo de tráfico en la nube de Azure. |
| [Office 365](insights/solution-office-365.md) | Supervise su entorno de Office 365. Versión actualizada con incorporación mejorada disponible a través de Azure Sentinel. |
| [SQL Analytics](insights/azure-sql.md) | Supervise el rendimiento de Azure SQL Database y SQL Managed Instance a escala y entre varias suscripciones. |
| [Surface Hub](insights/surface-hubs.md) | Realice un seguimiento del estado y el uso de los dispositivos Surface Hub. |
| [System Center Operations Manager](/system-center/scom) | Recopile datos de agentes de Operations Manager mediante la conexión de su grupo de administración a Azure Monitor. Consulte [Conexión de Operations Manager con Azure Monitor](agents/om-agents.md).<br> Evalúe el riesgo y el estado del grupo de administración de System Center Operations Manager con la solución [Operations Manager Assessment](insights/scom-assessment.md). |
| [Salas de Microsoft Teams](/microsoftteams/room-systems/azure-monitor-deploy) | Administración integrada de un extremo a otro de los dispositivos de Salas de Microsoft Teams. |
| [Visual Studio App Center](/appcenter/) | Compile, pruebe y distribuya aplicaciones y, a continuación, supervise su estado y uso. Consulte [Comience a analizar la aplicación móvil con App Center y Application Insights](app/mobile-center-quickstart.md). |
| Windows | [Windows Update Compliance](/windows/deployment/update/update-compliance-get-started): Evalúe las actualizaciones del escritorio de Windows.<br>[Análisis de escritorio](/configmgr/desktop-analytics/overview): Se integra en Configuration Manager para proporcionar conclusiones e inteligencia para tomar decisiones más informadas sobre la preparación de las actualizaciones de los clientes Windows. |



## <a name="other-solutions"></a>Otras soluciones
Hay otras soluciones disponibles para supervisar diferentes aplicaciones y servicios, pero el desarrollo activo se ha detenido y puede que no estén disponibles en todas las regiones. Están cubiertas por el acuerdo de nivel de servicio de ingesta de datos de Azure Log Analytics.

| Solución | Descripción |
|:---|:---|
| [Comprobación de mantenimiento de Active Directory](insights/ad-assessment.md) | Evalúe el riesgo y el estado de los entornos de Active Directory. |
| [Estado de replicación de Active Directory](insights/ad-replication-status.md) | Supervisa periódicamente el entorno de Active Directory para comprobar si existen errores de replicación. |
| [Activity Log Analytics](essentials/activity-log.md#activity-log-analytics-monitoring-solution) | Entradas del registro de actividad de Azure. |
| [DNS Analytics (versión preliminar)](insights/dns-analytics.md) | Recopila, analiza y correlaciona los registros analíticos y de auditoría de Windows DNS y otros datos relacionados a partir de los servidores DNS. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Recopile, vea y analice las métricas de rendimiento y estado de sistema de Cloud Foundry entre varias implementaciones. |
| [Contenedores](containers/containers.md) | Vea y administre hosts de contenedor de Docker y Windows. |
| [Evaluaciones a petición](/services-hub/health/getting_started_with_on_demand_assessments) | Evalúe y optimice la disponibilidad, la seguridad y el rendimiento de los entornos de tecnología de Microsoft locales, híbridos y en la nube. |
| [Comprobación de mantenimiento de SQL](insights/sql-assessment.md) | Evalúe el riesgo y el estado de los entornos de SQL Server.  |
| [Datos de conexión](insights/wire-data.md) | Datos consolidados de rendimiento y de red recopilados de equipos conectados a Windows y Linux con el agente de Log Analytics. |

## <a name="third-party-integration"></a>Integración de terceros

| Solución | Descripción |
|:---|:---|
| [ITSM](alerts/itsmc-overview.md) | El Conector de Administración de servicios de TI (ITSMC) le permite conectar Azure y un producto o servicio de Administración de servicios de TI (ITSM) compatibles.  |


## <a name="resources-outside-of-azure"></a>Recursos fuera de Azure
Azure Monitor puede recopilar datos de recursos fuera de Azure mediante los métodos que se enumeran en la tabla siguiente.

| Recurso | Método |
|:---|:---|
| APLICACIONES | Supervise las aplicaciones web fuera de Azure mediante Application Insights. Vea [¿Qué es Application Insights?](./app/app-insights-overview.md). |
| Máquinas virtuales | Use los agentes para recopilar datos del sistema operativo invitado de máquinas virtuales en otros entornos en la nube o locales. Consulte [Información general sobre los agentes de Azure Monitor](agents/agents-overview.md). |
| Cliente de API de REST | Hay API independientes disponibles para escribir datos en Registros y Métricas de Azure Monitor desde cualquier cliente de API de REST. Consulte [Envío de datos de registro a Azure Monitor con HTTP Data Collector API (versión preliminar pública)](logs/data-collector-api.md) para Registros y [Envío de métricas personalizadas de un recurso de Azure al almacén de métricas de Azure Monitor mediante la API REST](essentials/metrics-store-custom-rest-api.md) para Métricas. |



## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre la [plataforma de datos de Azure Monitor, que almacena los registros y las métricas recopilados por conclusiones y soluciones](data-platform.md).
- Complete un [tutorial sobre la supervisión de un recurso de Azure](essentials/tutorial-resource-logs.md).
- Complete un [tutorial sobre la escritura de una consulta de registro para analizar datos en Registros de Azure Monitor](essentials/tutorial-resource-logs.md).
- Complete un [tutorial sobre la creación de un gráfico de métricas para analizar datos en Métricas de Azure Monitor](essentials/tutorial-metrics-explorer.md).

