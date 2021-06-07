---
title: Adicionar um pool de nós spot a um cluster do AKS (Serviço de Kubernetes do Azure)
description: Saiba como adicionar um pool de nós Spot a um cluster do AKS (serviço kubernetes do Azure).
services: container-service
ms.service: container-service
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: 7f838b2a78f1c6993aa247f2944d4f2a9b1e9556
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181118"
---
# <a name="add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Adicionar um pool de nós spot a um cluster do AKS (Serviço de Kubernetes do Azure)

Um pool de nós Spot é um pool de nós apoiado por um [conjunto de dimensionamento de máquinas virtuais Spot][vmss-spot]. O uso de VMs pontuais para nós com seu cluster AKS permite que você aproveite a capacidade não utilizada no Azure a uma economia de custo significativa. A quantidade de capacidade inutilizada disponível variará com base em vários fatores, incluindo o tamanho do nó, a região e a hora do dia.

Ao implantar um pool de nós Spot, o Azure alocará os nós de spot se houver capacidade disponível. Mas não há SLA para os nós de spot. Um conjunto de escala de spot que faz o backup do pool de nós Spot é implantado em um único domínio de falha e não oferece nenhuma garantia de alta disponibilidade. A qualquer momento quando o Azure precisar da capacidade de volta, a infraestrutura do Azure removerá os nós de spot.

Os nós de spot são ótimos para cargas de trabalho que podem lidar com interrupções, encerramentos antecipados ou remoções. Por exemplo, cargas de trabalho como trabalhos de processamento em lotes, ambientes de desenvolvimento e teste e cargas de trabalho de computação grandes podem ser boas candidatas a serem agendadas em um pool de nós Spot.

Neste artigo, você adiciona um pool de nós Spot secundário a um cluster existente do AKS (serviço kubernetes do Azure).

Este artigo pressupõe uma compreensão básica dos conceitos do Kubernetes e do Azure Load Balancer. Para obter mais informações, confira [Principais conceitos do Kubernetes para o AKS (Serviço de Kubernetes do Azure)][kubernetes-concepts].

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Quando você cria um cluster para usar um pool de nós Spot, esse cluster também deve usar conjuntos de dimensionamento de máquinas virtuais para pools de nós e o balanceador de carga SKU *padrão* . Você também deve adicionar um pool de nós adicional depois de criar o cluster para usar um pool de nós Spot. Adicionar um pool de nós adicional é abordado em uma etapa posterior.

Este artigo requer que você esteja executando o CLI do Azure versão 2,14 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

### <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você cria e gerencia clusters AKS com um pool de nós spot:

* Um pool de nós Spot não pode ser o pool de nós padrão do cluster. Um pool de nós Spot só pode ser usado para um pool secundário.
* Não é possível atualizar um pool de nós Spot, pois os pools de nós Spot não podem garantir Cordon e drenagem. Você deve substituir o pool de nós Spot existente por um novo para executar operações, como atualizar a versão kubernetes. Para substituir um pool de nós Spot, crie um novo pool de nós spot com uma versão diferente do kubernetes, aguarde até que seu status esteja *pronto* e, em seguida, remova o pool de nós antigo.
* O plano de controle e os pools de nós não podem ser atualizados ao mesmo tempo. Você deve atualizá-los separadamente ou remover o pool de nós spot para atualizar o plano de controle e os pools de nós restantes ao mesmo tempo.
* Um pool de nós Spot deve usar conjuntos de dimensionamento de máquinas virtuais.
* Não é possível alterar ScaleSetPriority ou SpotMaxPrice após a criação.
* Ao definir SpotMaxPrice, o valor deve ser-1 ou um valor positivo com até cinco casas decimais.
* Um pool de nós spot terá o rótulo *kubernetes.Azure.com/scalesetpriority:spot*, o seu *kubernetes.Azure.com/scalesetpriority=spot:NoSchedule* e os pods do sistema terão a proteção contra afinidade.
* Você deve adicionar um [toleration correspondente][spot-toleration] para agendar cargas de trabalho em um pool de nós Spot.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>Adicionar um pool de nós spot a um cluster do AKS

Você deve adicionar um pool de nós Spot a um cluster existente que tenha vários pools de nós habilitados. Mais detalhes sobre como criar um cluster AKS com vários pools de nós estão disponíveis [aqui][use-multiple-node-pools].

Crie um pool de nós usando o [AKs AZ nodepool Add][az-aks-nodepool-add].
```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name spotnodepool \
    --priority Spot \
    --eviction-policy Delete \
    --spot-max-price -1 \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --no-wait
```

Por padrão, você cria um pool de nós com  uma prioridade *regular* em seu cluster AKs ao criar um cluster com vários pools de nós. O comando acima adiciona um pool de nós auxiliares a um cluster AKS existente com uma *prioridade* de *Spot*. A *prioridade* de *Spot* torna o pool de nós um pool de nós Spot. O parâmetro de *política de remoção* é definido como *excluir* no exemplo acima, que é o valor padrão. Quando você define a [política de remoção][eviction-policy] a ser *excluída*, os nós no conjunto de dimensionamento subjacente do pool de nós são excluídos quando são removidos. Você também pode definir a política de remoção como *desalocar*. Quando você define a política de remoção como *desalocar*, os nós no conjunto de dimensionamento subjacente são definidos como o estado parado-desalocado após a remoção. Os nós na contagem de estado parado-desalocado em relação à sua cota de computação e podem causar problemas com o dimensionamento ou a atualização do cluster. Os valores de *política* de *prioridade* e remoção só podem ser definidos durante a criação do pool de nós. Esses valores não podem ser atualizados posteriormente.

O comando também habilita o [dimensionador de cluster][cluster-autoscaler], que é recomendado para uso com pools de nós Spot. Com base nas cargas de trabalho em execução no cluster, o dimensionamento automática do cluster é dimensionado e escala verticalmente o número de nós no pool de nós. Para pools de nós de spot, o dimensionador automática de cluster aumentará o número de nós após uma remoção se nós adicionais ainda forem necessários. Se você alterar o número máximo de nós que um pool de nós pode ter, também precisará ajustar o `maxCount` valor associado ao cluster de dimensionamento automática. Se você não usar um conjunto de dimensionamento de clusters, após a remoção, o pool de pontos será reduzido para zero e exigirá uma operação manual para receber outros nós especiais.

> [!Important]
> Só agende cargas de trabalho em pools de nós especiais que possam lidar com interrupções, como trabalhos de processamento em lotes e ambientes de teste. É recomendável que você configure os de [Tolerations][taints-tolerations] no pool de nós spot para garantir que somente as cargas de trabalho que podem lidar com as remoções de nó sejam agendadas em um pool de nós Spot. Por exemplo, o comando acima de NY padrão adiciona um *kubernetes.Azure.com/scalesetpriority=spot:NoSchedule* de que somente os pods com um toleration correspondente estão agendados neste nó.

## <a name="verify-the-spot-node-pool"></a>Verificar o pool de nós Spot

Para verificar se o pool de nós foi adicionado como um pool de nós de spot:

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

Confirme se o *scaleSetPriority* é um *ponto*.

Para agendar um pod para ser executado em um nó de spot, adicione um toleration que corresponda ao seu nó de spot aplicado. O exemplo a seguir mostra uma parte de um arquivo YAML que define um toleration que *corresponde a um o seu* que é usado na etapa anterior.

```yaml
spec:
  containers:
  - name: spot-example
  tolerations:
  - key: "kubernetes.azure.com/scalesetpriority"
    operator: "Equal"
    value: "spot"
    effect: "NoSchedule"
   ...
```

Quando um pod com esse toleration é implantado, o kubernetes pode agendar com êxito o pod nos nós com o seu

## <a name="max-price-for-a-spot-pool"></a>Preço máximo para um pool de pontos
O [preço para instâncias especiais é variável][pricing-spot], com base na região e SKU. Para obter mais informações, consulte os preços para [Linux][pricing-linux] e [Windows][pricing-windows].

Como o preço é variável, você tem a opção de definir um preço máximo, em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, o valor *0,98765* seria um preço máximo de $0.98765 USD por hora. Se você definir o preço máximo como *-1*, a instância não será removida com base no preço. O preço da instância será o preço atual para o ponto ou o preço de uma instância padrão, o que for menor, contanto que haja capacidade e cota disponível.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a adicionar um pool de nós Spot a um cluster AKS. Para obter mais informações sobre como controlar os pods nos pools de nós, consulte [práticas recomendadas para recursos avançados do Agendador no AKs][operator-best-practices-advanced-scheduler].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az-aks-nodepool-add
[cluster-autoscaler]: cluster-autoscaler.md
[eviction-policy]: ../virtual-machine-scale-sets/use-spot.md#eviction-policy
[kubernetes-concepts]: concepts-clusters-workloads.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[pricing-linux]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/
[pricing-spot]: ../virtual-machine-scale-sets/use-spot.md#pricing
[pricing-windows]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/
[spot-toleration]: #verify-the-spot-node-pool
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[use-multiple-node-pools]: use-multiple-node-pools.md
[vmss-spot]: ../virtual-machine-scale-sets/use-spot.md