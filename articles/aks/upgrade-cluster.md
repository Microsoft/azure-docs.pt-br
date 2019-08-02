---
title: Atualizar um cluster do Serviço de Kubernetes do Azure (AKS)
description: Saiba como atualizar um cluster do AKS (Serviço de Kubernetes do Azure)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 2ed58846b9e7816092f0fc0787204921071d75e9
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498547"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Atualizar um cluster do Serviço de Kubernetes do Azure (AKS)

Como parte do ciclo de vida de um cluster do AKS, muitas vezes você precisará atualizar para a versão mais recente do Kubernetes. É importante aplicar as versões mais recentes de segurança do Kubernetes ou atualizar para obter os recursos mais recentes. Este artigo mostra como atualizar os componentes mestres ou um único pool de nós padrão em um cluster AKS.

Para clusters AKS que usam vários pools de nó ou nós do Windows Server (atualmente em visualização no AKS), consulte [atualizar um pool de nós no AKs][nodepool-upgrade].

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer que você esteja executando o CLI do Azure versão 2.0.65 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

> [!WARNING]
> Uma atualização do cluster AKS dispara um Cordon e dreno de seus nós. Se você tiver uma cota de computação baixa disponível, a atualização poderá falhar.  Consulte [aumentar cotas](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request?branch=pr-en-us-83289) para obter mais informações.

## <a name="check-for-available-aks-cluster-upgrades"></a>Verificação de atualizações disponíveis do cluster do AKS

Para verificar quais versões do kubernetes estão disponíveis para o cluster, use o comando [AZ AKs Get-][az-aks-get-upgrades] upgrades. O exemplo abaixo verifica atualizações disponíveis para o cluster nomeado *myAKSCluster* em um grupo de recursos nomeado *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Ao atualizar um cluster do AKS, as versões secundárias do Kubernetes não poderão ser ignoradas. Por exemplo, as atualizações entre *1.11. x* -> *1.12. x* ou *1.12. x* -> *1.13. x* são permitidas, no entanto, *1.11. x* -> *1.13. x* não é.
>
> Para atualizar, de *1.11. x* -> *1.13. x*, primeiro atualize de *1.11. x* -> *1.12. x*e, em seguida, atualize de *1.12. x* -> *1.13. x*.

A saída de exemplo a seguir mostra que o cluster pode ser atualizado para a versão *1.12.7* ou *1.12.8*:

```console
Name     ResourceGroup    MasterVersion  NodePoolVersion  Upgrades
-------  ---------------  -------------  ---------------  --------------
default  myResourceGroup  1.11.9         1.11.9           1.12.7, 1.12.8
```

## <a name="upgrade-an-aks-cluster"></a>Atualizar um cluster AKS

Com uma lista de versões disponíveis para o cluster AKS, use o comando [AZ AKs upgrade][az-aks-upgrade] para atualizar. Durante o processo de atualização, o AKS adiciona um novo nó ao cluster que executa a versão especificada do kubernetes, depois cuidadosamente [Cordon e esvazia][kubernetes-drain] um dos nós antigos para minimizar a interrupção na execução de aplicativos. Quando o novo nó é confirmado como executando pods de aplicativo, o nó antigo é excluído. Esse processo se repete até que todos os nós no cluster tenham sido atualizados.

O exemplo a seguir atualiza um cluster para a versão *1.12.8*:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.12.8
```

O cluster demora alguns minutos para ser atualizado, dependendo de quantos nós que você tem.

Para confirmar que a atualização foi bem-sucedida, use o comando [AZ AKs show][az-aks-show] :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

A saída de exemplo a seguir mostra que o cluster agora executa *1.12.8*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.12.8               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Próximas etapas

Este artigo mostrou como atualizar um cluster do AKS existente. Para saber mais sobre como implantar e gerenciar clusters do AKS, confira os tutoriais.

> [!div class="nextstepaction"]
> [Tutoriais do AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
