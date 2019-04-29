---
title: Desfragmentação de métricas no Azure Service Fabric | Microsoft Docs
description: Uma visão geral do uso de desfragmentação ou como uma estratégia de métricas no Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: e5ebfae5-c8f7-4d6c-9173-3e22a9730552
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 6e041e41372c72c6792c1fb4a1fbdc3bbe475b21
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844372"
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Desfragmentação de métricas e carga no Service Fabric
A estratégia padrão do Gerenciador de Recursos do Service Fabric para gerenciar métricas de carga no cluster é distribuir a carga. Garantir que os nós sejam utilizados de maneira uniforme evita pontos altos e baixos que levam à contenção e ao desperdício de recursos. A distribuição das cargas de trabalho no cluster também é a opção mais segura em termos de sobrevivência a falhas, uma vez que garante que uma falha não destruirá um grande percentual de uma determinada carga de trabalho. 

O Gerenciador de Recursos de Cluster do Service Fabric Cluster dá suporte a uma estratégia diferente para gerenciar a carga, que é a desfragmentação. Desfragmentação significa que, em vez de tentar distribuir a utilização de uma métrica em todo o cluster, ela será consolidada. A consolidação é apenas uma inversão da estratégia de balanceamento padrão – em vez de minimizar o desvio padrão médio da carga da métrica, o Gerenciador de Recursos de Cluster tenta aumentá-lo.

## <a name="when-to-use-defragmentation"></a>Quando usar a desfragmentação
Distribuir a carga no cluster consome alguns dos recursos de cada nó. Algumas cargas de trabalho criam serviços que são excepcionalmente grandes e consomem a maior parte de um nó. Nesses casos, é possível que, quando houver grandes cargas de trabalho sendo criadas, não haja espaço suficiente em nenhum nó para executá-las. Cargas de trabalho grandes não são um problema no Service Fabric. Nesses casos, o Gerenciador de Recursos de Cluster determina que precisa reorganizar o cluster para liberar espaço para essa grande carga de trabalho. No entanto, nesse intervalo, essa carga de trabalho precisa esperar para ser agendada no cluster.

Se houver muitos serviços e estados, pode ser que demore um longo tempo para que uma carga de trabalho grande seja colocada no cluster. A probabilidade disso acontecer será maior se outras cargas de trabalho no cluster também forem grandes e, portanto, demorarem mais para serem reorganizadas. A equipe do Service Fabric mediu o tempo de criação em simulações desse cenário. Descobrimos que a criação de serviços grandes passou a demorar muito mais assim que a utilização do cluster atingiu entre 30% e 50%. Para lidar com essa situação, introduzimos a desfragmentação como uma estratégia de balanceamento. Descobrimos que, para grandes cargas de trabalho, especialmente aquelas em que o tempo de criação era importante, a desfragmentação realmente ajudava no agendamento dessas novas cargas de trabalho no cluster.

Você pode configurar as métricas de desfragmentação para que o Gerenciador de Recursos de Cluster tente, de modo proativo, condensar a carga dos serviços em menos nós. Isso ajuda a garantir que quase sempre haja espaço para serviços grandes sem precisar reorganizar o cluster. Não ter necessidade de reorganizar o cluster permite criar grandes cargas de trabalho rapidamente.

A maioria das pessoas não precisa da desfragmentação. Normalmente, os serviços são pequenos e, portanto, não é difícil encontrar espaço para eles no cluster. Quando reorganização é possível, ela ocorre rapidamente, novamente porque a maioria dos serviços são pequenos e podem ser movidos rapidamente e em paralelo. No entanto, se você tiver serviços grandes e precisar que eles sejam criados rapidamente, a estratégia de desfragmentação será ideal para você. Discutiremos as vantagens e desvantagens do uso de desfragmentação em seguida. 

## <a name="defragmentation-tradeoffs"></a>Vantagens e desvantagens da desfragmentação
A desfragmentação pode aumentar o impacto das falhas, já que mais serviços estão em execução nos nós que falham. A desfragmentação também pode aumentar custos, pois os recursos do cluster precisam ser mantidos em reserva, aguardando a criação de grandes cargas de trabalho.

O diagrama a seguir fornece uma representação visual de dois clusters, um deles desfragmentado e o outro não. 

<center>

![Comparando Clusters balanceados e desfragmentados][Image1]
</center>

No caso do cluster balanceado, considere o número de movimentações que seria necessário para colocar um dos maiores objetos de serviço. No cluster desfragmentado, a carga de trabalho grande pode ser colocada nos nós quatro ou cinco sem precisar esperar que outros serviços se movam.

## <a name="defragmentation-pros-and-cons"></a>Prós e contras da desfragmentação
Quais são as outras compensações conceituais? Aqui está uma tabela rápida de itens a ser considerados:

| Vantagens da desfragmentação | Desvantagens da desfragmentação |
| --- | --- |
| Agiliza a criação de serviços grandes |Concentra a carga em menos nós, aumentando a contenção |
| Permite a redução da movimentação de dados durante a criação |Falhas podem afetar mais serviços e causar mais variação |
| Permite a descrição detalhada dos requisitos e a recuperação de espaço |Configuração geral mais complexa do Gerenciamento de Recursos |

Você pode misturar métricas desfragmentadas e normais no mesmo cluster. O Gerenciador de Recursos de Cluster tenta consolidar o maior número de métricas de desfragmentação enquanto distribui as outras. Os resultados de uma combinação das estratégias de desfragmentação e balanceamento dependem de vários fatores, incluindo:
  - o número de métricas de balanceamento versus o número de métricas de desfragmentação
  - Se algum serviço usar os dois tipos de métricas 
  - os pesos das métricas
  - as cargas da métrica atual
  
O experimento é necessário para determinar a configuração exata necessária. Recomendamos fazer uma medição completa de suas cargas de trabalho antes de habilitar as métricas de desfragmentação na produção. Isso vale especialmente ao combinar métricas de desfragmentação e balanceamento dentro do mesmo serviço. 

## <a name="configuring-defragmentation-metrics"></a>Configurando métricas de desfragmentação
Configurar métricas de desfragmentação é uma decisão global no cluster e métricas individuais podem ser selecionadas para desfragmentação. Os snippets de configuração a seguir mostram como configurar métricas para desfragmentação. Neste caso, "Metric1" é configurada como uma métrica de desfragmentação, enquanto "Metric2" continuará sendo balanceada normalmente. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

via ClusterConfig.json para implantações Autônomas ou Template.json para clusters hospedados pelo Azure:

```json
"fabricSettings": [
  {
    "name": "DefragmentationMetrics",
    "parameters": [
      {
          "name": "Metric1",
          "value": "true"
      },
      {
          "name": "Metric2",
          "value": "false"
      }
    ]
  }
]
```


## <a name="next-steps"></a>Próximas etapas
- O Gerenciador de Recursos de Cluster tem muitas opções para descrever o cluster. Para saber mais sobre elas, confira este artigo sobre a [descrição de um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
- As métricas são como o Gerenciador de Recursos de Cluster do Service Fabric gerencia o consumo e a capacidade no cluster. Para saber mais sobre as métricas e como configurá-las, confira [este artigo](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
