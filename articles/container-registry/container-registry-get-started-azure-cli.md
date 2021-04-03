---
title: Início rápido – Criar registro – CLI do Azure
description: Aprenda rapidamente a criar um registro de contêiner particular do Docker com a CLI do Azure.
ms.topic: quickstart
ms.date: 06/12/2020
ms.custom: seodec18, H1Hack27Feb2017, mvc, devx-track-azurecli
ms.openlocfilehash: 226e50aec8f7c76a1b4c81d1a07d57583059ef0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020068"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-cli"></a>Início Rápido: criar um registro de contêiner privado usando a CLI do Azure

O Registro de Contêiner do Azure é um serviço de registro de contêiner Docker gerenciado usado para armazenar imagens de contêiner de Docker particulares. Este guia detalha a criação de uma instância do Registro de Contêiner do Azure usando a CLI do Azure. Em seguida, use os comandos do Docker para efetuar push de uma imagem de contêiner no Registro e, por fim, efetuar pull e executar a imagem do seu Registro.

Este início rápido exige que você esteja executando a CLI do Azure (versão 2.0.55 ou posterior é recomendada). Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli].

Você também deve ter o Docker instalado localmente. O Docker fornece pacotes que configuram facilmente o Docker em qualquer sistema [macOS][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

Como o Azure Cloud Shell não inclui todos os componentes de Docker necessários (o daemon `dockerd`), você não pode usar o Cloud Shell para este guia de início rápido.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create][az-group-create]. Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Criar um registro de contêiner

Neste início rápido você criará um Registro *Básico*, que é uma opção com otimização de custo para desenvolvedores que estão aprendendo sobre o Registro de Contêiner do Azure. Para obter detalhes sobre as camadas de serviço disponíveis, confira [Camadas de serviço do registro de contêiner][container-registry-skus].

Crie uma instância ACR usando o comando [az acr create][az-acr-create]. O nome do registro deve ser exclusivo no Azure e conter de 5 a 50 caracteres alfanuméricos. No exemplo a seguir o nome *myContainerRegistry007* é usado. Atualize-o para um valor exclusivo.

```azurecli
az acr create --resource-group myResourceGroup \
  --name myContainerRegistry007 --sku Basic
```

Quando o registro é criado, o resultado é semelhante ao seguinte:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2019-01-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "mycontainerregistry007.azurecr.io",
  "name": "myContainerRegistry007",
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

Anote `loginServer` na saída, que é o nome totalmente qualificado do Registro (todo em minúsculas). No restante deste início rápido, `<registry-name>` é um espaço reservado para o nome do registro de contêiner e `<login-server>` é um espaço reservado para o nome do servidor de logon do registro.

## <a name="log-in-to-registry"></a>Fazer logon no registro

Antes de efetuar push e pull nas imagens de contêiner, você deverá fazer logon no Registro. Para fazer isso, use o comando [az acr login][az-acr-login]. Especifique somente o nome do Registro ao fazer logon com a CLI do Azure. Não use o nome do servidor de logon, que inclui um sufixo de domínio como `azurecr.io`. 

```azurecli
az acr login --name <registry-name>
```

Exemplo:

```azurecli
az acr login --name mycontainerregistry
```

O comando retorna uma mensagem `Login Succeeded` na conclusão.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Listar imagens de contêiner

O exemplo a seguir lista os repositórios em seu Registro:

```azurecli
az acr repository list --name <registry-name> --output table
```

Saída:

```
Result
----------------
hello-world
```

O exemplo a seguir lista as marcas no repositório **hello-world**.

```azurecli
az acr repository show-tags --name <registry-name> --repository hello-world --output table
```

Saída:

```
Result
--------
v1
```

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você poderá usar o comando [az group delete][az-group-delete] para remover o grupo de recursos, o registro de contêiner e as imagens de contêiner armazenadas lá.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Registro de Contêiner do Azure com a CLI do Azure, efetuou push de uma imagem de contêiner para o Registro e extraiu e executou a imagem do Registro. Prossiga para os tutoriais de Registro de Contêiner do Azure para uma análise mais profunda do ACR.

> [!div class="nextstepaction"]
> [Tutoriais de Registro de Contêiner do Azure][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Tutoriais das Tarefas do Registro de Contêiner do Azure][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
