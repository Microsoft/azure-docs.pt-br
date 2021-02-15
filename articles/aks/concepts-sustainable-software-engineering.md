---
title: Conceitos-engenharia de software sustentável nos serviços Kubernetess do Azure (AKS)
description: Saiba mais sobre a engenharia de software sustentável no AKS (serviço de kubernetes do Azure).
services: container-service
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 700723041855fdae4f994480d180292fdfd12e15
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97346887"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>Princípios de engenharia de software sustentável no serviço de kubernetes do Azure (AKS)

Os princípios de engenharia de software sustentável são um conjunto de competências para ajudá-lo a definir, criar e executar aplicativos sustentáveis. O objetivo geral é reduzir seu espaço de carbono de todos os aspectos do seu aplicativo. [Os princípios da engenharia de software sustentável][principles-sse] tem uma visão geral dos princípios da engenharia de software sustentável.

Uma ideia importante entender sobre a engenharia de software sustentável é que ela é uma mudança nas prioridades e no foco. Em muitos casos, o software é projetado e executado de forma a se concentrar no desempenho rápido e na baixa latência. A engenharia de software sustentável se concentra em reduzir o máximo possível de emissões de carbono. Em alguns casos, a aplicação de princípios de engenharia de software sustentável pode proporcionar um desempenho mais rápido ou menor latência, como ao reduzir a viagem de rede total. Em outros casos, reduzir as emissões de carbono pode causar um desempenho mais lento ou maior latência, como atrasar cargas de trabalho de baixa prioridade. Antes de considerar a aplicação de princípios de engenharia de software sustentável ao seu aplicativo, examine as prioridades, as necessidades e as compensações de seu aplicativo.

## <a name="measure-and-optimize"></a>Medir e otimizar

Para diminuir o volume de carbono dos clusters do AKS, você precisa entender como os recursos do cluster estão sendo usados. [Azure monitor][azure-monitor] fornece detalhes sobre o uso de recursos do cluster, como a memória e o uso da CPU. Esses dados podem ajudar a informar suas decisões para reduzir o volume de carbono do cluster e observar o efeito de suas alterações. Você também pode instalar a [calculadora de sustentabilidade da Microsoft][sustainability-calculator] para ver a superfície de carbono de todos os seus recursos do Azure.

## <a name="increase-resource-utilization"></a>Aumentar a utilização de recursos

Uma abordagem para reduzir seu espaço de carbono é reduzir a quantidade de tempo ocioso para seus recursos de computação. Reduzir seu tempo ocioso envolve aumentar a utilização de seus recursos de computação. Por exemplo, se você tivesse quatro nós em seu cluster, cada um executando às 50% de capacidade, todos os quatro nós têm 50% de capacidade não utilizada permanecendo ociosa. Se você reduziu o cluster para três nós, a mesma carga de trabalho faria com que os três nós fossem executados a uma capacidade de 67%, reduzindo sua capacidade não utilizada para 33% em cada nó e aumentando sua utilização.

> [!IMPORTANT]
> Ao considerar fazer alterações nos recursos do cluster, verifique se os [pools do sistema][system-pools] têm recursos suficientes para manter a estabilidade dos componentes principais do sistema do cluster. Nunca reduza os recursos do cluster para o ponto em que o cluster pode se tornar instável.

Depois de examinar a utilização do cluster, considere o uso dos recursos oferecidos por [vários pools de nós][multiple-node-pools]. Você pode usar o [dimensionamento de nó][node-sizing] para definir pools de nós com perfis de memória e CPU específicos, permitindo que você personalize seus nós para suas necessidades de carga de trabalho. Dimensionar seus nós para suas necessidades de carga de trabalho pode ajudá-lo a executar alguns nós em maior utilização. Você também pode configurar o modo como o cluster é [dimensionado][scale] e usar o dimensionamento automático de [Pod horizontal][scale-horizontal] e o [cluster AutoScaler][scale-auto] para dimensionar o cluster automaticamente com base em sua configuração. Controlar como as escalas de cluster pode ajudá-lo a manter todos os seus nós em execução com uma alta utilização, mantendo as alterações na carga de trabalho do cluster. Para casos em que uma carga de trabalho é tolerante a interrupções repentinas ou terminações, você pode usar [pools de pontos][spot-pools] para aproveitar a capacidade ociosa no Azure. Por exemplo, os pools de spot podem funcionar bem para trabalhos em lotes ou ambientes de desenvolvimento.

O aumento da utilização também pode reduzir os nós em excesso, o que reduz a energia consumida pelas [reservas de recursos em cada nó][resource-reservations].

Examine também as *solicitações* de CPU e de memória e *os limites* nos manifestos kubernetes de seus aplicativos. À medida que você reduz esses valores para memória e CPU, mais memória e CPU estão disponíveis para o cluster para executar outras cargas de trabalho. À medida que você executa mais cargas de trabalho com CPU e memória menores, o cluster torna-se alocado mais denso, o que aumenta a utilização. Ao reduzir a CPU e a memória para seus aplicativos, o comportamento de seus aplicativos poderá ficar degradado ou instável se você definir esses valores muito baixos. Antes de alterar as *solicitações* e *limites* de CPU e memória, considere a execução de alguns testes de benchmark para entender se esses valores estão definidos adequadamente. Além disso, nunca reduza esses valores para o ponto em que seu aplicativo se torna instável.

## <a name="reduce-network-travel"></a>Reduzir a viagem de rede

Reduzir as solicitações de distância e respostas de e para o cluster tem que viajar geralmente reduz o consumo de eletricidade por dispositivos de rede e reduz as emissões de carbono. Depois de examinar o tráfego de rede, considere a criação de clusters [em regiões][regions] mais próximas à origem do seu tráfego de rede. Você também pode usar o [Gerenciador de tráfego do Azure][azure-traffic-manager] para ajudar a rotear o tráfego para o cluster mais próximo e os [grupos de posicionamento de proximidade][proiximity-placement-groups] para ajudar a reduzir a distância entre os recursos do Azure.

> [!IMPORTANT]
> Ao considerar fazer alterações na rede do cluster, nunca reduza a viagem de rede ao custo de atender aos requisitos de carga de trabalho. Por exemplo, o uso de [zonas de disponibilidade][availability-zones] causa mais viagem de rede em seu cluster, mas o uso desse recurso pode ser necessário para lidar com os requisitos de carga de trabalho.

## <a name="demand-shaping"></a>Modelagem de demanda

Sempre que possível, considere alterar a demanda dos recursos do cluster para os horários ou regiões em que você pode usar a capacidade em excesso. Por exemplo, considere alterar o tempo ou a região para que um trabalho em lotes seja executado ou use [pools de spot][spot-pools]. Considere também refatorar seu aplicativo para usar uma fila para adiar as cargas de trabalho em execução que não precisam de processamento imediato.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os recursos do AKS mencionados neste artigo:

* [Vários pools de nós][multiple-node-pools]
* [Dimensionamento de nó][node-sizing]
* [Dimensionamento de um cluster][scale]
* [Dimensionador automático de pod horizontal][scale-horizontal]
* [Dimensionador automático de cluster][scale-auto]
* [Pools de pontos][spot-pools]
* [Pools do sistema][system-pools]
* [Reservas de recursos][resource-reservations]
* [Grupos de posicionamento de proximidade][proiximity-placement-groups]
* [Zonas de Disponibilidade][availability-zones]

[availability-zones]: availability-zones.md
[azure-monitor]: ../azure-monitor/insights/container-insights-overview.md
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