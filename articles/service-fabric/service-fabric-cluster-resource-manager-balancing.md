---
title: Equilibrar seu cluster do Azure Service Fabric | Microsoft Docs
description: Uma introdução ao balanceamento de cluster com o Gerenciador de Recursos de Cluster do Service Fabric.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 030b1465-6616-4c0b-8bc7-24ed47d054c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 74fe4f7c4c231f80c7555f39f840a85baae310e9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60809390"
---
# <a name="balancing-your-service-fabric-cluster"></a>Balanceamento do cluster do Service Fabric
O Gerenciador de Recursos de Cluster do Service Fabric oferece suporte a alterações de carga dinâmico, reagindo a inclusões ou remoções de nós ou serviços. Ele também corrige automaticamente as violações de restrição e, proativamente, balanceia novamente o cluster. Mas com que frequência essas ações são executadas, e o que as dispara?

Há três categorias diferentes de trabalho executadas pelo Gerenciador de Recursos de Cluster. Eles são:

1. Posicionamento – esse estágio lida com o posicionamento de réplicas com estado ou de instâncias sem estado que estejam ausentes. O posicionamento inclui os novos serviços e a manipulação de réplicas com estado ou de instâncias sem estado que falharam. A exclusão e o descarte de réplicas ou de instâncias são tratados aqui.
2. Verificações de Restrição – esse estágio verifica e corrige violações das restrições (regras) de posicionamento no sistema. Os exemplos de regras são a garantia de que os nós não estão acima da capacidade e se as restrições de posicionamento do serviço estão sendo atendidas.
3. Balanceamento – este estágio verifica se o rebalanceamento é necessário com base no nível desejado de saldo para métricas diferentes configurado. Nesse caso, ele tenta localizar uma organização em cluster que é mais equilibrado.

## <a name="configuring-cluster-resource-manager-timers"></a>Configuração de temporizadores do Gerenciador de Recursos de Cluster
O primeiro conjunto de controles de balanceamento são um conjunto de temporizadores. Esses temporizadores determinam a frequência com que o Gerenciador de Recursos de Cluster examina o cluster e executa as ações corretivas.

Cada um desses tipos diferentes de correções que o Gerenciador de Recursos de Cluster pode fazer é controlado por um temporizador diferente que rege sua frequência. Quando cada temporizador é acionado, a tarefa é agendada. Por padrão, o Resource Manager:

* verifica o estado e aplica atualizações (como gravação de um nó estiver inativo) cada 1/10 de segundo
* define o sinalizador de verificação de posicionamento 
* define o sinalizador de verificação de restrição a cada segundo
* Define o sinalizador de balanceamento a cada cinco segundos.

Veja a seguir exemplos de configuração que governam esses temporizadores:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

via ClusterConfig.json para implantações Autônomas ou Template.json para clusters hospedados pelo Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PLBRefreshGap",
          "value": "0.10"
      },
      {
          "name": "MinPlacementInterval",
          "value": "1.0"
      },
      {
          "name": "MinConstraintCheckInterval",
          "value": "1.0"
      },
      {
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

Atualmente o Resource Manager de Cluster executa apenas uma dessas ações ao mesmo tempo, sequencialmente. É por isso que fazemos referência a esses temporizadores como "intervalos mínimos" e as ações realizadas quando os temporizadores desligam como "sinalizadores de configuração". Por exemplo, o Resource Manager de Cluster se encarrega de solicitações pendentes para criar serviços antes do cluster de balanceamento. Como você pode ver, os intervalos de tempo padrão especificados, o Gerenciador de Recursos de Cluster examina se há qualquer coisa que precisa ser feito com frequência. Normalmente, isso significa que o conjunto de alterações feitas durante cada etapa é pequeno. Fazer pequenas alterações frequentemente faz com que o Gerenciador de Recursos de Cluster responda às coisas que acontecem no cluster. Os temporizadores padrão oferecem algum lote, já que muitos dos mesmos tipos de eventos tendem a ocorrer simultaneamente. 

Por exemplo, quando os nós falham, eles podem fazer com um domínio de falha inteiro por vez. Todas essas falhas são capturadas durante a próxima atualização de estado após o *PLBRefreshGap*. As correções são determinadas durante as seguintes execuções de posicionamento, verificação de restrição e balanceamento. Por padrão o Resource Manager de Cluster não é uma varredura horas de alterações no cluster e tentando resolver todas as alterações ao mesmo tempo. Isso levaria a picos de variação.

O Resource Manager de Cluster também precisa de algumas informações adicionais para determinar se o cluster desequilibrado. Para fazer isso, temos duas outras partes da configuração: *Limites de balanceamento* e *limites de atividade*.

## <a name="balancing-thresholds"></a>Limites de balanceamento
Um Limite de Balanceamento é o controle principal que dispara o rebalanceamento. O Limite de Balanceamento para uma métrica é uma _razão_. Se a carga de uma métrica no nó mais carregado dividido pela quantidade de carga no nó menos carregado excede o *Limite de Balanceamento* dessa métrica, o cluster é desequilibrado. Como resultado de balanceamento é disparada na próxima vez que o Resource Manager de Cluster verifica. O temporizador *MinLoadBalancingInterval* define a frequência com que o Gerenciador de Recursos de Cluster deve verificar se o rebalanceamento é necessário. A verificação não significa que nada acontece. 

Os Limites de Balanceamento são definidos baseados em cada métrica, como parte da definição do cluster. Para saber mais sobre como fazer isso, leia [este artigo](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

via ClusterConfig.json para implantações Autônomas ou Template.json para clusters hospedados pelo Azure:

```json
"fabricSettings": [
  {
    "name": "MetricBalancingThresholds",
    "parameters": [
      {
          "name": "MetricName1",
          "value": "2"
      },
      {
          "name": "MetricName2",
          "value": "3.5"
      }
    ]
  }
]
```

<center>

![Exemplo de limite de balanceamento][Image1]
</center>

Neste exemplo, cada serviço está consumindo uma unidade de alguma métrica. No exemplo superior, a carga máxima em um nó é cinco e o mínimo é dois. Digamos que o limite de balanceamento para esta métrica seja três. Como a proporção do cluster é 5/2 = 2,5 e é menor do que o especificado balanceamento de limite de três, o cluster é equilibrado. Nenhum balanceamento é disparada quando verifica se o Resource Manager de Cluster.

No exemplo inferior, a carga máxima em um nó é dez, enquanto o mínimo é dois, resultando em uma taxa de cinco. Cinco é maior que o limite de balanceamento designado de três para métrica. Como resultado, uma execução de rebalanceamento será agendada na próxima vez em que o temporizador de balanceamento for acionado. Em uma situação como essa, alguma carga normalmente é distribuída para Node3. Como o Gerenciador de Recursos de Cluster do Service Fabric não usa uma abordagem egoísta, alguma carga também pode ser distribuída para Node2. 

<center>

![Ações de exemplo de limite de balanceamento][Image2]
</center>

> [!NOTE]
> O "Balanceamento" trata duas estratégias diferentes para gerenciar a carga em seu cluster. É a estratégia padrão que o Gerenciador de Recursos de Cluster usa para distribuir a carga entre os nós no cluster. A outra estratégia é [desfragmentação](service-fabric-cluster-resource-manager-defragmentation-metrics.md). A desfragmentação é executada durante a mesma execução de balanceamento. As estratégias de balanceamento e desfragmentação podem ser usadas para métricas diferentes dentro do mesmo cluster. Um serviço pode ter métricas de balanceamento e desfragmentação. Para métricas de desfragmentação, a taxa de cargas no cluster dispara o rebalanceamento quando ele estiver _abaixo_ do limite de balanceamento. 
>

Obtendo abaixo do limite de balanceamento não é um objetivo explícito. Limites de Balanceamento são apenas um *gatilho*. Quando balanceamento é executado, o Gerenciador de Recursos de Cluster determina quais melhorias ele pode fazer, se houver alguma. Só porque uma pesquisa de balanceamento é iniciada não significa que nada será movimentado. Às vezes, o cluster é desequilibrado, mas fica muito restrito para corrigir. Como alternativa, os aprimoramentos exigem movimentações muito [dispendiosas](service-fabric-cluster-resource-manager-movement-cost.md)).

## <a name="activity-thresholds"></a>Limites de Atividade
Às vezes, embora os nós estejam relativamente desequilibrados, a quantidade *total* de carga no cluster é baixa. A falta de carga pode ser um dip transitório, ou porque o cluster é novo e está apenas começando a ser inicializado. Em ambos os casos, talvez não queira gastar tempo o cluster de balanceamento porque não há muito a ser obtido. Se o cluster passou por balanceamento, você gastaria recursos de rede e computação para mover as coisas sem fazer uma diferença *absoluta*. Para evitar movimentações desnecessárias, há outro controle conhecido como Limites de Atividade. Limites de atividade permite que você especifique algum limite inferior absoluto para a atividade. Se nenhum nó está acima desse limite, balanceamento não é disparado mesmo se o limite de balanceamento é atingido.

Vamos supor que podemos manter nosso Limite de Balanceamento de três para essa métrica. Vamos supor também que temos um Limite de Atividade de 1536. No primeiro caso, embora o cluster esteja desequilibrado pelo Limite de Balanceamento, nenhum nó atende ao Limite de Atividade e, portanto, nada acontece. No exemplo inferior, Node1 está acima do limite de atividade. Como o Limite de Balanceamento e o Limite de Atividade para a métrica foram ultrapassados, o balanceamento é agendado. Por exemplo, vamos examinar o diagrama a seguir: 

<center>

![Exemplo de limite de atividade][Image3]
</center>

Assim como os Limites de Balanceamento, os Limites de Atividade são definidos por métrica por meio da definição do cluster:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

via ClusterConfig.json para implantações Autônomas ou Template.json para clusters hospedados pelo Azure:

```json
"fabricSettings": [
  {
    "name": "MetricActivityThresholds",
    "parameters": [
      {
          "name": "Memory",
          "value": "1536"
      }
    ]
  }
]
```

Limites de balanceamento e atividade estão ambos vinculados a uma métrica específica - balanceamento é disparado somente se o limite de balanceamento e atividade limite é excedido para a mesma métrica.

> [!NOTE]
> Quando não especificado, o limite de balanceamento para uma métrica é 1 e o limite de atividade é 0. Isso significa que o Gerenciador de Recursos de Cluster tentará manter essa métrica perfeitamente balanceada para qualquer carga fornecida. Se você estiver usando métricas personalizadas, é recomendável que você defina explicitamente seus próprios limites de balanceamento e de atividade para suas métricas. 
>

## <a name="balancing-services-together"></a>Balanceamento dos serviços em conjunto
Se o cluster estiver desequilibrado ou não for uma decisão de todo o cluster. No entanto, a maneira como nós vamos corrigi-lo é mover réplicas de serviço individuais e instâncias. Isso faz sentido, certo? Se a memória é empilhada em um nó, várias réplicas ou instâncias podem contribuir para ela. Corrigir o desequilíbrio pode exigir a movimentação de qualquer uma das réplicas com monitoração de estado ou instâncias sem monitoração de estado que usam a métrica desequilibrada.

Ocasionalmente, no entanto, um serviço que não estava desequilibrado é movido (lembre-se da discussão sobre pesos local e global anteriormente). Por que um serviço é movido quando todas as métricas desse serviço foram balanceadas? Vejamos um exemplo:

- Vamos supor que há quatro serviços, Service1, Service2, Service3 e Service4. 
- O Service1 relata as métricas Metric1 e Metric2. 
- O Service2 relata as métricas Metric2 e Metric3. 
- O Service3 relata as métricas Metric3 e Metric4.
- O Service4 relata a métrica Metric99. 

Certamente, você pode ver onde queremos chegar: Há uma cadeia! Realmente, não há quatro serviços independentes. Temos três serviços relacionados e um que está por conta própria.

<center>

![Balanceamento dos serviços juntos][Image4]
</center>

Devido a essa cadeia, é possível que um desequilíbrio nas métricas 1 a 4 possa mover as réplicas ou instâncias pertencentes aos serviços 1 a 3. Também sabemos que um desequilíbrio nas métricas de 1, 2 ou 3 não pode causar movimentos no Service4. Não haveria nenhum ponto desde movendo as réplicas ou instâncias pertencentes a Serviço4 ao redor podem fazer absolutamente nada para afetar o saldo das métricas de 1 a 3.

O Gerenciador de Recursos de Cluster descobre automaticamente quais serviços estão relacionados. A adição, remoção ou alteração das métrica para esses serviços pode afetar suas relações. Por exemplo, entre duas execuções de balanceamento, o Service2 pode ter sido atualizado para remover Metric2. Isso interromperá a cadeia entre Service1 e Service2. Agora, em vez de dois grupos de serviços relacionados, há três:

<center>

![Balanceamento dos serviços juntos][Image5]
</center>

## <a name="next-steps"></a>Próximas etapas
* As métricas são como o Gerenciador de Recursos de Cluster do Service Fabric gerencia o consumo e a capacidade no cluster. Para saber mais sobre as métricas e como configurá-las, confira [este artigo](service-fabric-cluster-resource-manager-metrics.md)
* O Custo de Movimento é uma forma de sinalizar para o Gerenciador de Recursos de Cluster que a movimentação de determinados serviços é mais cara do que para outros. Para saber mais sobre o custo de movimento, consulte [este artigo](service-fabric-cluster-resource-manager-movement-cost.md)
* O Resource Manager do Cluster tem várias limitações que você pode configurar para diminuir a variação no cluster. Normalmente, eles não são necessários, mas você poderá aprender mais sobre eles [aqui](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
