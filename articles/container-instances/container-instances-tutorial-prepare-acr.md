---
title: Tutorial – Preparar um registro de contêiner para as Instâncias de Contêiner do Azure
description: Tutorial das Instâncias de Contêiner do Azure, parte 2 de 3 – Preparar um Registro de Contêiner do Azure e enviar uma imagem por push
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: b3c907eacb14ed65410a60fcf22ebe99fd8cc3bb
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325619"
---
# <a name="tutorial-deploy-an-azure-container-registry-and-push-a-container-image"></a>Tutorial: Implantar um Registro de Contêiner do Azure e enviar uma imagem de contêiner por push

Esta é a parte dois de um tutorial de três partes. A [Parte um](container-instances-tutorial-prepare-app.md) do tutorial criou uma imagem de contêiner do Docker para um aplicativo Web Node.js. Neste tutorial, você pode enviar a imagem para um Registro de Contêiner do Azure. Se você ainda não criou a imagem de contêiner, retorne ao [Tutorial 1 – Criar imagem de contêiner](container-instances-tutorial-prepare-app.md).

O Registro de Contêiner do Azure é o registro de Docker privado no Azure. Neste tutorial, você pode criar uma instância do Registro de Contêiner do Azure em sua assinatura e enviar a imagem de contêiner criada anteriormente para ele. Neste artigo, a segunda parte da série, você:

> [!div class="checklist"]
> * Cria uma instância do Registro de Contêiner do Azure
> * Marca uma imagem de contêiner para o Registro de Contêiner do Azure
> * Carrega a imagem em seu registro

No próximo artigo, que é o último da série, você implanta o contêiner do seu registro privado nas Instâncias de Contêiner do Azure.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="create-azure-container-registry"></a>Crie um registro de contêiner do Azure

Antes de criar o registro de contêiner, é necessário um *grupo de recursos* para implantá-lo. O grupo de recursos é uma coleção lógica na qual todos os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com o comando [az group create][az-group-create]. No exemplo a seguir, um grupo de recursos chamado *myResourceGroup* é criado na região *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Após criar o grupo de recursos, crie um registro de contêiner do Azure com o comando [az acr create][az-acr-create]. O nome do Registro de contêiner deve ser exclusivo no Azure e conter de 5 a 50 caracteres alfanuméricos. Substitua `<acrName>` por um nome exclusivo para o registro:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic --admin-enabled true
```

Aqui está o exemplo de saída para um novo registro de contêiner do Azure denominado *mycontainerregistry082* (mostrado aqui truncado):

```console
$ az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
...
{
  "adminUserEnabled": true,
  "creationDate": "2018-03-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

O restante deste tutorial se refere a `<acrName>` como um espaço reservado para o nome do registro de contêiner que você escolheu nesta etapa.

## <a name="log-in-to-container-registry"></a>Faça logon no registro de contêiner

Você deve fazer logon na instância do Registro de Contêiner do Azure antes de enviar imagens por push a ela. Use o comando [az acr login][az-acr-login] para concluir a operação. Você deve fornecer o nome exclusivo que você escolheu para o registro de contêiner quando ele foi criado.

```azurecli
az acr login --name <acrName>
```

O comando retorna `Login Succeeded` na conclusão:

```console
$ az acr login --name mycontainerregistry082
Login Succeeded
```

## <a name="tag-container-image"></a>Marcar imagem de contêiner

Para enviar por push uma imagem de contêiner para um registro privado, como o Registro de Contêiner do Azure, primeiro você deve marcar a imagem com o nome completo do servidor de logon do registro.

Primeiro, obtenha o nome do servidor de logon completo para o registro de contêiner do Azure. Execute o comando [az acr show][az-acr-show] a seguir e substitua `<acrName>` pelo nome do registro que você acabou de criar:

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Por exemplo, se o registro é denominado *mycontainerregistry082*:

```console
$ az acr show --name mycontainerregistry082 --query loginServer --output table
Result
------------------------
mycontainerregistry082.azurecr.io
```

Agora, exiba a lista de suas imagens locais com o comando [imagens do docker][docker-images]:

```bash
docker images
```

Juntamente com quaisquer outras imagens que você tem em seu computador, você deve ver a imagem *aci-tutorial-app* incorporada no [tutorial anterior](container-instances-tutorial-prepare-app.md):

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 minutes ago    68.1 MB
```

Marque a imagem *aci-tutorial-app* com o loginServer do Registro de contêiner. Além disso, adicione a marca `:v1` no final do nome da imagem para indicar o número de versão da imagem. Substitua `<acrLoginServer>` pelo resultado do comando [az acr show][az-acr-show] que você executou anteriormente.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

Execute `docker images` novamente para verificar a operação de marcação:

```console
$ docker images
REPOSITORY                                            TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app                                      latest    5c745774dfa9    39 minutes ago    68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app    v1        5c745774dfa9    7 minutes ago     68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Enviar imagem por push ao Registro de Contêiner do Azure

Agora que você já marcou a imagem *aci-tutorial-app* com o nome do servidor de logon completo de seu registro privado, poderá enviá-la por push para o registro com o comando [docker push][docker-push]. Substitua `<acrLoginServer>` pelo nome do servidor de logon completo obtido na etapa anterior.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

A operação `push` deve levar de alguns segundos a alguns minutos, dependendo de sua conexão de Internet, e a saída é semelhante à seguinte:

```console
$ docker push mycontainerregistry082.azurecr.io/aci-tutorial-app:v1
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Listar imagens no Registro de Contêiner do Azure

Para verificar se a imagem que você acabou de enviar realmente está em seu registro de contêiner do Azure, liste as imagens em seu registro com o comando [az acr repository list][az-acr-repository-list]. Substitua `<acrName>` pelo nome do seu Registro de contêiner.

```azurecli
az acr repository list --name <acrName> --output table
```

Por exemplo:

```console
$ az acr repository list --name mycontainerregistry082 --output table
Result
----------------
aci-tutorial-app
```

Para ver as *marcas* de uma imagem específica, use o comando [az acr repository show-tags][az-acr-repository-show-tags].

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Você deverá ver uma saída semelhante ao seguinte:

```console
$ az acr repository show-tags --name mycontainerregistry082 --repository aci-tutorial-app --output table
Result
--------
v1
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você preparou um registro de contêiner do Azure para ser usado com Instâncias de Contêiner do Azure e enviou por push uma imagem de contêiner ao registro. As etapas a seguir foram concluídas:

> [!div class="checklist"]
> * Implantando uma instância de Registro de Contêiner do Azure
> * Uma imagem de contêiner foi marcada para o Registro de Contêiner do Azure
> * Uma imagem foi carregada no Registro de Contêiner do Azure

Avance para o próximo tutorial para saber como implantar o contêiner no Azure usando as Instâncias de Contêiner do Azure:

> [!div class="nextstepaction"]
> [Implantar um contêiner em Instâncias de Contêiner do Azure](container-instances-tutorial-deploy-app.md)

<!-- LINKS - External -->
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: https://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
