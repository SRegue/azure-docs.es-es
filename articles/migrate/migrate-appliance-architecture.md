---
title: Arquitectura del dispositivo de Azure Migrate
description: Proporciona información general sobre el dispositivo de Azure Migrate usado en la detección, evaluación y migración del servidor.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 78b62c91148fc7a2c202cbf6792c1de442c142cc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742027"
---
# <a name="azure-migrate-appliance-architecture"></a>Arquitectura del dispositivo de Azure Migrate

En este artículo se describen los procesos y la arquitectura del dispositivo de Azure Migrate. El dispositivo de Azure Migrate es ligero y se implementa de forma local para detectar servidores para su migración a Azure.

## <a name="deployment-scenarios"></a>Escenarios de implementación

El dispositivo Azure Migrate se usa en los escenarios siguientes.

**Escenario** | **Herramienta** | **Se usa para**
--- | --- | ---
**Detección y evaluación de servidores que se ejecutan en el entorno de VMware** | Azure Migrate: Detección y evaluación | Detectar servidores que se ejecutan en el entorno de VMware.<br/><br/> Realizar la detección del inventario de software instalado, las aplicaciones web de ASP.NET, las instancias y bases de datos de SQL Server, y el análisis de dependencias sin agente.<br/><br/> Recopilar la configuración del servidor y los metadatos de rendimiento para las evaluaciones.
**Migración sin agente de servidores que se ejecutan en el entorno de VMware** | Server Migration de Azure Migrate | Detectar servidores que se ejecutan en el entorno de VMware.<br/><br/> Replicar servidores sin necesidad de instalar ningún agente en ellos.
**Detección y evaluación de servidores que se ejecutan en el entorno de Hyper-V** | Azure Migrate: Detección y evaluación | Detectar servidores que se ejecutan en el entorno de Hyper-V.<br/><br/> Recopilar la configuración del servidor y los metadatos de rendimiento para las evaluaciones.
**Detección y evaluación de servidores físicos o virtualizados locales** |  Azure Migrate: Detección y evaluación |  Detectar servidores físicos o virtualizados locales.<br/><br/> Recopilar la configuración del servidor y los metadatos de rendimiento para las evaluaciones.

## <a name="deployment-methods"></a>Métodos de implementación

Para implementar el dispositivo se pueden usar un par de métodos:

- El dispositivo se puede implementar mediante una plantilla para servidores que se ejecutan en los entornos de VMware o Hyper-V ([plantilla OVA en el caso de VMware](how-to-set-up-appliance-vmware.md) o [VHD en el de Hyper-V](how-to-set-up-appliance-hyper-v.md)).
- Si no quiere usar una plantilla, puede implementar el dispositivo para los entornos de VMware o Hyper-V mediante un [script del instalador de PowerShell](deploy-appliance-script.md).
- En Azure Government, el dispositivo se debe implementar mediante un script del instalador de PowerShell. Consulte los pasos de la implementación [aquí](deploy-appliance-script-government.md).
- En el caso de los servidores físicos o virtualizados locales o de cualquier otra nube, el dispositivo siempre se implementa mediante un script del instalador de PowerShell. Consulte los pasos de la implementación [aquí](how-to-set-up-appliance-physical.md).
- Los vínculos de descarga están disponibles en las tablas siguientes.

## <a name="appliance-services"></a>Servicios del dispositivo

El dispositivo tiene los servicios siguientes:

- **Administrador de configuración del dispositivo**: se trata de una aplicación web que se puede configurar con detalles de origen para iniciar la detección y la evaluación de los servidores.
- **Agente de detección**: el agente recopila los metadatos de configuración del servidor que se pueden usar para crear evaluaciones locales.
- **Agente de evaluación**: el agente recopila los metadatos de rendimiento del servidor que se pueden usar para crear evaluaciones basadas en el rendimiento.
- **Servicio de actualización automática**: el servicio mantiene actualizados todos los agentes que se ejecutan en el dispositivo. Se ejecuta automáticamente una vez cada 24 horas.
- **gente DRA**: orquesta la replicación del servidor y coordina la comunicación entre los servidores replicados y Azure. Solo se usa al replicar servidores en Azure mediante la migración sin agente.
- **Puerta de enlace**: Envía los datos replicados a Azure. Solo se usa al replicar servidores en Azure mediante la migración sin agente.
- **Agente de detección y evaluación de SQL**: envía los metadatos de configuración y rendimiento de las instancias y bases de datos de SQL Server a Azure.
- **Agente de detección y evaluación de aplicaciones web**: envía los datos de configuración de las aplicaciones web a Azure.

> [!Note]
> Los cuatro últimos servicios solo están disponibles en el dispositivo que se usa para la detección y evaluación de los servidores que se ejecutan en el entorno de VMware.

## <a name="discovery-and-collection-process"></a>Proceso de detección y recopilación

:::image type="content" source="./media/migrate-appliance-architecture/architecture1.png" alt-text="Arquitectura del dispositivo":::

El dispositivo se comunica con los orígenes de detección mediante el siguiente proceso.

**Proceso** | **Dispositivo de VMware** | **Dispositivo de Hyper-V** | **Dispositivo físico**
---|---|---|---
**Inicio de la detección** | De forma predeterminada, el dispositivo se comunica con la instancia de vCenter Server en el puerto TCP 443. Si vCenter Server escucha en otro puerto, puede configurarlo en administrador de configuración del dispositivo. | El dispositivo se comunica con los hosts de Hyper-V en el puerto 5985 (HTTP) de WinRM. | El dispositivo se comunica con los servidores de Windows a través del puerto de WinRM 5985 (HTTP) con los servidores de Linux a través del puerto 22 (TCP).
**Recopilación de metadatos de configuración y rendimiento** | El dispositivo recopila los metadatos de los servidores que se ejecutan en vCenter Server mediante las API de vSphere; para ello, se conecta al puerto 443 (puerto predeterminado) o a cualquier otro puerto que vCenter Server escuche. | El dispositivo recopila los metadatos de los servidores que se ejecutan en hosts de Hyper-V mediante una sesión del Modelo de información común (CIM) con hosts en el puerto 5985.| El dispositivo recopila metadatos de servidores de Windows mediante la sesión del Modelo de información común (CIM) con servidores en el puerto 5985 y desde servidores de Linux mediante la conectividad SSH en el puerto 22.
**Enviar datos de detección** | El dispositivo envía los datos recopilados a Azure Migrate: Detección y evaluación y Azure Migrate: Server Migration a través del puerto SSL 443.<br/><br/>  El dispositivo puede conectarse a Azure a través de Internet o mediante emparejamiento privado ExpressRoute o circuitos de emparejamiento de Microsoft. | El dispositivo envía los datos recopilados a Azure Migrate: Detección y evaluación a través del puerto SSL 443.<br/><br/> El dispositivo puede conectarse a Azure a través de Internet o mediante emparejamiento privado ExpressRoute o circuitos de emparejamiento de Microsoft. | El dispositivo envía los datos recopilados a Azure Migrate: Detección y evaluación a través del puerto SSL 443.<br/><br/> El dispositivo puede conectarse a Azure a través de Internet o mediante emparejamiento privado ExpressRoute o circuitos de emparejamiento de Microsoft. 
**Frecuencia de recopilación de datos** | Los metadatos de configuración se recopilan y se envían cada 30 minutos. <br/><br/> Los metadatos de rendimiento se recopilan cada 20 segundos y se agregan para enviar un punto de datos a Azure cada 10 minutos. <br/><br/> Los datos de inventario de software se envían a Azure una vez cada 12 horas. <br/><br/> Los datos de dependencia sin agente se recopilan cada 5 minutos, se agregan en el dispositivo y se envían a Azure cada 6 horas. <br/><br/> Los datos de configuración de SQL Server se actualizan una vez cada 24 horas y los datos de rendimiento se capturan cada 30 segundos. <br/><br/> Los datos de configuración de las aplicaciones web se actualizan una vez cada 24 horas. Los datos de rendimiento no se capturan en las aplicaciones web.| Los metadatos de configuración se recopilan y se envían cada 30 minutos. <br/><br/> Los metadatos de rendimiento se recopilan cada 30 segundos y se agregan para enviar un punto de datos a Azure cada 10 minutos.|  Los metadatos de configuración se recopilan y se envían cada 30 minutos. <br/><br/> Los metadatos de rendimiento se recopilan cada 5 minutos y se agregan para enviar un punto de datos a Azure cada 10 minutos.
**Evaluación y migración** | Puede crear evaluaciones a partir de los metadatos que haya recopilado el dispositivo mediante la herramienta Azure Migrate: Detección y evaluación.<br/><br/>Asimismo, también puede comenzar a migrar servidores que se ejecutan en el entorno de VMware mediante la herramienta Azure Migrate: Server Migration para organizar la replicación de servidores sin agente.| Puede crear evaluaciones a partir de los metadatos que haya recopilado el dispositivo mediante la herramienta Azure Migrate: Detección y evaluación. | Puede crear evaluaciones a partir de los metadatos que haya recopilado el dispositivo mediante la herramienta Azure Migrate: Detección y evaluación.

## <a name="next-steps"></a>Pasos siguientes

[Revise](migrate-appliance.md) la matriz de compatibilidad del dispositivo.