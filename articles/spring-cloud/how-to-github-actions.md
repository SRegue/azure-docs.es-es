---
title: CI/CD de Azure Spring Cloud con Acciones de GitHub
description: Creación de un flujo de trabajo de CI/CD para Azure Spring Cloud con Acciones de GitHub
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 2cdbec553909b354cc85e15d2b33c731c67e12b7
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015516"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>CI/CD de Azure Spring Cloud con Acciones de GitHub

Acciones de GitHub admite un flujo de trabajo del ciclo de vida de desarrollo de software automatizado. Con Acciones de GitHub para Azure Spring Cloud puede crear flujos de trabajo en el repositorio para compilar, probar, empaquetar, publicar e implementar en Azure.

## <a name="prerequisites"></a>Requisitos previos

En este ejemplo se requiere la [CLI de Azure](/cli/azure/install-azure-cli).

::: zone pivot="programming-language-csharp"
## <a name="set-up-github-repository-and-authenticate"></a>Configuración y autenticación de repositorios de GitHub

Para autorizar la acción de inicio de sesión de Azure se necesita una credencial de entidad de servicio de Azure. Para obtener una credencial de Azure, ejecute los siguientes comandos en una máquina local:

```azurecli
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

Para acceder a un grupo de recursos concreto, puede reducir el ámbito:

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

El comando debe generar un objeto JSON:

```json
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

En este ejemplo se usa el [ejemplo de Steeltoe en GitHub](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample).  Bifurque el repositorio, abra la página del repositorio de GitHub de la bifurcación y seleccione la pestaña **Configuración**. Abra el menú **Secretos** y seleccione **Nuevo secreto**:

![Agregar un nuevo secreto](./media/github-actions/actions1.png)

Establezca el nombre del secreto en `AZURE_CREDENTIALS` y su valor en la cadena JSON que encontró en el encabezado *Configuración y autenticación del repositorio de GitHub*.

![Establecer datos de secreto](./media/github-actions/actions2.png)

También puede obtener la credencial de inicio de sesión de Azure desde Key Vault en Acciones de GitHub, como se explica en [Autenticación de Azure Spring con Key Vault en Acciones de GitHub](./github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Aprovisionamiento de una instancia de servicio

Para aprovisionar una instancia del servicio Azure Spring Cloud, ejecute los siguientes comandos mediante la CLI de Azure.

```azurecli
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/Azure-Spring-Cloud-Samples --label main --search-paths steeltoe-sample/config
```

## <a name="build-the-workflow"></a>Compilación del flujo de trabajo

El flujo de trabajo se define con las siguientes opciones.

### <a name="prepare-for-deployment-with-azure-cli"></a>Preparación de la implementación con la CLI de Azure

Actualmente, el comando `az spring-cloud app create` no es idempotente. Después de ejecutarlo una vez, recibirá un error si vuelve a ejecutar el mismo comando. Este flujo de trabajo se recomienda en las instancias y aplicaciones de Azure Spring Cloud existentes.

Use los siguientes comandos de la CLI de Azure para la preparación:

```azurecli
az config set defaults.group=<service group name>
az config set defaults.spring-cloud=<service instance name>
az spring-cloud app create --name planet-weather-provider
az spring-cloud app create --name solar-system-weather
```

### <a name="deploy-with-azure-cli-directly"></a>Implementación directa con la CLI de Azure

Cree el archivo *.github/workflows/main.yml* en el repositorio con el siguiente contenido. Reemplace *\<your resource group name>* y *\<your service name>* por los valores correctos.

```yaml
name: Steeltoe-CD

# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the main branch
on:
  push:
    branches: [ main]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    env:
      working-directory: ./steeltoe-sample
      resource-group-name: <your resource group name>
      service-name: <your service name>

    # Supported .NET Core version matrix.
    strategy:
      matrix:
        dotnet: [ '3.1.x' ]

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2

      # Set up .NET Core 3.1 SDK
      - uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ matrix.dotnet }}

      # Set credential for az login
      - uses: azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: install Azure CLI extension
        run: |
          az extension add --name spring-cloud --yes

      - name: Build and package planet-weather-provider app
        working-directory: ${{env.working-directory}}/src/planet-weather-provider
        run: |
          dotnet publish
          az spring-cloud app deploy -n planet-weather-provider --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll --artifact-path ./publish-deploy-planet.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
      - name: Build solar-system-weather app
        working-directory: ${{env.working-directory}}/src/solar-system-weather
        run: |
          dotnet publish
          az spring-cloud app deploy -n solar-system-weather --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll --artifact-path ./publish-deploy-solar.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
```

::: zone-end

::: zone pivot="programming-language-java"

## <a name="set-up-github-repository-and-authenticate"></a>Configuración y autenticación de repositorios de GitHub

Para autorizar la acción de inicio de sesión de Azure se necesita una credencial de entidad de servicio de Azure. Para obtener una credencial de Azure, ejecute los siguientes comandos en una máquina local:

```azurecli
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

Para acceder a un grupo de recursos concreto, puede reducir el ámbito:

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

El comando debe generar un objeto JSON:

```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

Aquí se usa el ejemplo de [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) de GitHub.  Bifurque el ejemplo, abra la página del repositorio de GitHub y seleccione la pestaña **Configuración**. Abra el menú **Secretos** y seleccione **Agregar un nuevo secreto**:

![Agregar un nuevo secreto](./media/github-actions/actions1.png)

Establezca el nombre del secreto en `AZURE_CREDENTIALS` y su valor en la cadena JSON que encontró en el encabezado *Configuración y autenticación del repositorio de GitHub*.

![Establecer datos de secreto](./media/github-actions/actions2.png)

También puede obtener la credencial de inicio de sesión de Azure desde Key Vault en Acciones de GitHub, como se explica en [Autenticación de Azure Spring con Key Vault en Acciones de GitHub](./github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Aprovisionamiento de una instancia de servicio

Para aprovisionar una instancia del servicio Azure Spring Cloud, ejecute los siguientes comandos mediante la CLI de Azure.

```azurecli
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```

## <a name="build-the-workflow"></a>Compilación del flujo de trabajo

El flujo de trabajo se define con las siguientes opciones.

### <a name="prepare-for-deployment-with-azure-cli"></a>Preparación de la implementación con la CLI de Azure
Actualmente, el comando `az spring-cloud app create` no es idempotente.  Este flujo de trabajo se recomienda en las instancias y aplicaciones de Azure Spring Cloud existentes.

Use los siguientes comandos de la CLI de Azure para la preparación:

```azurecli
az config set defaults.group=<service group name>
az config set defaults.spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Implementación directa con la CLI de Azure

Cree el archivo *.github/workflow/main.yml* en el repositorio:

```yaml
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:

    - uses: actions/checkout@main

    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8

    - name: maven build, clean
      run: |
        mvn clean package -DskipTests

    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud

    - name: Deploy with AZ CLI commands
      run: |
        az config set defaults.group=$GROUP
        az config set defaults.spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```

### <a name="deploy-with-azure-cli-action"></a>Implementación con una acción de la CLI de Azure

El comando az `run` usará la versión más reciente de la CLI de Azure. Si hay cambios importantes, también puede usar una versión específica de la CLI de Azure con azure/CLI `action`.

> [!Note]
> Este comando se ejecutará en un contenedor nuevo, por lo que `env` no funcionará y es posible que el acceso a archivos entre acciones tenga restricciones adicionales.

Cree el archivo *.github/workflow/main.yml* en el repositorio:

```yaml
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:

    - uses: actions/checkout@main

    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8

    - name: maven build, clean
      run: |
        mvn clean package -DskipTests

    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az config set defaults.group=<service group name>
          az config set defaults.spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>Implementación con el complemento Maven

Otra opción es usar el [complemento Maven](./quickstart.md) para implementar el archivo .jar y actualizar la configuración de la aplicación. El comando `mvn azure-spring-cloud:deploy` es idempotente y creará automáticamente las aplicaciones si fuera necesario. No es preciso crear las aplicaciones correspondientes de antemano.

```yaml
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:

    - uses: actions/checkout@main

    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8

    - name: maven build, clean
      run: |
        mvn clean package -DskipTests

    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

::: zone-end

## <a name="run-the-workflow"></a>Ejecución del flujo de trabajo

**Acciones** de GitHub se debe habilitar automáticamente después de insertar *.github/workflow/main.yml* en GitHub. La acción se desencadenará al insertar una nueva confirmación. Si crea este archivo en el explorador, la acción ya debería haberse ejecutado.

Para comprobar que la acción se ha habilitado, seleccione la pestaña **Acciones** de la página del repositorio de GitHub:

![Comprobar acción habilitado](./media/github-actions/actions3.png)

Si al ejecutarse la acción aparece un error, por ejemplo, si no ha establecido la credencial de Azure, puede volver a ejecutar las comprobaciones después de corregir el error. En la página del repositorio de GitHub, seleccione **Acciones**, la tarea del flujo de trabajo concreta y luego el botón **Volver a ejecutar comprobaciones** para volver a ejecutar las comprobaciones:

![Volver a ejecutar comprobaciones](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Pasos siguientes

* [Key Vault para Acciones de GitHub en Spring Cloud](./github-actions-key-vault.md)
* [Entidades de servicio de Azure Active Directory](/cli/azure/ad/sp#az_ad_sp_create_for_rbac)
* [Acciones de GitHub para Azure](https://github.com/Azure/actions/)
