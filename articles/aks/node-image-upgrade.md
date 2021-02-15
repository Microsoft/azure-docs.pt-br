---
title: Atualizar imagens de nó do AKS (serviço kubernetes do Azure)
description: Saiba como atualizar as imagens em nós de cluster AKS e pools de nós.
ms.service: container-service
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jpalma
ms.openlocfilehash: 83d7d48922806334e2b49494fe0ef1d15e1a7a6a
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531472"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Atualização de imagem de nó do AKS (serviço kubernetes do Azure)

O AKS dá suporte à atualização das imagens em um nó para que você esteja atualizado com as atualizações mais recentes do sistema operacional e do tempo de execução. O AKS fornece uma nova imagem por semana com as atualizações mais recentes, portanto, é benéfico atualizar as imagens do nó regularmente para os recursos mais recentes, incluindo patches do Linux ou do Windows. Este artigo mostra como atualizar imagens de nó de cluster AKS e como atualizar imagens de pool de nós sem Atualizar a versão do kubernetes.

Para obter mais informações sobre as imagens mais recentes fornecidas pelo AKS, consulte as [notas de versão do AKS](https://github.com/Azure/AKS/releases).

Para obter informações sobre como atualizar a versão do kubernetes para seu cluster, consulte [atualizar um cluster AKs][upgrade-cluster].

> [!NOTE]
> O cluster AKS deve usar conjuntos de dimensionamento de máquinas virtuais para os nós.

## <a name="check-if-your-node-pool-is-on-the-latest-node-image"></a>Verifique se o pool de nós está na imagem de nó mais recente

Você pode ver qual é a versão de imagem de nó mais recente disponível para o pool de nós com o seguinte comando: 

```azurecli
az aks nodepool get-upgrades \
    --nodepool-name mynodepool \
    --cluster-name myAKSCluster \
    --resource-group myResourceGroup
```

Na saída, você pode ver `latestNodeImageVersion` como no exemplo abaixo:

```output
{
  "id": "/subscriptions/XXXX-XXX-XXX-XXX-XXXXX/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/nodepool1/upgradeProfiles/default",
  "kubernetesVersion": "1.17.11",
  "latestNodeImageVersion": "AKSUbuntu-1604-2020.10.28",
  "name": "default",
  "osType": "Linux",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.ContainerService/managedClusters/agentPools/upgradeProfiles",
  "upgrades": null
}
```

Portanto, para `nodepool1` a imagem de nó mais recente disponível é `AKSUbuntu-1604-2020.10.28` . Agora você pode compará-lo com a versão da imagem do nó atual em uso pelo pool de nós executando:

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --query nodeImageVersion
```

Um exemplo de saída seria:

```output
"AKSUbuntu-1604-2020.10.08"
```

Neste exemplo, você pode atualizar da `AKSUbuntu-1604-2020.10.08` versão da imagem atual para a versão mais recente `AKSUbuntu-1604-2020.10.28` . 

## <a name="upgrade-all-nodes-in-all-node-pools"></a>Atualizar todos os nós em todos os pools de nós

A atualização da imagem do nó é feita com `az aks upgrade` . Para atualizar a imagem do nó, use o seguinte comando:

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

Durante a atualização, verifique o status das imagens de nó com o seguinte `kubectl` comando para obter os rótulos e filtrar as informações da imagem do nó atual:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Quando a atualização for concluída, use `az aks show` para obter os detalhes do pool de nós atualizados. A imagem do nó atual é mostrada na `nodeImageVersion` propriedade.

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>Atualizar um pool de nós específico

A atualização da imagem em um pool de nós é semelhante à atualização da imagem em um cluster.

Para atualizar a imagem do sistema operacional do pool de nós sem fazer uma atualização do cluster kubernetes, use a `--node-image-only` opção no exemplo a seguir:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

Durante a atualização, verifique o status das imagens de nó com o seguinte `kubectl` comando para obter os rótulos e filtrar as informações da imagem do nó atual:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Quando a atualização for concluída, use `az aks nodepool show` para obter os detalhes do pool de nós atualizados. A imagem do nó atual é mostrada na `nodeImageVersion` propriedade.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>Atualizar imagens de nó com surtos de nó

Para acelerar o processo de atualização de imagem de nó, você pode atualizar suas imagens de nó usando um valor de surto de nó personalizável. Por padrão, o AKS usa um nó adicional para configurar atualizações.

Se você quiser aumentar a velocidade das atualizações, use o `--max-surge` valor para configurar o número de nós a serem usados para atualizações para que eles sejam concluídos mais rapidamente. Para saber mais sobre as compensações de várias `--max-surge` configurações, confira [Personalizar atualização de surtos de nó][max-surge].

O comando a seguir define o valor máximo de surto para executar uma atualização de imagem de nó:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

Durante a atualização, verifique o status das imagens de nó com o seguinte `kubectl` comando para obter os rótulos e filtrar as informações da imagem do nó atual:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Use `az aks nodepool show` para obter os detalhes do pool de nós atualizado. A imagem do nó atual é mostrada na `nodeImageVersion` propriedade.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>Próximas etapas

- Consulte as [notas de versão do AKS](https://github.com/Azure/AKS/releases) para obter informações sobre as imagens de nó mais recentes.
- Saiba como atualizar a versão do kubernetes com a [atualização de um cluster do AKS][upgrade-cluster].
- [Aplicar automaticamente atualizações de cluster e pool de nós a ações do GitHub][github-schedule]
- Saiba mais sobre vários pools de nós e como atualizar pools de nós com [criar e gerenciar vários pools de nós][use-multiple-node-pools].

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[github-schedule]: node-upgrade-github-actions.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
