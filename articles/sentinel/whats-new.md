---
title: Novedades de Azure Sentinel
description: En este artículo se describen las nuevas características de Azure Sentinel de los últimos meses.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 05/26/2021
ms.openlocfilehash: 6289a142e98b347f3295b8961ee1518ce8499eb4
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/27/2021
ms.locfileid: "110539498"
---
# <a name="whats-new-in-azure-sentinel"></a>Novedades de Azure Sentinel

En este artículo se enumeran las características que se han agregado recientemente a Azure Sentinel y las nuevas características de servicios relacionados que proporcionan una experiencia de usuario mejorada en Azure Sentinel.

Si busca elementos de más de 6 meses, puede encontrarlos en las [Archivo de novedades de Azure Sentinel](whats-new-archive.md). Para más información sobre las características anteriores, consulte los [blogs de Tech Community](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New).

> [!IMPORTANT]
> Actualmente, las características indicadas están en VERSIÓN PRELIMINAR. En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

> [!TIP]
> Nuestros equipos de búsqueda de amenazas en Microsoft aportan consultas, cuadernos de estrategias, libros y cuadernos a la [comunidad de Azure Sentinel](https://github.com/Azure/Azure-Sentinel), lo que incluye [consultas de búsqueda](https://github.com/Azure/Azure-Sentinel) concretas que sus equipos pueden adaptar y usar.
>
> Usted también puede contribuir. Únase a nosotros en la [comunidad de GitHub de cazadores de amenazas de Azure Sentinel](https://github.com/Azure/Azure-Sentinel/wiki).
>

## <a name="may-2021"></a>Mayo de 2021

- [Módulo de PowerShell para Azure Sentinel](#azure-sentinel-powershell-module)
- [Mejoras en la agrupación de alertas](#alert-grouping-enhancements)
- [Soluciones de Azure Sentinel (versión preliminar pública)](#azure-sentinel-solutions-public-preview)
- [Supervisión continua de amenazas para la solución SAP (versión preliminar pública)](#continuous-threat-monitoring-for-sap-solution-public-preview)
- [Integraciones de inteligencia sobre amenazas (versión preliminar pública)](#threat-intelligence-integrations-public-preview)
- [Fusión a través de alertas programadas (versión preliminar pública)](#fusion-over-scheduled-alerts-public-preview)
- [Anomalías de SOC-ML (versión preliminar pública)](#soc-ml-anomalies-public-preview)
- [Página de entidad de IP (versión preliminar pública)](#ip-entity-page-public-preview)
- [Personalización de actividades (versión preliminar pública)](#activity-customization-public-preview)
- [Panel de búsqueda (versión preliminar pública)](#hunting-dashboard-public-preview)
- [Equipos de incidentes: Colaboración en Microsoft Teams (versión preliminar pública)](#azure-sentinel-incident-team---collaborate-in-microsoft-teams-public-preview)
- [Libro Confianza cero (TIC3.0)](#zero-trust-tic30-workbook)


### <a name="azure-sentinel-powershell-module"></a>Módulo de PowerShell para Azure Sentinel

El módulo oficial de PowerShell para Azure Sentinel para automatizar las tareas operativas diarias se ha publicado con disponibilidad general.

Puede descargarlo aquí: [Galería de PowerShell](https://www.powershellgallery.com/packages/Az.SecurityInsights/).

Para más información, consulte la documentación de PowerShell: [Az.SecurityInsights](/powershell/module/az.securityinsights/).

### <a name="alert-grouping-enhancements"></a>Mejoras en la agrupación de alertas

Ahora puede configurar la regla de análisis para agrupar las alertas en un único incidente, no solo cuando coinciden con un tipo de entidad específico, sino también cuando coinciden con un nombre de alerta, gravedad u otros detalles personalizados específicos de una entidad configurada. 

En la pestaña **Configuración de incidentes** del asistente para reglas de análisis, active la agrupación de alertas y, a continuación, seleccione la opción **Agrupar alertas en un solo incidente si los tipos de entidad seleccionados y los detalles coinciden**. 

A continuación, seleccione el tipo de entidad y los detalles pertinentes que desea que coincidan:

:::image type="content" source="media/whats-new/alert-grouping-details.png" alt-text="Agrupación de alertas por los detalles de entidad que coinciden.":::

Para más información, consulte [Agrupación de alertas](tutorial-detect-threats-custom.md#alert-grouping).

### <a name="azure-sentinel-solutions-public-preview"></a>Soluciones de Azure Sentinel (versión preliminar pública)

Azure Sentinel ofrece ahora [soluciones](sentinel-solutions-catalog.md) de **contenido empaquetado** que incluyen combinaciones de uno o varios conectores de datos, libros, reglas de análisis, cuadernos de estrategias, consultas de búsqueda, analizadores, listas de reproducción y otros componentes para Azure Sentinel.

Las soluciones proporcionan una mejor detectabilidad en el producto, una implementación de un solo paso y escenarios de producto completos. Para más información, consulte [Detección e implementación de soluciones de Azure Sentinel](sentinel-solutions-deploy.md).

### <a name="continuous-threat-monitoring-for-sap-solution-public-preview"></a>Supervisión continua de amenazas para la solución SAP (versión preliminar pública)

Las soluciones de Azure Sentinel incluyen ahora la **supervisión continua de amenazas para SAP** lo que le permite supervisar los sistemas SAP en busca de amenazas sofisticadas en el nivel empresarial y el de la aplicación.

El conector de datos de SAP genera una gran cantidad de registros de 14 aplicaciones de todo el conjunto del sistema SAP y recopila registros de Advanced Business Application Programming (ABAP) mediante llamadas a NetWeaver RFC y datos de almacenamiento de archivos mediante la interfaz de control de OSSAP. El conector de datos de SAP se agrega a la capacidad de Azure Sentinel para supervisar la infraestructura subyacente de SAP.

Para ingerir registros de SAP en Azure Sentinel, debe tener instalado el conector de datos de Azure Sentinel para SAP en el entorno de SAP. Una vez implementado el conector de datos de SAP, implemente el variado contenido de seguridad de la solución de SAP para obtener información fácilmente sobre el entorno de SAP de su organización y mejorar la funcionalidad de todas las operaciones de seguridad relacionadas.

Para más información, consulte [Tutorial: Implementación de la solución Azure Sentinel para SAP (versión preliminar pública)](sap-deploy-solution.md)

### <a name="threat-intelligence-integrations-public-preview"></a>Integraciones de inteligencia sobre amenazas (versión preliminar pública)

Azure Sentinel proporciona varias maneras diferentes de [usar fuentes de inteligencia sobre amenazas](import-threat-intelligence.md) para mejorar la capacidad de los analistas de seguridad de detectar y priorizar las amenazas conocidas.

Ahora puede usar uno de los muchos productos de la plataforma de inteligencia sobre amenazas integrada (TIP) que se acaban de publicar, conectarse a servidores TAXII para aprovechar cualquier origen de inteligencia sobre amenazas compatible con STIX y usar cualquier solución personalizada que pueda comunicarse directamente con la [API Security tiIndicators de Microsoft Graph](/graph/api/resources/tiindicator).

También puede conectarse a orígenes de inteligencia sobre amenazas a partir de cuadernos de estrategias, con el fin de enriquecer los incidentes con información de TI que puede ayudar a dirigir acciones de investigación y respuesta.

Para más información, consulte [Integración de inteligencia sobre amenazas en Azure Sentinel](threat-intelligence-integration.md).

### <a name="fusion-over-scheduled-alerts-public-preview"></a>Fusión a través de alertas programadas (versión preliminar pública)

El motor de correlación de aprendizaje automático de **Fusion** ahora puede detectar ataques de varias fases mediante las alertas que genera un conjunto de [reglas de análisis programadas](tutorial-detect-threats-custom.md) en sus correlaciones, además de las alertas importadas desde otros orígenes de datos.

Para más información, consulte [Detección avanzada de ataques de varias fases en Azure Sentinel](fusion.md).

### <a name="soc-ml-anomalies-public-preview"></a>Anomalías de SOC-ML (versión preliminar pública)

Las anomalías basadas en aprendizaje automático de SOC-ML de Azure Sentinel pueden identificar comportamientos inusuales que, de lo contrario, podrían pasar desapercibidos.

SOC-ML usa plantillas de reglas de análisis que pueden funcionar de forma inmediata. Aunque las anomalías no indican necesariamente un comportamiento malintencionado o incluso sospechoso por sí mismas, se pueden usar para mejorar la fidelidad de las detecciones, las investigaciones y la búsqueda de amenazas.

Para más información, consulte [Uso de anomalías de SOC-ML para detectar amenazas en Azure Sentinel](soc-ml-anomalies.md).

### <a name="ip-entity-page-public-preview"></a>Página de entidad de IP (versión preliminar pública)

Azure Sentinel es compatible ahora con la entidad de dirección IP y ya puede ver la información sobre esta en la nueva página de la entidad.

Al igual que las páginas de las entidades de usuario y host, la página de la entidad de dirección IP incluye información general sobre la dirección IP, una lista de las actividades de las que se ha encontrado que la dirección IP forma parte, etc., lo que le proporciona un almacén de información cada vez más completo para mejorar la investigación sobre los incidentes de seguridad.

Para más información, consulte [Páginas de entidad](identify-threats-with-entity-behavior-analytics.md#entity-pages).

### <a name="activity-customization-public-preview"></a>Personalización de actividades (versión preliminar pública)

Hablando de páginas de entidad, ahora puede crear nuevas actividades personalizadas para las entidades, de las que se realizará un seguimiento y se mostrarán en sus respectivas páginas de entidad junto con las actividades listas para usar que ha visto allí hasta ahora.

Para más información, consulte [Personalización de actividades en escalas de tiempo de páginas de entidad](customize-entity-activities.md).

### <a name="hunting-dashboard-public-preview"></a>Panel de búsqueda (versión preliminar pública)

La hoja **Búsqueda** se ha actualizado. El nuevo panel le permite ejecutar todas las consultas, o un subconjunto seleccionado, en un solo clic.

Identifique dónde desea empezar la búsqueda. Para ello, debe examinar el recuento de resultados, los picos o el cambio en el recuento de resultados en un período de 24 horas. También puede ordenar y filtrar por favoritos, origen de datos, táctica y técnica MITRE ATT&CK, resultados o diferencias de resultados. Vea las consultas que aún no tienen los orígenes de datos necesarios conectados y obtenga recomendaciones necesarias para habilitar estas consultas.

Para más información, consulte [Búsqueda de amenazas con Azure Sentinel](hunting.md).

### <a name="azure-sentinel-incident-team---collaborate-in-microsoft-teams-public-preview"></a>Equipo de incidentes de Azure Sentinel: Colaboración en Microsoft Teams (versión preliminar pública)

Azure Sentinel admite ahora una integración directa con Microsoft Teams, lo que le permite colaborar sin problemas en toda la organización y con las partes interesadas externas.

Directamente desde el incidente en Azure Sentinel, cree un nuevo *equipo de incidente* para usarlo para la comunicación central y la coordinación.

Los equipos de incidentes son especialmente útiles cuando se usan como un puente de conferencia dedicado para incidentes continuos de alta gravedad. Las organizaciones que ya usan Microsoft Teams para la comunicación y la colaboración pueden usar la integración de Azure Sentinel para aportar datos de seguridad directamente a sus conversaciones y al trabajo diario.

En Microsoft Teams, la pestaña de la **página Incidente** del nuevo equipo siempre tiene los datos más actualizados y recientes de Azure Sentinel, lo que garantiza que los equipos tengan a mano los datos más importantes.

[ ![Página Incidente en Microsoft Teams.](media/collaborate-in-microsoft-teams/incident-in-teams.jpg) ](media/collaborate-in-microsoft-teams/incident-in-teams.jpg#lightbox)

Para más información, consulte [Colaboración en Microsoft Teams (versión preliminar pública)](collaborate-in-microsoft-teams.md).

### <a name="zero-trust-tic30-workbook"></a>Libro Confianza cero (TIC3.0)

El nuevo libro Confianza cero (TIC3.0) de Azure Sentinel proporciona una visualización automatizada de los principios de [Confianza cero](/security/zero-trust/), que se cruzan con el marco de [conexiones a Internet de confianza](https://www.cisa.gov/trusted-internet-connections) (TIC).

Sabemos que el cumplimiento no es solo un requisito anual, y las organizaciones deben supervisar las configuraciones a lo largo del tiempo como punto fuerte. El libre Confianza cero de Azure Sentinel usa toda la gama de ofertas de seguridad de Microsoft en Azure, Office 365, Teams, Intune, Windows Virtual Desktop y muchas más.

[ ![Libro Confianza cero](media/zero-trust-workbook.gif) ](media/zero-trust-workbook.gif#lightbox)

**Libro Confianza cero**:

- Permite a los implementadores, analistas de SecOps, evaluadores, responsables de la toma de decisiones de seguridad y cumplimiento, MSSP y otros usuarios conocer la situación de la posición de seguridad de las cargas de trabajo en la nube.
- Ofrece más de 75 tarjetas de control, alineadas con las funcionalidades de seguridad de TIC 3.0, con botones de GUI seleccionables para la navegación.
- Está diseñado para dotar al personal de automatización, inteligencia artificial, aprendizaje automático, generación de consultas y alertas, visualizaciones, recomendaciones adaptadas y referencias a la documentación correspondiente.

Para más información, vea [Tutorial: Visualización y supervisión de los datos](tutorial-monitor-your-data.md).

## <a name="april-2021"></a>Abril de 2021

- [Conectores de datos basados en Azure Policy](#azure-policy-based-data-connectors)
- [Escala de tiempo del incidente (versión preliminar pública)](#incident-timeline-public-preview)

### <a name="azure-policy-based-data-connectors"></a>Conectores de datos basados en Azure Policy

Azure Policy permite aplicar un conjunto común de configuraciones de registros de diagnóstico a todos los recursos (actuales y futuros) de un tipo determinado cuyos registros quiera ingerir en Azure Sentinel.

Para continuar con nuestros esfuerzos para llevar la eficacia de [Azure Policy](../governance/policy/overview.md) a la tarea de configuración de recopilación de datos, ahora ofrecemos otro recopilador de datos mejorado por Azure Policy para los recursos de la [cuenta de Azure Storage](connect-azure-storage-account.md), publicado en versión preliminar pública.

Además, dos de nuestros conectores en versión preliminar, para [Azure Key Vault](connect-azure-key-vault.md) y [Azure Kubernetes Service](connect-azure-kubernetes-service.md), ahora se han publicado para disponibilidad general (GA) y se han unido al conector de [Azure SQL Database](connect-azure-sql-logs.md).

### <a name="incident-timeline-public-preview"></a>Escala de tiempo del incidente (versión preliminar pública)

La primera pestaña de la página de detalles del incidente es ahora **Escala de tiempo**, que muestra una escala de tiempo de las alertas y los marcadores del incidente. La escala de tiempo de un incidente puede ayudarle a entenderlo mejor y a reconstruir la escala temporal de la actividad del atacante con las alertas y los marcadores relacionados.

- Seleccione un elemento de la escala de tiempo para ver los detalles correspondientes, sin salir del contexto del incidente.
- Filtre el contenido de la escala de tiempo para mostrar solo las alertas o los marcadores, o bien los elementos con una táctica de MITRE o gravedad específica.
- Puede seleccionar el vínculo **Id. de alerta del sistema** para ver el registro completo o el vínculo **Eventos** para ver los eventos relacionados en el área **Registros**.

Por ejemplo:

:::image type="content" source="media/tutorial-investigate-cases/incident-timeline.png" alt-text="Pestaña de escala de tiempo del incidente":::

Para más información, consulte el [Tutorial: Investigación de incidentes con Azure Sentinel](tutorial-investigate-cases.md).

## <a name="march-2021"></a>Marzo de 2021

- [Configurar los libros para que se actualicen automáticamente en modo de vista](#set-workbooks-to-automatically-refresh-while-in-view-mode)
- [Nuevas detecciones para Azure Firewall](#new-detections-for-azure-firewall)
- [Reglas de automatización y cuadernos de estrategias desencadenados por incidentes (versión preliminar pública)](#automation-rules-and-incident-triggered-playbooks-public-preview), que incluye documentación de los nuevos cuadernos de estrategias.
- [Nuevos enriquecimientos de alertas: asignación de entidades mejorada y detalles personalizados (versión preliminar pública)](#new-alert-enrichments-enhanced-entity-mapping-and-custom-details-public-preview)
- [Imprimir los libros de Azure Sentinel o guardarlos como PDF](#print-your-azure-sentinel-workbooks-or-save-as-pdf)
- [Los filtros de incidentes y las preferencias de ordenación ahora se guardan en la sesión (versión preliminar pública)](#incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview)
- [Integración de incidentes de Microsoft 365 Defender (versión preliminar pública)](#microsoft-365-defender-incident-integration-public-preview)
- [Nuevos conectores de servicio de Microsoft con Azure Policy](#new-microsoft-service-connectors-using-azure-policy)

### <a name="set-workbooks-to-automatically-refresh-while-in-view-mode"></a>Configurar los libros para que se actualicen automáticamente en modo de vista

Los usuarios de Azure Sentinel ahora pueden usar la nueva [funcionalidad de Azure Monitor](https://techcommunity.microsoft.com/t5/azure-monitor/azure-workbooks-set-it-to-auto-refresh/ba-p/2228555) para actualizar automáticamente los datos del libro durante una sesión de visualización.

En cada libro o plantilla de libro, seleccione :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false"::: **Actualización automática** para mostrar las opciones de intervalo. Seleccione la opción que quiera usar para la sesión de visualización actual y, luego, seleccione **Aplicar**.

- Los intervalos de actualización admitidos oscilan entre los **cinco minutos** y **un día**.
- De manera predeterminada, la actualización automática está desactivada. Para optimizar el rendimiento, la actualización automática también se desactiva cada vez que se cierra un libro, y no se ejecuta en segundo plano. Vuelva a activar la actualización automática según sea necesario la próxima vez que abra el libro.
- La actualización automática se pausa mientras edita un libro y los intervalos de actualización automática se reinician cada vez que pasa al modo de vista desde el modo de edición.

    También se reinician los intervalos si actualiza manualmente el libro al seleccionar el botón :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false"::: **Actualizar**.

Para obtener más información, consulte [Tutorial: Visualizar y supervisar los datos](tutorial-monitor-your-data.md) y la [documentación de Azure Monitor](../azure-monitor/visualize/workbooks-overview.md).

### <a name="new-detections-for-azure-firewall"></a>Nuevas detecciones para Azure Firewall

Se han agregado varias detecciones integradas para Azure Firewall al área de [Análisis](import-threat-intelligence.md#analytics-puts-your-threat-indicators-to-work-detecting-potential-threats) en Azure Sentinel. Estas nuevas detecciones permiten a los equipos de seguridad recibir alertas si las máquinas de la red interna intentan consultar o conectarse a nombres de dominio de Internet o direcciones IP asociadas a indicadores de riesgo conocidos, tal como se define en la consulta de la regla de detección.

Las nuevas detecciones incluyen:

- [Señal de red Solorigate](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
- [Códigos hash y dominios GALLIUM conocidos](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
- [Known IRIDIUM IP (Dirección IP de IRIDIUM conocida)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
- [IP/dominios de grupo de Phosphorus conocidos](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
- [Dominios de THALLIUM incluidos en el ataque de DCU](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
- [Código de hash de maldoc relacionado con ZINC conocido](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)
- [Dominios de grupo de STRONTIUM conocidos](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
- [NOBELIUM, indicadores de riesgo de dominio e IP: marzo de 2021](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)


Las detecciones de Azure Firewall se agregan continuamente a la galería de plantillas integrada. Para recibir las detecciones más recientes de Azure Firewall, en **Plantillas de reglas**, filtre **Orígenes de datos** por **Azure Firewall**:

:::image type="content" source="media/whats-new/new-detections-analytics-efficiency-workbook.jpg" alt-text="Nuevas detecciones en el libro de eficiencia de Análisis":::

Para obtener más información, consulte [Nuevas detecciones para Azure Firewall en Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-network-security/new-detections-for-azure-firewall-in-azure-sentinel/ba-p/2244958).

### <a name="automation-rules-and-incident-triggered-playbooks-public-preview"></a>Reglas de automatización y cuadernos de estrategias desencadenados por incidentes (versión preliminar pública)

Las reglas de automatización son un nuevo concepto de Azure Sentinel que permite administrar de forma centralizada la automatización del control de incidentes. Además de permitirle asignar cuadernos de estrategias a incidentes (no solo a las alertas, como hasta ahora), las reglas de automatización también permiten automatizar las respuestas de varias reglas de análisis a la vez, etiquetar, asignar o cerrar incidentes automáticamente sin necesidad de cuadernos de estrategias y controlar el orden de las acciones que se ejecutan. Las reglas de automatización agilizarán el uso de la automatización en Azure Sentinel y le permitirán simplificar flujos de trabajo complejos de los procesos de orquestación de incidentes.

Consulte esta [explicación completa de las reglas de automatización](automate-incident-handling-with-automation-rules.md) para obtener más información.

Como hemos mencionado anteriormente, los cuadernos de estrategias ahora se pueden activar con el desencadenador de incidentes además del desencadenador de alertas. El desencadenador de incidentes proporciona a los cuadernos de estrategias un conjunto mayor de entradas con las que trabajar (ya que el incidente incluye también todos los datos de la entidad y la alerta), lo que ofrece más capacidad y flexibilidad en los flujos de trabajo de respuesta. Los cuadernos de estrategias desencadenados por incidentes se activan con una llamada desde las reglas de automatización.

Obtenga más información sobre las [funcionalidades mejoradas de los cuadernos de estrategias](automate-responses-with-playbooks.md) y descubra cómo [diseñar un flujo de trabajo de respuesta](tutorial-respond-threats-playbook.md) al combinar los cuadernos de estrategias y las reglas de automatización.

### <a name="new-alert-enrichments-enhanced-entity-mapping-and-custom-details-public-preview"></a>Nuevos enriquecimientos de alertas: asignación de entidades mejorada y detalles personalizados (versión preliminar pública)

Puede enriquecer las alertas de dos formas nuevas para que sean más informativas y más fáciles de usar.

Comience por llevar la asignación de entidades al siguiente nivel. Ahora puede asignar casi 20 tipos de entidades, usuarios, hosts y direcciones IP a archivos y procesos, a buzones de correo, a recursos de Azure y a dispositivos IoT. También puede usar varios identificadores para cada entidad y fortalecer así su identificación única. Esto le ofrece un conjunto de datos mucho más completo en los incidentes, proporciona una correlación más amplia y una investigación más eficaz. [Conozca la nueva forma de asignar entidades](map-data-fields-to-entities.md) en las alertas.

[Obtenga más información sobre las entidades](entities-in-azure-sentinel.md) y consulte la [lista completa de entidades disponibles y sus identificadores](entities-reference.md).

Mejore aún más sus funcionalidades de investigación y respuesta mediante la personalización de las alertas para mostrar los detalles de los eventos sin procesar. Ahora puede visibilizar el contenido del evento en los incidentes y aumentar de este modo su capacidad y flexibilidad para investigar amenazas de seguridad y responder a ellas. [Obtenga información sobre cómo exponer los detalles personalizados](surface-custom-details-in-alerts.md) en las alertas.



### <a name="print-your-azure-sentinel-workbooks-or-save-as-pdf"></a>Imprimir los libros de Azure Sentinel o guardarlos como PDF

Ahora puede imprimir los libros de Azure Sentinel, exportarlos a archivos PDF y guardarlos de forma local o compartida.

En el libro, seleccione el menú de opciones > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **Imprimir contenido**. A continuación, seleccione la impresora o elija **Guardar como PDF**, según necesite.

:::image type="content" source="media/whats-new/print-workbook.png" alt-text="Imprima el libro o guárdelo como PDF.":::

Para más información, vea [Tutorial: Visualización y supervisión de los datos](tutorial-monitor-your-data.md).

### <a name="incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview"></a>Los filtros de incidentes y las preferencias de ordenación ahora se guardan en la sesión (versión preliminar pública)

Ahora los filtros y la ordenación de incidentes se guardan en la sesión de Azure Sentinel, incluso si navega a otras áreas del producto.
Si no cambia de sesión, cuando regrese al área [Incidentes](tutorial-investigate-cases.md) de Azure Sentinel se mostrarán los filtros y la ordenación tal como los dejó.

> [!NOTE]
> Los filtros y la ordenación de incidentes no se guardan si sale de Azure Sentinel o si actualiza el explorador.

### <a name="microsoft-365-defender-incident-integration-public-preview"></a>Integración de incidentes de Microsoft 365 Defender (versión preliminar pública)

La integración de incidentes de [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) de Azure Sentinel le permite transmitir todos los incidentes de M365D a Azure Sentinel y mantenerlos sincronizados entre ambos portales. Los incidentes de M365D (anteriormente conocidos como Protección contra amenazas de Microsoft o MTP) incluyen todas las alertas, entidades e información pertinente asociadas, lo que le proporciona un contexto suficiente para realizar la evaluación de errores y una investigación preliminar en Azure Sentinel. Una vez en Sentinel, los incidentes permanecerán sincronizados de manera bidireccional con M365D, lo que le permite aprovechar las ventajas de ambos portales en su investigación de los incidentes.

El uso conjunto de Azure Sentinel y Microsoft 365 Defender ofrece lo mejor de ambos mundos. Consigue la amplitud de detalles que proporciona un sistema SIEM en todo el ámbito de los recursos de información de la organización, así como la profundidad de la potencia investigadora personalizada y adaptada que ofrece un sistema XDR para proteger los recursos de Microsoft 365, y todo ello coordinado y sincronizado para lograr un funcionamiento sin problemas del centro de operaciones de seguridad.

Para más información, consulte [Integración de Microsoft 365 Defender en Azure Sentinel](microsoft-365-defender-sentinel-integration.md).

### <a name="new-microsoft-service-connectors-using-azure-policy"></a>Nuevos conectores de servicio de Microsoft con Azure Policy

[Azure Policy](../governance/policy/overview.md) es un servicio de Azure que permite usar directivas para aplicar y controlar las propiedades de un recurso. El uso de directivas garantiza que los recursos sigan siendo compatibles con los estándares de gobernanza de TI.

Entre las propiedades de los recursos que se pueden controlar con directivas están la creación y control de los registros de diagnóstico y auditoría. Ahora, Azure Sentinel usa Azure Policy para que pueda aplicar un conjunto común de valores de registros de diagnóstico a todos los recursos (actuales y futuros) de un tipo determinado cuyos registros quiera ingerir en Azure Sentinel. Gracias a Azure Policy, ya no tendrá que establecer la configuración de registros de diagnóstico para cada recurso.

Los conectores basados en Azure Policy ahora están disponibles para los siguientes servicios de Azure:
- [Azure Key Vault](connect-azure-key-vault.md) (versión preliminar pública)
- [Azure Kubernetes Service](connect-azure-kubernetes-service.md) (versión preliminar pública)
- [Servidores e instancias de Azure SQL Database](connect-azure-sql-logs.md) (disponibilidad general)

Los clientes podrán seguir enviando manualmente los registros de instancias específicas y no tendrán que usar el motor de directivas.

## <a name="february-2021"></a>Febrero de 2021

- [Libro de certificación del modelo de madurez de ciberseguridad (CMMC)](#cybersecurity-maturity-model-certification-cmmc-workbook)
- [Conectores de datos de terceros](#third-party-data-connectors)
- [Información de UEBA en la página de entidades (versión preliminar pública)](#ueba-insights-in-the-entity-page-public-preview)
- [Búsqueda de incidentes mejorada (versión preliminar pública)](#improved-incident-search-public-preview)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>Libro de certificación del modelo de madurez de ciberseguridad (CMMC)

El libro de CMMC de Azure Sentinel proporciona un mecanismo para ver las consultas de registros alineadas con los controles de CMMC en la cartera de Microsoft, incluidas las ofertas de seguridad de Microsoft, Office 365, Teams, Intune, Windows Virtual Desktop y muchas más.

Gracias al libro de CMMC, los arquitectos de seguridad, ingenieros, analistas de operaciones de seguridad, administradores y profesionales de TI consiguen una visibilidad que les permite tomar conciencia de la situación de la posición de seguridad de las cargas de trabajo en la nube. También incluye recomendaciones para seleccionar, diseñar, implementar y configurar ofertas de Microsoft para adaptarse a los requisitos y las prácticas de CMMC correspondientes.

Incluso si no necesita cumplir con CMMC, el libro de CMMC resulta útil para crear centros de operaciones de seguridad, desarrollar alertas, visualizar amenazas y tomar conciencia de la situación de las cargas de trabajo.

Acceda al libro CMMC en el área **Libros** de Azure Sentinel. Seleccione **Plantilla** y, después, busque **CMMC**.

:::image type="content" source="media/whats-new/cmmc-guide-toggle.gif" alt-text="Active y desactive la guía de libro de CMMC" lightbox="media/whats-new/cmmc-guide-toggle.gif":::


Para más información, consulte:

- [Libro de certificación del modelo de madurez de ciberseguridad (CMMC) de Azure Sentinel](https://techcommunity.microsoft.com/t5/public-sector-blog/azure-sentinel-cybersecurity-maturity-model-certification-cmmc/ba-p/2110524)
- [Tutorial: Visualizar y supervisar los datos](tutorial-monitor-your-data.md)


### <a name="third-party-data-connectors"></a>Conectores de datos de terceros

Nuestra colección de integraciones con terceros sigue creciendo. En los dos últimos meses hemos agregado treinta conectores más. Esta es la lista:

- [Agari Phishing Defense y Agari Brand Protection](connect-agari-phishing-defense.md)
- [Akamai Security Events](connect-akamai-security-events.md)
- [Alsid para Active Directory](connect-alsid-active-directory.md)
- [Servidor HTTP de Apache](connect-apache-http-server.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [Blackberry CylancePROTECT](connect-data-sources.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Cisco Firepower eStreamer](connect-data-sources.md)
- [Cisco Meraki](connect-cisco-meraki.md)
- [Cisco Umbrella](connect-cisco-umbrella.md)
- [Cisco Unified Computing System (UCS)](connect-cisco-ucs.md)
- [ESET Enterprise Inspector](connect-data-sources.md)
- [ESET Security Management Center](connect-data-sources.md)
- [Google Workspace (anteriormente, G Suite)](connect-google-workspace.md)
- [Imperva WAF Gateway](connect-imperva-waf-gateway.md)
- [Juniper SRX](connect-juniper-srx.md)
- [Netskope](connect-data-sources.md)
- [NXLog DNS Logs](connect-nxlog-dns.md)
- [NXLog Linux Audit](connect-nxlog-linuxaudit.md)
- [Onapsis Platform](connect-data-sources.md)
- [Proofpoint On Demand Email Security (POD)](connect-proofpoint-pod.md)
- [Qualys Vulnerability Management Knowledge Base](connect-data-sources.md)
- [Servicio de Salesforce en la nube](connect-salesforce-service-cloud.md)
- [SonicWall Firewall](connect-data-sources.md)
- [Sophos Cloud Optix](connect-sophos-cloud-optix.md)
- [Squid Proxy](connect-squid-proxy.md)
- [Symantec Endpoint Protection](connect-data-sources.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Trend Micro XDR](connect-data-sources.md)
- [VMware ESXi](connect-vmware-esxi.md)

### <a name="ueba-insights-in-the-entity-page-public-preview"></a>Información de UEBA en la página de entidades (versión preliminar pública)

Las páginas de detalles de la entidad de Azure Sentinel proporcionan un [panel de información](identify-threats-with-entity-behavior-analytics.md#entity-insights), que muestra datos sobre el comportamiento de la entidad y ayuda a identificar rápidamente las anomalías y las amenazas de seguridad.

Si tiene [UEBA habilitado](ueba-enrichments.md) y ha seleccionado un período de tiempo de al menos cuatro días, este panel de información ahora incluirá también las siguientes secciones nuevas para ofrecer la información sobre UEBA:

|Sección  |Descripción  |
|---------|---------|
|**UEBA Insights** (Información de UEBA)     | Resume las actividades de usuario anómalas: <br>- Entre ubicaciones geográficas, dispositivos y entornos<br>- Entre los horizontes de tiempo y frecuencia, en comparación con el propio historial del usuario <br>- En comparación con el comportamiento de sus pares <br>- En comparación con el comportamiento de la organización     |
|**User Peers Based on Security Group Membership** (Pares del usuario en función de la pertenencia al grupo de seguridad)     |   Enumera los pares del usuario en función de la pertenencia a grupos de seguridad de Azure AD, lo que proporciona a los equipos de operaciones de seguridad una lista de otros usuarios que comparten permisos similares.  |
|**User Access Permissions to Azure Subscription** (Permisos de acceso del usuario a las suscripciones de Azure)     |     Muestra los permisos de acceso del usuario a las suscripciones de Azure a las que puede acceder directamente o a través de grupos o entidades de servicio de Azure AD.   |
|**Threat Indicators Related to The User** (Indicadores de amenaza relacionados con el usuario)     |  Enumera una colección de amenazas conocidas relacionadas con las direcciones IP representadas en las actividades del usuario. Las amenazas se enumeran por tipo y familia de amenazas, y las enriquece el servicio de inteligencia sobre amenazas de Microsoft.       |
|     |         |

### <a name="improved-incident-search-public-preview"></a>Búsqueda de incidentes mejorada (versión preliminar pública)

Hemos mejorado la experiencia de búsqueda de incidentes de Azure Sentinel, lo que le permite navegar más rápido por los incidentes a medida que investiga una amenaza específica.

Ahora puede buscar incidentes en Azure Sentinel utilizando los siguientes detalles del incidente:

- Id.
- Título
- Producto
- Propietario
- Etiqueta

## <a name="january-2021"></a>Enero de 2021

- [Asistente para reglas de análisis: experiencia mejorada de edición de consultas (versión preliminar pública)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [Módulo de PowerShell Az.SecurityInsights (versión preliminar pública)](#azsecurityinsights-powershell-module-public-preview)
- [Conector de SQL Database](#sql-database-connector)
- [Conector de Dynamics 365 (versión preliminar pública)](#dynamics-365-connector-public-preview)
- [Mejora en los comentarios de incidentes](#improved-incident-comments)
- [Clústeres de Log Analytics dedicados](#dedicated-log-analytics-clusters)
- [Identidades administradas de Logic Apps](#logic-apps-managed-identities)
- [Mejora en el ajuste de reglas con los grafos en versión preliminar de las reglas de análisis](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>Asistente para reglas de análisis: mejora en la edición de consultas (versión preliminar pública)

Ahora, el Asistente para reglas de análisis programado de Azure Sentinel ofrece las siguientes mejoras para escribir y editar consultas:

-   Una ventana de edición que se puede expandir y que proporciona más espacio en la pantalla para ver la consulta.
-   Resaltado de palabras clave en el código de la consulta.
-   Compatibilidad con Autocompletar expandida.
-   Validaciones de consultas en tiempo real. Los errores en la consulta ahora se muestran en forma de bloque rojo en la barra de desplazamiento y en forma de punto rojo en el nombre de la pestaña **Establecer la lógica de la regla**. Además, las consultas que tengan errores no se pueden guardar.

Para más información, consulte [Tutorial: Creación de reglas de análisis personalizadas para detectar amenazas](tutorial-detect-threats-custom.md).
### <a name="azsecurityinsights-powershell-module-public-preview"></a>Módulo de PowerShell Az.SecurityInsights (versión preliminar pública)

Azure Sentinel ya admite el nuevo módulo de PowerShell [Az.SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/).

El módulo **Az.SecurityInsights** admite casos de uso comunes de Azure Sentinel, como la interacción con incidentes para cambiar los estados, la gravedad, el propietario, etc., la incorporación de comentarios y etiquetas a incidentes, y la creación de marcadores.

Aunque se recomienda usar las plantillas de [Azure Resource Manager](../azure-resource-manager/templates/index.yml) para la canalización de CI/CD, el módulo **Az.SecurityInsights** es útil para las tareas posteriores a la implementación y está destinado a la automatización de SOC.  Por ejemplo, la automatización de SOC puede incluir los pasos necesarios para configurar conectores de datos, crear reglas de análisis o agregar acciones de automatización a las reglas de análisis.

Para más información, incluida una lista completa de los cmdlets disponibles y una descripción de los mismos, las descripciones y ejemplos de parámetros, consulte la [documentación de Az.SecurityInsights de PowerShell](/powershell/module/az.securityinsights/).

### <a name="sql-database-connector"></a>Conector de SQL Database

Azure Sentinel ahora incluye el conector de Azure SQL Database, que permite transmitir en secuencias los registros de auditoría y diagnóstico de bases de datos a Azure Sentinel, así como supervisar continuamente la actividad en todas las instancias.

Azure SQL es un motor de base de datos de plataforma como servicio (PaaS) totalmente administrado que se encarga de la mayoría de las funciones de administración de bases de datos, como actualizar, aplicar revisiones, crear copias de seguridad y supervisar sin la intervención del usuario.

Para más información, consulte [Conexión de los registros de auditoría y diagnóstico de bases de datos de Azure SQL](connect-azure-sql-logs.md).

### <a name="dynamics-365-connector-public-preview"></a>Conector de Dynamics 365 (versión preliminar pública)

Ahora Azure Sentinel proporciona un conector para Microsoft Dynamics 365, que le permite recopilar los registros de actividad de los usuarios, administradores y de soporte técnico de las aplicaciones Dynamics 365 en Azure Sentinel. Puede usar estos datos para ayudarle a auditar la totalidad de las acciones de procesamiento de datos que se realizan y analizarlas para localizar posibles infracciones de seguridad.

Para obtener más información, consulte [Conexión de los registros de actividad de Dynamics 365 a Azure Sentinel](connect-dynamics-365.md).

### <a name="improved-incident-comments"></a>Mejora en los comentarios de incidentes

Los analistas usan estos comentarios para colaborar en los incidentes, así como documentar procesos y pasos manualmente o como parte de un cuaderno de estrategias. 

La mejora en nuestra experiencia en comentarios de incidentes permite no solo dar formato a los comentarios, sino también editar o eliminar los comentarios existentes.

Para más información, consulte [Creación automática de incidentes a partir de alertas de seguridad de Microsoft](create-incidents-from-alerts.md).
### <a name="dedicated-log-analytics-clusters"></a>Clústeres de Log Analytics dedicados

Azure Sentinel ahora admite clústeres de Log Analytics dedicados como opción de implementación. Se recomienda considerar la posibilidad de usar un clúster dedicado si:

- **Ingiere más de 1 TB al día** en el área de trabajo de Azure Sentinel.
- **Tiene varias áreas de trabajo de Azure Sentinel** en una inscripción de Azure.

Los clústeres dedicados le permiten usar características como claves administradas por el cliente, caja de seguridad, cifrado doble y consultas entre áreas de trabajo rápidas cuando varias áreas de trabajo están en el mismo clúster.

Para más información, consulte [Clústeres dedicados de registros de Azure Monitor](../azure-monitor/logs/logs-dedicated-clusters.md).

### <a name="logic-apps-managed-identities"></a>Identidades administradas de Logic Apps

Azure Sentinel ahora admite identidades administradas para el conector de Logic Apps de Azure Sentinel, lo que permite conceder permisos directamente a un cuaderno de estrategias específico para que funcione en Azure Sentinel, en lugar de crear identidades adicionales.

- **Sin una identidad administrada**, el conector de Logic Apps requiere una identidad independiente con un rol RBAC de Azure Sentinel para ejecutarse en Azure Sentinel. Esta identidad independiente puede ser un usuario de Azure AD o una entidad de servicio, como una aplicación registrada de Azure AD.

- **Al activar la compatibilidad con identidades administradas en una aplicación lógica**, la aplicación lógica se registra en Azure AD y se proporciona un identificador de objeto. Use el identificador de objeto en Azure Sentinel para asignar la aplicación lógica con un rol RBAC de Azure en el área de trabajo de Azure Sentinel. 

Para más información, consulte:

- [Autenticación con Identidad administrada en Azure Logic Apps](../logic-apps/create-managed-service-identity.md)
- [Documentación del conector de Logic Apps de Azure Sentinel](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>Mejora en el ajuste de reglas con los grafos de versión preliminar de reglas de análisis (versión preliminar pública)

Azure Sentinel le permite ahora ajustar mejor las reglas de análisis, lo que le ayuda a aumentar su precisión y reducir el ruido.

Después de editar una regla de análisis en la pestaña **Establecer la lógica de la regla**, busque el área **Results simulation** (Simulación de resultados) de la derecha. 

Seleccione **Test with current data** (Probar con datos actuales) para que Azure Sentinel ejecute una simulación de las últimas 50 ejecuciones de una regla de análisis. Se genera un grafo para mostrar el número promedio de alertas que habría generado la regla, en función de los datos de eventos sin procesar que se han evaluado. 

Para más información, consulte [Definición de la lógica de consulta de regla y configuración de los valores](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings).

## <a name="december-2020"></a>Diciembre de 2020

- [80 nuevas consultas de búsqueda integradas](#80-new-built-in-hunting-queries)
- [Mejoras en el agente de Log Analytics](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 nuevas consultas de búsqueda integradas
 
Las consultas de búsqueda integradas de Azure Sentinel permiten a los analistas de SOC reducir las lagunas en la cobertura de detección actual y lograr nuevos clientes potenciales de búsqueda.

Esta actualización de Azure Sentinel incluye nuevas consultas de búsqueda que proporcionan cobertura en la matriz de la plataforma MITRE ATT&CK:

- **Colección**
- **Comando y control**
- **Acceso de credenciales**
- **Detección**
- **Ejecución**
- **Exfiltración**
- **Impacto**
- **Acceso inicial**
- **Persistencia**
- **Elevación de privilegios**

Las consultas de búsqueda agregadas están diseñadas para ayudarle a encontrar actividades sospechosas en su entorno. Aunque pueden devolver actividades legítimas y actividades potencialmente malintencionadas, pueden resultar útiles como guía para la búsqueda. 

Si, después de ejecutar estas consultas, confía en los resultados, puede convertirlos en reglas de análisis, o bien agregar resultados de búsqueda a los incidentes nuevos o existentes.

Todas las consultas agregadas están disponibles a través de la página de búsqueda de Azure Sentinel. Para más información, consulte [Búsqueda de amenazas con Azure Sentinel](hunting.md).

### <a name="log-analytics-agent-improvements"></a>Mejoras en el agente de Log Analytics

Los usuarios de Azure Sentinel se benefician de las siguientes mejoras en el agente de Log Analytics:

- **Compatibilidad con más sistemas operativos**, entre los que se incluyen CentOS 8, RedHat 8 y SUSE Linux 15.
- **Compatibilidad con Python 3**, además de con Python 2

Azure Sentinel usa el agente de Log Analytics para enviar eventos al área de trabajo, lo que incluye eventos de seguridad de Windows, los eventos de Syslog, los registros de CEF, etc.

> [!NOTE]
> Al agente de Log Analytics, a veces se le denomina Agente de OMS o Microsoft Monitoring Agent (MMA). 
> 

Para más información, consulte la [documentación de Log Analytics](../azure-monitor/agents/log-analytics-agent.md) y las [notas de la versión del agente de Log Analytics](https://github.com/microsoft/OMS-Agent-for-Linux/releases).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
>[Incorporación de Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Obtención de visibilidad sobre las alertas](quickstart-get-visibility.md)
