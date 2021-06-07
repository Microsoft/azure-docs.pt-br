---
title: Conceitos – Engenharia de software sustentável no AKS (Serviço de Kubernetes do Azure)
description: Saiba mais sobre a engenharia de software sustentável no AKS (Serviço de Kubernetes do Azure).
services: container-service
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: e179b49879b78b2bab738407984c0f50d161114b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572671"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>Princípios de engenharia de software sustentável no AKS (Serviço de Kubernetes do Azure)

Os princípios de engenharia de software sustentável são um conjunto de competências para ajudar você a definir, criar e executar aplicativos sustentáveis. A meta geral é reduzir a pegada de carbono em todos os aspectos do seu aplicativo. Você tem uma visão geral nos [Princípios de engenharia de software sustentável][principles-sse].

É importante entender que a engenharia de software sustentável é uma mudança de prioridades e foco. Em muitos casos, o software é projetado e executado de modo a se concentrar no desempenho rápido e na baixa latência. A engenharia de software sustentável se concentra em reduzir o máximo possível as emissões de carbono. Em alguns casos, a aplicação de princípios de engenharia de software sustentável pode proporcionar um desempenho mais rápido ou menor latência, como ao reduzir a distância da rede total. Em outros casos, reduzir as emissões de carbono pode causar um desempenho mais lento ou aumentar a latência, por exemplo, atrasando as cargas de trabalho de baixa prioridade. Antes de considerar usar os princípios de engenharia de software sustentável no seu aplicativo, examine as prioridades, as necessidades e as vantagens de seu aplicativo.

## <a name="measure-and-optimize"></a>Medir e otimizar

Para diminuir a pegada de carbono dos clusters do AKS, você precisa entender como os recursos do cluster estão sendo usados. O [Azure Monitor][azure-monitor] mostra detalhes sobre o uso de recursos do cluster, como a memória e o da CPU. Esses dados podem ajudar a respaldar suas decisões para reduzir a pegada de carbono do cluster e observar o efeito de suas alterações. Você também pode instalar a [Microsoft Sustainability Calculator][sustainability-calculator] para ver a pegada de carbono de todos os seus recursos do Azure.

## <a name="increase-resource-utilization"></a>Aumentar a utilização de recursos

Uma abordagem para reduzir a pegada de carbono é reduzir a quantidade de tempo ocioso dos recursos de computação. Reduzir o tempo ocioso envolve aumentar a utilização de seus recursos de computação. Por exemplo, se você tivesse quatro nós em seu cluster, cada um com uma execução de 50% da capacidade, todos os quatro nós terão 50% de capacidade não utilizada, ociosa. Se você reduzir o cluster para três nós, a mesma carga de trabalho faria com que os três nós fossem executados a uma capacidade de 67%, diminuindo a capacidade não utilizada para 33% em cada nó e aumentando a utilização.

> [!IMPORTANT]
> Ao considerar fazer alterações nos recursos do cluster, verifique se os [pools do sistema][system-pools] têm recursos suficientes para manter a estabilidade dos componentes principais do sistema do cluster. Nunca reduza os recursos do cluster a ponto de deixá-lo instável.

Depois de examinar a utilização do cluster, considere o uso dos recursos oferecidos por [vários pools de nós][multiple-node-pools]. É possível usar o [dimensionamento de nó][node-sizing] para definir pools de nós com perfis específicos de memória e CPU, o que permite personalizar nós para as suas necessidades de carga de trabalho. Dimensionar nós para as suas necessidades de carga de trabalho pode ajudar você a executar alguns nós com maior utilização. Também é possível configurar o modo como o cluster é [dimensionado][scale] e usar o [dimensionador automático de pod horizontalPod horizontal][scale-horizontal] e o [dimensionador automático de cluster][scale-auto] para dimensionar o cluster automaticamente com base em sua configuração. Controlar como o cluster é dimensionado ajuda você a manter todos os nós em execução com alta utilização e a acompanhar as alterações na carga de trabalho de cluster. Nos casos em que a carga de trabalho é tolerante a interrupções ou encerramentos repentinos, use os [pools de spot][spot-pools] para aproveitar a capacidade ociosa no Azure. Por exemplo, os pools de spot podem funcionar bem para trabalhos em lotes ou ambientes de desenvolvimento.

Aumentar a utilização também pode reduzir os nós em excesso, o que diminui a energia consumida pelas [reservas de recursos em cada nó][resource-reservations].

Analise também as *solicitações* e os *limites* de CPU e memória nos manifestos do Kubernetes dos seus aplicativos. À medida que você reduz esses valores para a memória e a CPU, mais memória e CPU ficam disponíveis para o cluster executar outras cargas de trabalho. Conforme você executa mais cargas de trabalho com CPU e memória menores, o cluster fica mais densamente alocado, o que aumenta a utilização. O comportamento dos aplicativos pode ser degradado ou ficar instável quando você reduz a CPU e a memória a valores muito baixos. Antes de alterar as *solicitações* e os *limites* de CPU e memória, considere a execução de alguns testes de parâmetro de comparação para entender se esses valores estão definidos adequadamente. Além disso, nunca reduza esses valores a ponto de deixar o aplicativo instável.

## <a name="reduce-network-travel"></a>Reduzir a distância da rede

Reduzir a distância percorrida por solicitações e respostas do cluster diminui o consumo de eletricidade dos dispositivos de rede e atenua as emissões de carbono. Depois de examinar o tráfego de rede, considere a criação de clusters [em regiões][regions] mais próximas à origem do seu tráfego de rede. Também é possível usar o [Gerenciador de Tráfego do Azure][azure-traffic-manager] para ajudar a rotear o tráfego para o cluster mais próximo e os [grupos de posicionamento por proximidade][proiximity-placement-groups] para ajudar a reduzir a distância entre os recursos do Azure.

> [!IMPORTANT]
> Ao considerar fazer alterações na rede do cluster, nunca reduza a distância da rede a fim de atender aos requisitos de carga de trabalho. Por exemplo, o uso de [zonas de disponibilidade][availability-zones] aumenta a distância da rede em seu cluster, mas o uso desse recurso pode ser necessário para lidar com os requisitos de carga de trabalho.

## <a name="demand-shaping"></a>Modelagem de demanda

Sempre que possível, considere alterar a demanda dos recursos do cluster para os horários ou regiões em que você pode usar a capacidade em excesso. Por exemplo, considere alterar o horário ou a região para que um trabalho em lotes seja executado ou use [pools de spot][spot-pools]. Considere também refatorar seu aplicativo para usar uma fila para adiar as cargas de trabalho em execução que não precisam de processamento imediato.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os recursos do AKS mencionados neste artigo:

* [Vários pools de nós][multiple-node-pools]
* [Dimensionamento de nó][node-sizing]
* [Dimensionamento de um cluster][scale]
* [Dimensionador automático de pod horizontal][scale-horizontal]
* [Dimensionador automático de cluster][scale-auto]
* [Pools de spot][spot-pools]
* [Pools do sistema][system-pools]
* [Reservas de recursos][resource-reservations]
* [Grupos de posicionamento por proximidade][proiximity-placement-groups]
* [Zonas de Disponibilidade][availability-zones]

[availability-zones]: availability-zones.md
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md
[azure-traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[proiximity-placement-groups]: reduce-latency-ppg.md
[regions]: faq.md#which-azure-regions-currently-provide-aks
[resource-reservations]: concepts-clusters-workloads.md#resource-reservations
[scale]: concepts-scale.md
[scale-auto]: concepts-scale.md#cluster-autoscaler
[scale-horizontal]: concepts-scale.md#horizontal-pod-autoscaler
[spot-pools]: spot-node-pool.md
[multiple-node-pools]: use-multiple-node-pools.md
[node-sizing]: use-multiple-node-pools.md#specify-a-vm-size-for-a-node-pool
[sustainability-calculator]: https://azure.microsoft.com/blog/microsoft-sustainability-calculator-helps-enterprises-analyze-the-carbon-emissions-of-their-it-infrastructure/
[system-pools]: use-system-pools.md
[principles-sse]: https://docs.microsoft.com/learn/modules/sustainable-software-engineering-overview/