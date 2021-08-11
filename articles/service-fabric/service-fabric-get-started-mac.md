---
title: Configuración de un entorno de desarrollo en macOS
description: Instale las herramientas, el SDK y el motor en tiempo de ejecución y cree un clúster de desarrollo local. Después de completar esta instalación, estará listo para crear aplicaciones en macOS.
ms.topic: conceptual
ms.date: 10/16/2020
ms.custom: devx-track-js
ms.openlocfilehash: 71fd869ad68164faf883fe148a47c2da4fd133b0
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110088436"
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Configuración de su entorno de desarrollo en Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [Mac OS X](service-fabric-get-started-mac.md)

Puede crear aplicaciones de Azure Service Fabric para que se ejecuten en clústeres de Linux con Mac OS X. En este documento se describe cómo configurar su Mac para desarrollo.

## <a name="prerequisites"></a>Prerrequisitos
Azure Service Fabric no se ejecuta de forma nativa en Mac OS X. Para ejecutar un clúster local de Service Fabric, se proporciona una imagen preconfigurada del contenedor de Docker. Antes de comenzar, necesitará lo siguiente:

* Cumplir los requisitos del sistema para instalar [Docker Desktop en Mac](https://docs.docker.com/docker-for-mac/install/)

* Para más información, consulte [Instalar y ejecutar Docker Desktop en Mac](https://docs.docker.com/docker-for-mac/install/#install-and-run-docker-desktop-on-mac).

>[!TIP]
>
>Para instalar Docker en el equipo Mac, siga los pasos mencionados en la [documentación de Docker](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install). Después de la instalación, puede usar Docker Desktop para establecer las preferencias, incluidos los [límites de recursos](https://docs.docker.com/docker-for-mac) y el [uso del disco](https://docs.docker.com/docker-for-mac/space/).

## <a name="create-a-local-container-and-set-up-service-fabric"></a>Creación de un contenedor local e instalación de Service Fabric
Para configurar un contenedor local de Docker y hacer que un clúster de Service Fabric se ejecute en él, realice los pasos siguientes:

1. Actualice la configuración del demonio de Docker en el host con las opciones siguientes y reinicie dicho demonio: 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    Puede actualizar estos valores directamente en el archivo daemon.json en la ruta de acceso de instalación de Docker. Puede modificar directamente los valores de configuración del demonio en Docker. Seleccione el **icono de Docker** y, a continuación, seleccione **Preferencias** > **Daemon** > **Avanzadas**.
    
    >[!NOTE]
    >
    >Se recomienda modificar el demonio directamente en Docker porque la ubicación del archivo daemon.json puede variar de una máquina a otra. Por ejemplo, ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json.
    >

    >[!TIP]
    >Cuando se prueben aplicaciones de gran tamaño, se recomienda aumentar los recursos asignados a Docker, para lo que se debe hacer clic en el **icono de Docker** y, después, seleccionar **Opciones avanzadas** para ajustar el número de núcleos y la memoria.

2. Inicie el clúster.<br/>
    <b>Ubuntu 18.04 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16.04 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > De forma predeterminada, se extraerá la imagen con la versión más reciente de Service Fabric. Para ver revisiones concretas, consulte la página [Service Fabric OneBox](https://hub.docker.com/_/microsoft-service-fabric-onebox) en Docker Hub.



3. Opcional: cree la imagen de Service Fabric extendida.

    En un directorio nuevo, cree un archivo denominado `Dockerfile` para crear la imagen personalizada:

    >[!NOTE]
    >Puede adaptar esa imagen con un Dockerfile para agregar programas o dependencias al contenedor.
    >Por ejemplo, si se agrega `RUN apt-get install nodejs -y`, será posible usar aplicaciones `nodejs` como ejecutables de invitado.
    ```Dockerfile
    FROM mcr.microsoft.com/service-fabric/onebox:u18
    RUN apt-get install nodejs -y
    EXPOSE 19080 19000 80 443
    WORKDIR /home/ClusterDeployer
    CMD ["./ClusterDeployer.sh"]
    ```
    
    >[!TIP]
    > De forma predeterminada, se extraerá la imagen con la versión más reciente de Service Fabric. Para revisiones concretas, visite la página [Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/).

    Para crear una imagen reutilizable a partir de `Dockerfile`, abra una ventana de terminal y ejecute `cd` para cambiar al directorio donde se encuentra su `Dockerfile` y, después, ejecute:

    ```bash 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Esta operación tardará un tiempo, pero solo se necesita una vez.

    Ya se puede iniciar rápidamente una copia local de Service Fabric, siempre que se necesite. Para ello, hay que ejecutar:

    ```bash 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Al especificar un nombre para la instancia del contenedor, puede tratarlo de forma más legible. 
    >
    >Si la aplicación está escuchando en determinados puertos, estos deben especificarse mediante etiquetas `-p` adicionales. Por ejemplo, si la aplicación está escuchando en el puerto 8080, agregue la siguiente etiqueta `-p`:
    >
    >`docker run -itd -p 19000:19000 -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:u18`
    >

4. El clúster tardará unos instantes en iniciarse. Una vez que se inicie puede ver los registros usando el comando siguiente o pasar al panel para ver el estado de los clústeres: `http://localhost:19080`

    ```bash 
    docker logs sftestcluster
    ```


5. Para detener y limpiar el contenedor, use el comando siguiente. De todas formas, vamos a usar este contenedor en el paso siguiente.

    ```bash 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Limitaciones conocidas 
 
 A continuación se muestran limitaciones conocidas del clúster local que se ejecuta en un contenedor para dispositivos Mac: 
 
 * El servicio DNS no se ejecuta y actualmente no se admite dentro del contenedor. [Problema n.° 132](https://github.com/Microsoft/service-fabric/issues/132)
 * La ejecución de aplicaciones basadas en contenedores requiere la ejecutar SF en un host de Linux. Actualmente no se admiten aplicaciones de contenedor anidadas.

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>Configuración de la CLI de Service Fabric (sfctl) en un Mac

Para instalar la CLI de Service Fabric (`sfctl`) en un Mac, siga las instrucciones que encontrará en [CLI de Service Fabric](service-fabric-cli.md#cli-mac).
Los comandos de la CLI permiten interactuar con las entidades de Service Fabric, lo que incluye clústeres, aplicaciones y servicios.

1. Para conectarse al clúster antes de implementar las aplicaciones, ejecute el siguiente comando. 

```bash
sfctl cluster select --endpoint http://localhost:19080
```

## <a name="create-your-application-on-your-mac-by-using-yeoman"></a>Creación de una aplicación en un equipo Mac mediante Yeoman

Service Fabric proporciona herramientas de scaffolding que le ayudarán a crear una aplicación de Service Fabric desde el terminal mediante el generador de plantillas Yeoman. Realice los pasos siguientes para asegurarse de que el generador de plantillas Yeoman de Service Fabric está en funcionamiento en la máquina:

1. Node.js y el administrador de paquetes de nodo (NPM) deben estar instalados en el equipo Mac. El software puede instalarse mediante [HomeBrew](https://brew.sh/), como se indica a continuación:

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. Instale el generador de plantillas [Yeoman](https://yeoman.io/) en la máquina desde NPM:

    ```bash
    npm install -g yo
    ```
3. Para instalar el generador Yeoman que desea usar, siga los pasos descritos en la [documentación](service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables) de introducción. Para crear aplicaciones de Service Fabric mediante Yeoman, siga estos pasos:

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. Después de instalar los generadores, cree ejecutables invitados o servicios de contenedor mediante la ejecución de `yo azuresfguest` o `yo azuresfcontainer` respectivamente.

5. Para compilar una aplicación Java de Service Fabric en Mac, JDK versión 1.8 y Gradle deberían estar instalados en la máquina host. El software puede instalarse mediante [HomeBrew](https://brew.sh/), como se indica a continuación: 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

    > [!IMPORTANT]
    > Las versiones actuales de `brew cask install java` pueden instalar una versión más reciente del JDK.
    > Asegúrese de instalar JDK 8.

## <a name="deploy-your-application-on-your-mac-from-the-terminal"></a>Implementación de la aplicación en el equipo Mac desde el terminal

Una vez que cree y compile la aplicación de Service Fabric, puede implementar su aplicación mediante la [CLI de Service Fabric](service-fabric-cli.md#cli-mac):

1. Conéctese al clúster de Service Fabric que se ejecuta dentro de la instancia del contenedor en el equipo Mac:

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Vaya al directorio del proyecto y ejecute el script de instalación:

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-31-development"></a>Configuración del desarrollo con .NET Core 3.1

Instale el [SDK de .NET Core 3.1 para Mac](https://dotnet.microsoft.com/download?initial-os=macos) para iniciar la [creación de aplicaciones de Service Fabric en C#](service-fabric-create-your-first-linux-application-with-csharp.md). Los paquetes de aplicaciones de Service Fabric en .NET Core se hospedan en NuGet.org.

## <a name="install-the-service-fabric-plug-in-for-eclipse-on-your-mac"></a>Instalación del complemento de Service Fabric para Eclipse en un equipo Mac

Azure Service Fabric proporciona un complemento de Eclipse Neon (o posterior) para el IDE de Java. El complemento simplifica el proceso de creación, compilación e implementación de servicios de Java. Para instalar la versión más reciente del complemento de Service Fabric para Eclipse o actualizar a dicha versión, siga [estos pasos](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse). Los otros pasos de la [documentación de Service Fabric para Eclipse](service-fabric-get-started-eclipse.md) también son aplicables: compilar una aplicación, agregar un servicio a una aplicación, desinstalar una aplicación, etcétera.

El último paso es crear una instancia del contenedor con una ruta de acceso que se comparte con el host. El complemento requiere este tipo de creación de instancias para funcionar con el contenedor de Docker en el equipo Mac. Por ejemplo:

```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox mcr.microsoft.com/service-fabric/onebox:latest
```

Los atributos se definen de la manera siguiente:
* `/Users/sayantan/work/workspaces/mySFWorkspace` es la ruta de acceso completa del área de trabajo en el equipo Mac.
* `/tmp/mySFWorkspace` es la ruta de acceso que está dentro del contenedor al que se debe asignar el área de trabajo.

>[!NOTE]
> 
>Si tiene un nombre o ruta de acceso diferente para el área de trabajo, actualice estos valores con el comando `docker run`.
> 
>Si inicia el contenedor con otro nombre distinto de `sfonebox`, actualice el mismo valor en el archivo testclient.sh, en su aplicación Java del actor de Service Fabric.
>

## <a name="next-steps"></a>Pasos siguientes
<!-- Links -->
* [Creación e implementación de la primera aplicación de Java para Service Fabric en Linux con Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Creación e implementación de la primera aplicación de Java para Service Fabric con el complemento de Eclipse para Service Fabric](service-fabric-get-started-eclipse.md)
* [Creación de un clúster de Service Fabric en Azure Portal](service-fabric-cluster-creation-via-portal.md)
* [Creación de un clúster de Service Fabric con Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Entender el modelo de aplicación de Service Fabric](service-fabric-application-model.md)
* [Uso de la CLI de Service Fabric para administrar las aplicaciones](service-fabric-application-lifecycle-sfctl.md)
* [Preparación del entorno de desarrollo Linux en Windows](service-fabric-local-linux-cluster-windows.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
