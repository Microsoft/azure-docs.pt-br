---
title: Iniciar e parar um serviço de kubernetes do Azure (AKS)
description: Saiba como parar ou iniciar um cluster do AKS (serviço kubernetes do Azure).
services: container-service
ms.topic: article
ms.date: 09/24/2020
author: palma21
ms.openlocfilehash: 87d51f9c1d084faf79c7ec1cf1255a6fb3c8245d
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200999"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster"></a>Parar e iniciar um cluster do serviço de kubernetes do Azure (AKS)

Suas cargas de trabalho do AKS podem não precisar ser executadas continuamente, por exemplo, um cluster de desenvolvimento que é usado somente durante o horário comercial. Isso leva a tempos em que o cluster AKS (serviço kubernetes do Azure) pode estar ocioso, executando não mais do que os componentes do sistema. Você pode reduzir a superfície do cluster [dimensionando todos os `User` pools de nós para 0](scale-cluster.md#scale-user-node-pools-to-0), mas seu [ `System` pool](use-system-pools.md) ainda é necessário para executar os componentes do sistema enquanto o cluster está em execução. Para otimizar ainda mais os custos durante esses períodos, você pode desativar completamente (parar) o cluster. Essa ação interromperá o plano de controle e os nós de agente, permitindo que você economize em todos os custos de computação, mantendo todos os seus objetos e o estado do cluster armazenados para quando você iniciá-lo novamente. Em seguida, você pode selecionar o ponto em que parou após um final de semana ou para que o cluster seja executado somente enquanto você executa seus trabalhos em lotes.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se precisar de um cluster do AKS, veja o guia de início rápido do AKS [usando a CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

### <a name="limitations"></a>Limitações

Ao usar o recurso iniciar/parar do cluster, as seguintes restrições se aplicam:

- Este recurso só tem suporte para clusters com backup de conjuntos de dimensionamento de máquinas virtuais.
- O estado do cluster de um cluster AKS interrompido é preservado por até 12 meses. Se o cluster for interrompido por mais de 12 meses, o estado do cluster não poderá ser recuperado. Para obter mais informações, consulte as [políticas de suporte do AKS](support-policies.md).
- Você só pode iniciar ou excluir um cluster AKS interrompido. Para executar qualquer operação como escala ou atualização, inicie o cluster primeiro.

## <a name="stop-an-aks-cluster"></a>Parar um cluster AKS

Você pode usar o `az aks stop` comando para interromper um plano de controle e nós de um cluster AKS em execução. O exemplo a seguir interrompe um cluster chamado *myAKSCluster*:

```azurecli-interactive
az aks stop --name myAKSCluster --resource-group myResourceGroup
```

Você pode verificar quando o cluster é interrompido usando o comando [AZ AKs show][az-aks-show] e confirmando os `powerState` programas como `Stopped` na saída abaixo:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Stopped"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Se o `provisioningState` Mostrar `Stopping` que significa que o cluster ainda não foi totalmente interrompido.

> [!IMPORTANT]
> Se você estiver usando [orçamentos de interrupção de Pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) , a operação de parada poderá levar mais tempo, uma vez que o processo de drenagem demorará mais para ser concluído.

## <a name="start-an-aks-cluster"></a>Iniciar um cluster AKS

Você pode usar o `az aks start` comando para iniciar um plano de controle e nós do cluster AKs interrompido. O cluster é reiniciado com o estado do plano de controle anterior e o número de nós do agente.  
O exemplo a seguir inicia um cluster chamado *myAKSCluster*:

```azurecli-interactive
az aks start --name myAKSCluster --resource-group myResourceGroup
```

Você pode verificar quando o cluster foi iniciado usando o comando [AZ AKs show][az-aks-show] e confirmando os `powerState` programas `Running` como na saída abaixo:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Running"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Se o `provisioningState` Mostrar `Starting` que significa que o cluster ainda não foi totalmente iniciado.

## <a name="next-steps"></a>Próximas etapas

- Para saber como dimensionar os `User` pools para 0, consulte [dimensionar `User` pools para 0](scale-cluster.md#scale-user-node-pools-to-0).
- Para saber como economizar custos usando instâncias de spot, consulte [Adicionar um pool de nós de spot a AKs](spot-node-pool.md).
- Para saber mais sobre as políticas de suporte do AKS, consulte [políticas de suporte do AKS](support-policies.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-show]: /cli/azure/aks#az_aks_show
