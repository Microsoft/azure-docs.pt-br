---
title: Usar grupos de posicionamento de proximidade para reduzir a latência de clusters do AKS (serviço kubernetes do Azure)
description: Saiba como usar grupos de posicionamento de proximidade para reduzir a latência para suas cargas de trabalho de cluster AKS.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: c0c1bf83bf5a816debe61cab0ceab856bfbd062f
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181169"
---
# <a name="reduce-latency-with-proximity-placement-groups"></a>Reduzir a latência com grupos de posicionamento de proximidade

> [!Note]
> Ao usar grupos de posicionamento de proximidade em AKS, a colocação se aplica somente aos nós de agente. O nó para o nó e o Pod hospedado correspondente à latência de Pod foram aprimorados. A colocalização não afeta o posicionamento do plano de controle de um cluster.

Ao implantar seu aplicativo no Azure, a difusão de instâncias de máquina virtual (VM) em regiões ou zonas de disponibilidade cria a latência de rede, o que pode afetar o desempenho geral do seu aplicativo. Um grupo de posicionamento de proximidade é um agrupamento lógico usado para garantir que os recursos de computação do Azure estejam fisicamente localizados próximos um do outro. Alguns aplicativos como jogos, simulações de engenharia e HFT (transações de alta frequência) exigem baixa latência e tarefas que são concluídas rapidamente. Para cenários de HPC (computação de alto desempenho) como esses, considere o uso de PPG ( [grupos de posicionamento de proximidade](../virtual-machines/co-location.md#proximity-placement-groups) ) para pools de nós do cluster.

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer que você esteja executando o CLI do Azure versão 2,14 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

### <a name="limitations"></a>Limitações

* Um grupo de posicionamento de proximidade pode ser mapeado para no máximo uma zona de disponibilidade.
* Um pool de nós deve usar conjuntos de dimensionamento de máquinas virtuais para associar um grupo de posicionamento de proximidade.
* Um pool de nós pode associar um grupo de posicionamento de proximidade no pool de nós apenas a tempo de criação.

## <a name="node-pools-and-proximity-placement-groups"></a>Pools de nós e grupos de posicionamento de proximidade

O primeiro recurso que você implanta com um grupo de posicionamento de proximidade é anexado a um data center específico. Recursos adicionais implantados com o mesmo grupo de posicionamento de proximidade são colocados na mesma data center. Depois que todos os recursos que usam o grupo de posicionamento de proximidade tiverem sido interrompidos (desalocados) ou excluídos, eles não serão mais anexados.

* Muitos pools de nós podem ser associados a um único grupo de posicionamento de proximidade.
* Um pool de nós só pode ser associado a um único grupo de posicionamento de proximidade.

### <a name="configure-proximity-placement-groups-with-availability-zones"></a>Configurar grupos de posicionamento de proximidade com zonas de disponibilidade

> [!NOTE]
> Embora os grupos de posicionamento de proximidade exijam que um pool de nós use no máximo uma zona de disponibilidade, o [SLA da VM do Azure de linha de base de 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) ainda estará em vigor para as VMs em uma única zona.

Os grupos de posicionamento de proximidade são um conceito de pool de nós e associados a cada pool de nós individual. O uso de um recurso PPG não afeta a disponibilidade do plano de controle AKS. Isso pode afetar a forma como um cluster deve ser projetado com zonas. Para garantir que um cluster seja distribuído em várias zonas, o design a seguir é recomendado.

* Provisione um cluster com o primeiro pool de sistema usando 3 zonas e nenhum grupo de posicionamento de proximidade associado. Isso garante que o pods do sistema esteja em um pool de nós dedicado que se espalhará por várias zonas.
* Adicione pools de nó de usuário adicionais com uma zona exclusiva e um grupo de posicionamento de proximidade associado a cada pool. Um exemplo é nodepool1 na zona 1 e PPG1, nodepool2 na zona 2 e PPG2, nodepool3 na zona 3 com PPG3. Isso garante em um nível de cluster, os nós são distribuídos em várias zonas e cada pool de nós individual é colocado na zona designada com um recurso PPG dedicado.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>Criar um novo cluster AKS com um grupo de posicionamento de proximidade

O exemplo a seguir usa o comando [AZ Group Create][az-group-create] para criar um grupo de recursos chamado *MyResource* Group na região *centralus* . Um cluster AKS chamado *myAKSCluster* é então criado usando o comando [AZ AKs Create][az-aks-create] .

A rede acelerada melhora muito o desempenho de rede das máquinas virtuais. Idealmente, use grupos de posicionamento de proximidade em conjunto com a rede acelerada. Por padrão, o AKS usa rede acelerada em [instâncias de máquinas virtuais com suporte](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints), que incluem a maioria das máquinas virtuais do Azure com dois ou mais vCPUs.

Crie um novo cluster AKS com um grupo de posicionamento de proximidade associado ao primeiro pool de nós do sistema:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
Execute o comando a seguir e armazene a ID que é retornada:

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

O comando produz a saída, que inclui o valor de *ID* que você precisa para os próximos comandos da CLI:

```output
{
  "availabilitySets": null,
  "colocationStatus": null,
  "id": "/subscriptions/yourSubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Compute/proximityPlacementGroups/myPPG",
  "location": "centralus",
  "name": "myPPG",
  "proximityPlacementGroupType": "Standard",
  "resourceGroup": "myResourceGroup",
  "tags": {},
  "type": "Microsoft.Compute/proximityPlacementGroups",
  "virtualMachineScaleSets": null,
  "virtualMachines": null
}
```

Use a ID de recurso do grupo de posicionamento de proximidade para o valor *myPPGResourceID* no comando abaixo:

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial system node pool only. The PPG has no effect on the cluster control plane.
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>Adicionar um grupo de posicionamento de proximidade a um cluster existente

Você pode adicionar um grupo de posicionamento de proximidade a um cluster existente criando um novo pool de nós. Opcionalmente, você pode migrar as cargas de trabalho existentes para o novo pool de nós e, em seguida, excluir o pool de nós original.

Use o mesmo grupo de posicionamento de proximidade que você criou anteriormente, e isso garantirá que os nós de agente em ambos os pools de nós no cluster AKS estejam localizados fisicamente na mesma data center.

Use a ID do recurso do grupo de posicionamento de proximidade que você criou anteriormente e adicione um novo pool de nós com o [`az aks nodepool add`][az-aks-nodepool-add] comando:

```azurecli-interactive
# Add a new node pool that uses a proximity placement group, use a --node-count = 1 for testing
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 1 \
    --ppg myPPGResourceID
```

## <a name="clean-up"></a>Limpar

Para excluir o cluster, use o [`az group delete`][az-group-delete] comando para excluir o grupo de recursos AKs:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [grupos de posicionamento de proximidade][proximity-placement-groups].

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[proximity-placement-groups]: ../virtual-machines/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az-aks-create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az-aks-nodepool-add
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete