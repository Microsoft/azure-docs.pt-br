---
title: Atualizar um cluster do Serviço de Kubernetes do Azure (AKS)
description: Saiba como atualizar um cluster do AKS (Serviço de Kubernetes do Azure)
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 02/12/2019
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 59d52db8c3f5f8968eae1a544abe1e5c6bbaacca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61028433"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Atualizar um cluster do Serviço de Kubernetes do Azure (AKS)

Como parte do ciclo de vida de um cluster do AKS, muitas vezes você precisará atualizar para a versão mais recente do Kubernetes. É importante aplicar as versões mais recentes de segurança do Kubernetes ou atualizar para obter os recursos mais recentes. Este artigo mostra como atualizar um cluster do AKS existente.

## <a name="before-you-begin"></a>Antes de começar

Este artigo exige a execução da CLI do Azure versão 2.0.56 ou posterior. Execute `az --version` para encontrar a versão. Se precisar instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="check-for-available-aks-cluster-upgrades"></a>Verificação de atualizações disponíveis do cluster do AKS

Para verificar quais versões do Kubernetes estão disponíveis para seu cluster, use o comando [az aks get-upgrades][az-aks-get-upgrades]. O exemplo abaixo verifica atualizações disponíveis para o cluster nomeado *myAKSCluster* em um grupo de recursos nomeado *myResourceGroup*:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Ao atualizar um cluster do AKS, as versões secundárias do Kubernetes não poderão ser ignoradas. Por exemplo, atualizações entre *1.10.x* -> *1.11.x* ou *1.11.x* -> *1.12.x* são permitidas, porém *1.10.x* -> *1.12.x* não são.
>
> Para atualizar, do *1.10.x* -> *1.12.x*, primeiro atualize do *1.10.x* -> *1.11.x*, em seguida, atualize do *1.11.x* -> *1.12.x*.

A saída de exemplo a seguir mostra que o cluster pode ser atualizado para a versão *1.11.5* ou *1.11.6*:

```console
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.10.12          1.10.12            1.11.5, 1.11.6
```

## <a name="upgrade-an-aks-cluster"></a>Atualizar um cluster AKS

Com uma lista de versões disponíveis para o cluster do AKS, use o comando [az aks upgrade][az-aks-upgrade] para atualizar. Durante o processo de atualização, o AKS adiciona um novo nó ao cluster e, em seguida, cuidadosamente [isola e drena][kubernetes-drain] um nó por vez para minimizar a interrupção dos aplicativos em execução. Os exemplos a seguir atualizam um cluster para a versão *1.11.6*:

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.11.6
```

O cluster demora alguns minutos para ser atualizado, dependendo de quantos nós que você tem.

Para confirmar se a atualização teve êxito, use o comando [az aks show][az-aks-show]:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

A saída de exemplo a seguir mostra que o cluster agora executa a versão *1.11.6*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  chinaeast2      myResourceGroup  1.11.6               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.chinaeast2.azmk8s.io
```

## <a name="next-steps"></a>Próximas etapas

Este artigo mostrou como atualizar um cluster do AKS existente. Para saber mais sobre como implantar e gerenciar clusters do AKS, confira os tutoriais.

> [!div class="nextstepaction"]
> [Tutoriais do AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest
[az-aks-get-upgrades]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-get-upgrades
[az-aks-upgrade]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-upgrade
[az-aks-show]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-show