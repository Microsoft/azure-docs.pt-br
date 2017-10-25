---
title: "Limitação no Resource Manager de Cluster do Service Fabric | Microsoft Docs"
description: Aprenda a configurar os limitadores fornecidos pelo Resource Manager de Cluster do Service Fabric.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 4a44678b-a5aa-4d30-958f-dc4332ebfb63
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 22223923f6783f11178b44c3c3087f17eb8dc41b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>Restringindo o Gerenciador de Recursos de Cluster do Service Fabric
Mesmo se você tiver configurado o Gerenciador de Recursos do Cluster corretamente, o cluster poderá ser interrompido. Por exemplo, poderia haver falhas simultâneas no domínio de falha e no nó – o que aconteceria se isso ocorresse durante um upgrade? O Gerenciador de Recursos de Cluster sempre tenta corrigir tudo, consumindo recursos do cluster ao tentar reorganizar e corrigir o cluster. As restrições ajudam a construir uma barreira para que o cluster possa usar recursos para se estabilizar — os nós voltam, as partições de rede são corrigidas, os bits corrigidos são implantados.

Para ajudar com esses tipos de situação, o Gerenciador de Recursos de Cluster do Service Fabric inclui várias restrições. Essas restrições são exigências razoavelmente grandes. Em geral, elas não devem ser alteradas sem planejamento e teste cuidadosos.

No caso de alterar as restrições do Gerenciador de Recursos de Cluster, você deve ajustá-las para sua carga real esperada. Você pode descobrir que precisa ter algumas restrições definidas, mesmo que isso signifique que o cluster levará mais tempo para ser estabilizado em algumas situações. O teste é obrigatório para determinar os valores corretos das restrições. As restrições precisam ser altas o suficiente para permitir que o cluster responda às alterações em um tempo razoável e baixas o suficiente para de fato impedir o consumo de muito recurso. 

Na maioria das vezes em que vimos os clientes usarem essas restrições era porque eles já estavam em um ambiente com restrição de recursos. Alguns exemplos disso são a largura de banda de rede limitada em nós individuais ou discos que não podem criar muitas réplicas com estado paralelamente devido às limitações da taxa de transferência. Sem restrições, as operações poderiam sobrecarregar esses recursos, fazendo com que as operações falhassem ou ficassem lentas. Nessas situações, os clientes usavam restrições e sabiam que estavam estendendo o tempo que levaria para o cluster chegar a um estado estável. Os clientes também entendiam que poderiam acabar executando com uma confiabilidade geral menor enquanto estivessem restritos.


## <a name="configuring-the-throttles"></a>Configurando os limitadores

O Service Fabric tem dois mecanismos para restringir o número de movimentações de réplica. O mecanismo padrão que existia antes do Service Fabric 5.7 representava a restrição como um número absoluto de movimentações permitidas. Isso não funciona para clusters de todos os tamanhos. Particularmente, em clusters grandes, o valor padrão pode ser muito pequeno, o que reduz significativamente o balanceamento mesmo quando ele é necessário, não tendo qualquer efeito em clusters menores. Esse mecanismo anterior foi substituído pela restrição baseada em porcentagem, que é melhor escalada com clusters dinâmicos, nos quais o número de serviços e nós muda regularmente.

As restrições são baseadas em uma porcentagem do número de réplicas nos clusters. As restrições baseadas em porcentagem permitem expressar a regra: "não mova mais de 10% das réplicas no intervalo de 10 minutos", por exemplo.

As definições de configuração para restrição baseada em porcentagem são:

  - GlobalMovementThrottleThresholdPercentage - o número máximo de movimentações permitidas no cluster a qualquer momento, expresso como porcentagem do número total de réplicas no cluster. 0 indica nenhum limite. O valor padrão é 0. Se essa configuração e GlobalMovementThrottleThreshold forem especificadas, será usado o limite mais conservador.
  - GlobalMovementThrottleThresholdPercentageForPlacement - o número máximo de movimentações permitidas durante a fase de posicionamento, expresso como porcentagem do número total de réplicas no cluster. 0 indica nenhum limite. O valor padrão é 0. Se essa configuração e GlobalMovementThrottleThresholdForPlacement forem especificadas, será usado o limite mais conservador.
  - GlobalMovementThrottleThresholdPercentageForBalancing - o número máximo de movimentações permitidas durante a fase de balanceamento, expresso como porcentagem do número total de réplicas no cluster. 0 indica nenhum limite. O valor padrão é 0. Se essa configuração e GlobalMovementThrottleThresholdForBalancing forem especificadas, será usado o limite mais conservador.

Ao especificar a porcentagem de restrição, você deve especificar 5% como 0,05. O intervalo no qual essas restrições são governadas é GlobalMovementThrottleCountingInterval, que é especificado em segundos.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

via ClusterConfig.json para implantações Autônomas ou Template.json para clusters hospedados pelo Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThresholdPercentage",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForPlacement",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForBalancing",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

### <a name="default-count-based-throttles"></a>Restrições baseadas em contagem padrão
Essas informações são fornecidas para o caso em que você tenha clusters mais antigos ou ainda retenha essas configurações em clusters que foram atualizados desde então. Em geral, é recomendável que elas sejam substituídas pelas restrições baseadas em porcentagem acima. Uma vez que a restrição baseada em porcentagem é desabilitada por padrão, essas restrições permanecem como as restrições padrão para um cluster até que elas sejam desabilitadas e substituídas pelas restrições baseadas em porcentagem. 

  - GlobalMovementThrottleThreshold – essa configuração controla o número total de movimentações no cluster ao longo de um período. O tempo é especificado em segundos como GlobalMovementThrottleCountingInterval. O valor padrão para GlobalMovementThrottleThreshold é 1000 e o valor padrão para GlobalMovementThrottleCountingInterval é 600.
  - MovementPerPartitionThrottleThreshold – essa configuração controla o número total de movimentações de qualquer partição de serviço ao longo de um período. O tempo é especificado em segundos como MovementPerPartitionThrottleCountingInterval. O valor padrão para MovementPerPartitionThrottleThreshold é 50 e o valor padrão para MovementPerPartitionThrottleCountingInterval é 600.

A configuração para essas restrições segue o mesmo padrão que a restrição baseada em porcentagem.

## <a name="next-steps"></a>Próximas etapas
- Para descobrir como o Gerenciador de Recursos de Cluster gerencia e balanceia carga no cluster, confira o artigo sobre [como balancear carga](service-fabric-cluster-resource-manager-balancing.md)
- O Cluster Resource Manager tem muitas opções para descrever o cluster. Para saber mais sobre elas, confira este artigo sobre a [descrição de um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
