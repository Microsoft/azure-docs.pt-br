---
title: Dimensionar um cluster do Serviço de Kubernetes do Azure (AKS)
description: Saiba como dimensionar o número de nós em um cluster do AKS (Serviço de Kubernetes do Azure).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 9cc06df5d2a66ede18af52c13201c731c12e2049
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614500"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Dimensionar a contagem de nós em um cluster do AKS (Serviço de Kubernetes do Azure)

Se as necessidades de recurso para seus aplicativos mudarem, você poderá dimensionar manualmente um cluster do AKS para executar um número diferente de nós. Ao reduzir verticalmente, os nós são cuidadosamente [isolados e esvaziados][kubernetes-drain] para minimizar as interrupções nos aplicativos em execução. Quando você escala verticalmente, o AKS aguarda até que nós são marcados `Ready` pelo cluster do Kubernetes, antes de pods são agendados neles.

## <a name="scale-the-cluster-nodes"></a>Escalar os nós de cluster

Primeiro, obtenha o *nome* de seu pool de nó usando o [show do az aks][az-aks-show] comando. O exemplo a seguir obtém o nome do pool de nó para o cluster chamado *myAKSCluster* na *myResourceGroup* grupo de recursos:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

A saída de exemplo a seguir mostra que o *nome* é *nodepool1*:

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles

[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

Use o [escala do az aks][az-aks-scale] comando dimensionar os nós do cluster. O exemplo a seguir escala um cluster chamado *myAKSCluster* para um único nó. Forneça seu próprio *--nodepool-name* do comando anterior, como *nodepool1*:

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

A saída de exemplo a seguir mostra que o cluster foi dimensionado com êxito para um nó, conforme mostrado na seção *agentPoolProfiles*:

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você dimensionado manualmente um cluster do AKS para aumentar ou diminuir o número de nós. Você também pode usar o [dimensionador automático de cluster][cluster-autoscaler] (atualmente em visualização no AKS) para dimensionar automaticamente o cluster.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
