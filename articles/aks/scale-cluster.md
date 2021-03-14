---
title: Dimensionar um cluster do Serviço de Kubernetes do Azure (AKS)
description: Saiba como dimensionar o número de nós em um cluster do AKS (Serviço de Kubernetes do Azure).
services: container-service
ms.topic: article
ms.date: 09/16/2020
ms.openlocfilehash: fdb61bf090351894329c24eb1a3c73d627e622e8
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102173759"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Dimensionar a contagem de nós em um cluster do AKS (Serviço de Kubernetes do Azure)

Se as necessidades de recurso para seus aplicativos mudarem, você poderá dimensionar manualmente um cluster do AKS para executar um número diferente de nós. Ao reduzir, os nós são cuidadosamente [isolados e drenados][kubernetes-drain] para minimizar a interrupção aos aplicativos em execução. Quando você escala verticalmente, o AKS aguarda até que os nós sejam marcados `Ready` pelo cluster do kubernetes antes de os pods serem agendados neles.

## <a name="scale-the-cluster-nodes"></a>Escalar os nós de cluster

Primeiro, obtenha o *nome* do pool de nós usando o comando [AZ AKs show][az-aks-show] . O exemplo a seguir obtém o nome do pool de nós para o cluster chamado *myAKSCluster* no grupo de recursos *MyResource* Group:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

A saída de exemplo a seguir mostra que o *nome* é *nodepool1*:

```output
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

Use o comando [AZ AKs Scale][az-aks-scale] para dimensionar os nós de cluster. O exemplo a seguir escala um cluster chamado *myAKSCluster* para um único nó. Forneça seu próprio `--nodepool-name` do comando anterior, como *nodepool1*:

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


## <a name="scale-user-node-pools-to-0"></a>Dimensionar `User` pools de nós para 0

Diferentemente dos `System` pools de nós que sempre exigem nós em execução, os `User` pools de nós permitem que você dimensione para 0. Para saber mais sobre as diferenças entre pools de nó do usuário e do sistema, consulte [pools de nó do usuário e do sistema](use-system-pools.md).

Para dimensionar um pool de usuários para 0, você pode usar a [escala AZ AKs nodepool][az-aks-nodepool-scale] em alternativa ao `az aks scale` comando acima e definir 0 como sua contagem de nós.


```azurecli-interactive
az aks nodepool scale --name <your node pool name> --cluster-name myAKSCluster --resource-group myResourceGroup  --node-count 0 
```

Você também pode dimensionar automaticamente `User` os pools de nós para 0 nós, definindo o `--min-count` parâmetro do [dimensionamento](cluster-autoscaler.md) automático do cluster como 0.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você dimensionou manualmente um cluster AKS para aumentar ou diminuir o número de nós. Você também pode usar o [dimensionamento][cluster-autoscaler] automático do cluster para dimensionar automaticamente o cluster.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az-aks-nodepool-scale