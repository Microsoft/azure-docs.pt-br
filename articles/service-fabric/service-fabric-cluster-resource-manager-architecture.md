---
title: Arquitetura do Gerenciador de recursos
description: Uma visão geral do e informações arquitetônicas sobre o serviço do Gerenciador de recursos de Cluster Service Fabric do Azure.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 0aff55810508fedcf354fba3d9fca9f7a402029b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94685827"
---
# <a name="cluster-resource-manager-architecture-overview"></a>Visão geral da arquitetura do Gerenciador de Recursos de Cluster
O Gerenciador de Recursos de Cluster do Service Fabric é um serviço central que é executado no cluster. Ele gerencia o estado desejado dos serviços no cluster, particularmente com relação ao consumo de recursos e às regras de posicionamento. 

Para gerenciar os recursos em seu cluster, o Gerenciador de Recursos de Cluster do Service Fabric precisa ter várias informações:

- Quais serviços existem atualmente
- O consumo de recursos atual (ou padrão) de cada serviço 
- A capacidade restante do cluster 
- A capacidade dos nós no cluster 
- A quantidade de recursos consumidos em cada nó

O consumo de recursos de um determinado serviço pode mudar com o tempo e, geralmente, os serviços consideram mais de um tipo de recurso. Em serviços diferentes, pode haver recursos físicos e físicos reais que estão sendo medidos. Os serviços podem rastrear métricas físicas, como consumo de disco e memória. Com mais frequência, os serviços podem considerar métricas lógicas — como "WorkQueueDepth" ou "TotalRequests". Métricas lógicas e físicas podem ser usadas no mesmo cluster. As métricas podem ser compartilhadas entre vários serviços ou podem ser específicas de um serviço em particular.

## <a name="other-considerations"></a>Outras considerações
Os proprietários e operadores do cluster podem ser diferentes dos autores do serviço e do aplicativo ou, no mínimo, são as mesmas pessoas desempenhando funções diferentes. Quando desenvolve seu aplicativo, você sabe quais são algumas das coisas que ele requer. Você tem uma estimativa dos recursos que ele consumirá e como diferentes serviços devem ser implantados. Por exemplo, a camada da Web precisa ser executada em nós expostos à Internet, enquanto os serviços de banco de dados não. Outro exemplo são os serviços Web, provavelmente são restritos pela rede e pela CPU, enquanto os serviços da camada de dados têm mais relação com o consumo de memória e disco. No entanto, a pessoa que manipula um incidente de site ao vivo para esse serviço em produção ou que está gerenciando uma atualização do serviço, tem um trabalho diferente a fazer, que exige ferramentas diferentes. 

O cluster e os serviços são dinâmicos:

- O número de nós no cluster pode aumentar e diminuir
- Nós de diferentes tamanhos e tipos podem entrar e sair
- Serviços podem ser criados, removidos e podem alterar as respectivas alocações de recursos e regras de posicionamento desejadas
- Atualizações ou outras operações de gerenciamento podem passar pelo cluster até o aplicativo percorrendo os níveis de infraestrutura
- Falhas podem ocorrer a qualquer momento.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Componentes e fluxo de dados do Gerenciador de Recursos de Cluster
O Gerenciador de Recursos de Cluster precisa controlar os requisitos de cada serviço e o consumo de recursos por cada objeto de serviço dentro desses serviços. O Gerenciador de Recursos de Cluster tem duas partes conceituais: agentes que são executados em cada nó e um serviço tolerante a falhas. Os agentes em cada nó rastreiam os relatórios de carga dos serviços, agregando-os e reportando-os periodicamente. O serviço do Gerenciador de Recursos de Cluster agrega todas as informações dos agentes locais e reage com base em sua configuração atual.

Vamos observar o seguinte diagrama:

<center>

![O diagrama que mostra o serviço do Gerenciador de recursos de cluster agrega todas as informações dos agentes locais e reage com base em sua configuração atual.][Image1]
</center>

No runtime, muitas alterações poderiam acontecer. Por exemplo, digamos que a quantidade de recursos que alguns recursos consomem muda, alguns serviços falham e alguns nós entram e saem do cluster. Todas as alterações em um nó são agregadas e enviadas periodicamente ao serviço do Gerenciador de Recursos de Cluster (1 e 2), onde são agregadas novamente, analisadas e armazenadas. Em intervalo de segundos, o serviço analisa todas as alterações e determina se alguma ação é necessária (3). Por exemplo, ele poderia observar que alguns nós vazios foram adicionados ao cluster. Como resultado, ele decide mover alguns serviços para esses nós. O Gerenciador de Recursos de Cluster também poderia observar que um nó específico está sobrecarregado ou que determinados serviços falharam ou foram excluídos, liberando recursos em outro lugar.

Vamos observar o próximo diagrama e ver o que acontece em seguida. Digamos que o Gerenciador de Recursos de Cluster determine que é necessário fazer alterações. Ele se organiza com outros serviços do sistema (em especial, o Gerenciador de Failover) para fazer as alterações necessárias. Em seguida, os comandos necessários são enviados aos nós apropriados (4). Por exemplo, digamos que o Gerenciador de Recursos tenha observado que o Node5 estava sobrecarregado e, então, decidiu mover o serviço B do Node5 para o Node4. No final da reconfiguração (5), o cluster terá esta aparência:

<center>

![Arquitetura do balanceador de recursos][Image2]
</center>

## <a name="next-steps"></a>Próximas etapas
- O Cluster Resource Manager tem muitas opções para descrever o cluster. Para saber mais sobre eles, confira este artigo sobre como [descrever um cluster Service Fabric](./service-fabric-cluster-resource-manager-cluster-description.md)
- As tarefas principais do Gerenciador de Recursos de Cluster são o rebalanceamento do cluster e a imposição de regras de posicionamento. Para obter mais informações sobre como configurar esses comportamentos, consulte [Balanceamento do cluster do Service Fabric](./service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
