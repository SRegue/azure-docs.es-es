---
title: 'Kubernetes en el tutorial de Azure: Creación de un registro de contenedor'
description: En este tutorial de Azure Kubernetes Service (AKS) se crea una instancia de Azure Container Registry y se carga una imagen del contenedor de aplicaciones de ejemplo.
services: container-service
ms.topic: tutorial
ms.date: 05/20/2021
ms.custom: mvc, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 157aae551f4fa77c4739670081804af1b8dbf16b
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/29/2021
ms.locfileid: "110697771"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>Tutorial: Implementación y uso de Azure Container Registry

Azure Container Registry (ACR) es un registro privado para imágenes de contenedor. Un registro de contenedor privado permite compilar e implementar aplicaciones y código personalizado de forma segura. En este tutorial, que es la segunda de las siete partes, se implementa una instancia de ACR y se insertar una imagen de contenedor en ella. Aprenderá a:

> [!div class="checklist"]
> * Crear una instancia de Azure Container Registry (ACR)
> * Etiquetar una imagen de contenedor para ACR
> * Cargar la imagen a ACR
> * Ver las imágenes del registro

En tutoriales posteriores, esta instancia de ACR se integra con un clúster de Kubernetes en AKS y se implementa una aplicación a partir de la imagen.

## <a name="before-you-begin"></a>Antes de empezar

En el [tutorial anterior][aks-tutorial-prepare-app], se creó una imagen de contenedor para una aplicación sencilla de Azure Voting. Si no ha creado la imagen de la aplicación de Azure Voting, vuelva al [Tutorial 1: creación de imágenes de contenedor][aks-tutorial-prepare-app].

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para realizar este tutorial es necesario ejecutar la versión 2.0.53, o superior, de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure][azure-cli-install].

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Para realizar este tutorial es preciso ejecutar la versión 5.9.0 de Azure PowerShell, o cualquier versión posterior. Ejecute `Get-InstalledModule -Name Az` para encontrar la versión. Si necesita instalarla o actualizarla, consulte el artículo sobre la [instalación de Azure PowerShell][azure-powershell-install].

---

## <a name="create-an-azure-container-registry"></a>Creación de una instancia de Azure Container Registry

Para crear una instancia de Azure Container Registry, es preciso tener antes un grupo de recursos. Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para crear un grupo de recursos, use el comando [az group create][az-group-create]. En el siguiente ejemplo, se crea un grupo de recursos denominado *myResourceGroup* en la región *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Cree una instancia de Azure Container Registry con el comando [crear az acr][az-acr-create] y especifique su propio nombre de registro. El nombre del registro debe ser único dentro de Azure y contener entre 5 y 50 caracteres alfanuméricos. En el resto del tutorial, se usa `<acrName>` como marcador de posición del nombre del registro de contenedor. Proporcione su propio nombre único de registro. La SKU *básica* es un punto de entrada optimizado para costo con fines de desarrollo que proporciona un equilibrio entre almacenamiento y rendimiento.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Cree un grupo de recursos con el cmdlet [New-AzResourceGroup][new-azresourcegroup]. En el siguiente ejemplo, se crea un grupo de recursos denominado *myResourceGroup* en la región *eastus*:

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

Cree una instancia de Azure Container Registry con el cmdlet [New-AzContainerRegistry][new-azcontainerregistry] y especifique su propio nombre de registro. El nombre del registro debe ser único dentro de Azure y contener entre 5 y 50 caracteres alfanuméricos. En el resto del tutorial, se usa `<acrName>` como marcador de posición del nombre del registro de contenedor. Proporcione su propio nombre único de registro. La SKU *básica* es un punto de entrada optimizado para costo con fines de desarrollo que proporciona un equilibrio entre almacenamiento y rendimiento.

```azurepowershell
New-AzContainerRegistry -ResourceGroupName myResourceGroup -Name <acrname> -Sku Basic
```

---

## <a name="log-in-to-the-container-registry"></a>Inicio de sesión en el registro de contenedor

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para usar la instancia de ACR, primero debe iniciar sesión. Use el comando [az acr login][az-acr-login] y especifique el nombre único que dio al registro de contenedor en el paso anterior.

```azurecli
az acr login --name <acrName>
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Para usar la instancia de ACR, primero debe iniciar sesión. Use el cmdlet [Connect-AzContainerRegistry][connect-azcontainerregistry] y especifique el nombre único otorgado al registro de contenedor en el paso anterior.

```azurepowershell
Connect-AzContainerRegistry -Name <acrName>
```

---

Al finalizar, el comando devuelve un mensaje que indica que el *inicio de sesión se ha realizado correctamente*.

## <a name="tag-a-container-image"></a>Etiquetado de una imagen de contenedor

Para ver una lista de las imágenes locales actuales, use el comando [docker images][docker-images]:

```console
docker images
```
La salida del comando anterior muestra una lista de las imágenes locales actuales:

```output
REPOSITORY                                     TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front   v1                  84b41c268ad9        7 minutes ago       944MB
mcr.microsoft.com/oss/bitnami/redis            6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                     python3.6           a16ce562e863        6 weeks ago         944MB
```

Para usar la imagen de contenedor *azure-vote-front* con ACR, se debe etiquetar con la dirección del servidor de inicio de sesión del registro de imagen. Esta etiqueta se usa para el enrutamiento al insertar imágenes de contenedor en un registro de imágenes.

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para obtener la dirección del servidor de inicio de sesión, use el comando [az acr list][az-acr-list] y consulte *loginServer* como se indica a continuación:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```
### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Para obtener la dirección del servidor de inicio de sesión, use el cmdlet [Get-AzContainerRegistry][get-azcontainerregistry] y consulte el elemento *loginServer* como se indica a continuación:

```azurepowershell
(Get-AzContainerRegistry -ResourceGroupName myResourceGroup -Name <acrName>).LoginServer
```

---

Ahora, etiquete la imagen *azure-vote-front* local con la dirección *acrLoginServer* del registro de contenedor. Para indicar la versión de la imagen, agregue *:v1* al final de su nombre:

```console
docker tag mcr.microsoft.com/azuredocs/azure-vote-front:v1 <acrLoginServer>/azure-vote-front:v1
```

Para comprobar que se aplican las etiquetas, vuelva a ejecutar [docker images][docker-images].

```console
docker images
```

Una imagen se etiqueta con la dirección de la instancia de ACR y un número de versión.

```
REPOSITORY                                      TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front    v1                  84b41c268ad9        16 minutes ago      944MB
mycontainerregistry.azurecr.io/azure-vote-front v1                  84b41c268ad9        16 minutes ago      944MB
mcr.microsoft.com/oss/bitnami/redis             6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                      python3.6           a16ce562e863        6 weeks ago         944MB
```

## <a name="push-images-to-registry"></a>Inserción de imágenes en el registro

Con la imagen generada y etiquetada, inserte la imagen *azure-vote-front* en la instancia de ACR. Use [docker push][docker-push] y especifique su propia dirección *acrLoginServer* para el nombre de la imagen, tal como se muestra a continuación:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

La inserción de la imagen en ACR puede tardar varios minutos en completarse.

## <a name="list-images-in-registry"></a>Lista de imágenes en el registro

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para que se devuelva una lista de las imágenes que se han insertado en la instancia de ACR, el usuario debe utilizar el comando [az acr repository list][az-acr-repository-list]. Escriba su propio `<acrName>` como se indica a continuación:

```azurecli
az acr repository list --name <acrName> --output table
```

En la siguiente salida de ejemplo se muestra la imagen *azure-vote-front* como disponible en el registro:

```output
Result
----------------
azure-vote-front
```

Para ver las etiquetas de una imagen concreta, use el comando [az acr repository show-tags][az-acr-repository-show-tags] como se indica a continuación:

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

La salida de ejemplo siguiente muestra la imagen de *v1* etiquetada en un paso anterior:

```output
Result
--------
v1
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Para que se devuelva una lista de las imágenes que se han insertado en la instancia de ACR, use el cmdlet [Get-AzContainerRegistryManifest][get-azcontainerregistrymanifest]. Escriba su propio `<acrName>` como se indica a continuación:

```azurepowershell
Get-AzContainerRegistryManifest -RegistryName <acrName> -RepositoryName azure-vote-front
```

En la siguiente salida de ejemplo se muestra la imagen *azure-vote-front* como disponible en el registro:

```output
Registry  ImageName        ManifestsAttributes
--------  ---------        -------------------
<acrName> azure-vote-front {Microsoft.Azure.Commands.ContainerRegistry.Models.PSManifestAttributeBase}
```

Para ver las etiquetas de una imagen concreta, use el cmdlet [Get-AzContainerRegistryTag][get-azcontainerregistrytag] como se indica a continuación:

```azurepowershell
Get-AzContainerRegistryTag -RegistryName <acrName> -RepositoryName azure-vote-front
```

La salida de ejemplo siguiente muestra la imagen de *v1* etiquetada en un paso anterior:

```output
Registry  ImageName        Tags
--------  ---------        ----
<acrName> azure-vote-front {v1}
```

---

Ya hay una imagen de contenedor almacenada en una instancia de Azure Container Registry privada. En el siguiente tutorial, dicha imagen se implementa desde ACR en un clúster de Kubernetes.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado una instancia de Azure Container Registry y ha insertado una imagen para su uso en un clúster de AKS. Ha aprendido a:

> [!div class="checklist"]
> * Crear una instancia de Azure Container Registry (ACR)
> * Etiquetar una imagen de contenedor para ACR
> * Cargar la imagen a ACR
> * Ver las imágenes del registro

Vaya al siguiente tutorial para aprender a implementar un clúster de Kubernetes en Azure.

> [!div class="nextstepaction"]
> [Implementación de un clúster de Kubernetes][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr
[az-acr-list]: /cli/azure/acr
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-list]: /cli/azure/acr#az_acr_list
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-powershell-install]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[new-azcontainerregistry]: /powershell/module/az.containerregistry/new-azcontainerregistry
[connect-azcontainerregistry]: /powershell/module/az.containerregistry/connect-azcontainerregistry
[get-azcontainerregistry]: /powershell/module/az.containerregistry/get-azcontainerregistry
[get-azcontainerregistrymanifest]: /powershell/module/az.containerregistry/get-azcontainerregistrymanifest
[get-azcontainerregistrytag]: /powershell/module/az.containerregistry/get-azcontainerregistrytag
