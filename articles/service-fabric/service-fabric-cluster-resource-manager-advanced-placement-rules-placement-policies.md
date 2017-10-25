---
title: "Resource Manager de Cluster do Service Fabric – políticas de posicionamento | Microsoft Docs"
description: "Visão geral das políticas de posicionamento adicionais e das regras para os Serviços do Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 5c2d19c6-dd40-4c4b-abd3-5c5ec0abed38
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: c240643d2a7ce98ddd7f7871eeef654cced953f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="placement-policies-for-service-fabric-services"></a>Políticas de posicionamento para serviços do Service Fabric
As políticas de posicionamento são regras adicionais que podem ser usadas para administrar o posicionamento de serviço em alguns cenários específicos, menos comuns. Alguns exemplos desses cenários são:

- O cluster do Service Fabric engloba distâncias geográficas, como vários datacenters locais ou regiões do Azure
- O ambiente abrange várias áreas de controle geopolítico ou legal, ou algum outro caso em que você tem limites políticos que precisa impor
- Há considerações de latência ou desempenho de comunicação devido a grandes distâncias ou uso de links de rede mais lentos ou menos confiáveis
- Você precisa manter determinadas cargas de trabalho colocadas como um melhor esforço, seja com outras cargas de trabalho, seja na proximidade dos clientes

A maioria desses requisitos se alinha ao layout físico do cluster, representado como os domínios de falha do cluster. 

As políticas de posicionamento avançado que ajudam a resolver esses cenários são:

1. Domínios inválidos
2. Domínios necessários
3. Domínios preferenciais
4. Desativação de empacotamento de réplica

A maioria dos controles a seguir pode ser configurada por meio das propriedades de nó e de restrições de posicionamento, mas algumas são mais complicadas. Para simplificar, o Cluster Resource Manager do Service Fabric fornece essas políticas de posicionamento adicionais. As políticas de posicionamento são configuradas de acordo com uma instância de serviço nomeada. Elas também podem ser atualizadas dinamicamente.

## <a name="specifying-invalid-domains"></a>Especificando domínios inválidos
A política de posicionamento **InvalidDomain** permite especificar que um determinado Domínio de Falha é inválido para um serviço específico. Essa política garante que um serviço específico nunca seja executado em uma determinada área, por exemplo, por motivos de política corporativa ou geopolíticos. Vários domínios inválidos podem ser especificados através de diferentes políticas.

<center>
![Exemplo de domínio inválido][Image1]
</center>

Código:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Especificando domínios necessários
A política de posicionamento de domínio necessário requer que o serviço esteja presente somente no domínio especificado. Vários domínios necessários podem ser especificados através de diferentes políticas.

<center>
![Exemplo de domínio necessário][Image2]
</center>

Código:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas-of-a-stateful-service"></a>Especificando um domínio preferido para as réplicas primárias de um serviço com estado
O Domínio Primário Preferencial especifica o domínio de falha no qual colocar o primário. O primário é encerrado nesse domínio quando tudo está íntegro. Se o domínio ou a réplica primária falhar ou desligar, o primário move-se para algum outro local. De modo ideal, no mesmo domínio. Se essa nova localização não estiver no domínio preferencial, o Cluster Resource Manager vai movê-lo de volta ao domínio preferencial assim que possível. Obviamente, essa configuração só faz sentido para serviços com estado. Essa política é mais útil em clusters que estão distribuídos em regiões do Azure ou em vários datacenters, mas têm serviços que preferem o posicionamento em um determinado local. Manter os primários próximos aos seus usuários ou outros serviços ajudam a fornecer latência menor, especialmente para leituras, que são tratadas pelos primários por padrão.

<center>
![Domínios primários preferenciais e failover][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Exigindo a distribuição de réplica e desabilitando o empacotamento
As réplicas _normalmente_ são distribuídas entre domínios de falha e upgrade quando o cluster está íntegro. No entanto, há casos em que mais de uma réplica para uma determinada partição podem acabar temporariamente empacotadas em uma único domínio. Por exemplo, digamos que o cluster tenha nove nós em três domínios de falha, fd:/0, fd:/1 e fd:/2. Digamos também que o serviço tem três réplicas. Digamos que os nós que estavam sendo usados para as réplicas em fd:/1 e fd:/2 foram desativados. Normalmente, o Gerenciador de Recursos de Cluster prefere outros nós nos mesmos domínios de falha. Nesse caso, digamos que devido a problemas de capacidade nenhum dos outros nós nesses domínios era válido. Se o Cluster Resource Manager criasse substituições para essas réplicas, ele precisaria escolher nós em fd:/0. No entanto, fazer _isso_ cria uma situação em que a restrição de Domínio de Falha é violada. Empacotar réplicas aumenta as chances de que o conjunto inteiro de réplicas possa ser desativado ou roubado. 

> [!NOTE]
> Para obter mais informações sobre restrições e prioridades de restrições em geral, confira [este tópico](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).
>

Se você já viu uma mensagem de integridade, como "`The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`", você atingiu essa condição ou algo parecido. Normalmente, apenas uma ou duas réplicas são empacotadas juntas temporariamente. Enquanto houver menos de um quorum de réplicas em um determinado domínio, você estará seguro. O empacotamento é raro, mas pode acontecer e, geralmente, essas situações são transitórias, uma vez que os nós voltam. Se os nós permanecem inativos e o Cluster Resource Manager precisa criar substituições, normalmente haverá outros nós disponíveis nos domínios de falha ideais.

Algumas cargas de trabalho sempre preferirão ter o número de destino de réplicas, mesmo se elas forem empacotadas em menos domínios. Essas cargas de trabalho apostam contra falhas de domínio permanentes simultâneas totais e normalmente podem recuperar o estado local. Outras cargas de trabalho preferem passar logo pelo tempo de inatividade do que arriscar a correção ou a perda de dados. A maioria das cargas de trabalho de produção é executada com mais de três réplicas, mais de três domínios de falha e muitos nós válidos por domínio de falha. Por isso, o comportamento padrão permite o empacotamento de domínio por padrão. O comportamento padrão permite balanceamento normal e failover para tratar desses casos extremos, mesmo que isso signifique empacotamento de domínio temporário.

Se desejar desabilitar esse empacotamento para uma determinada carga de trabalho, você poderá especificar a política `RequireDomainDistribution` no serviço. Quando essa política é definida, o Gerenciador de Recursos de Cluster garante que duas réplicas da mesma partição não seja executadas no mesmo domínio de falha ou upgrade.

Código:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Agora, seria possível usar essas configurações para serviços em um cluster que não tenha sido distribuído geograficamente? Seria, mas também não há um bom motivo para isso. As configurações de domínio obrigatórias, inválidas e preferenciais devem ser evitadas, a menos que os cenários as exijam. Não faz sentido tentar forçar uma determinada carga de trabalho a ser executada em um único rack ou preferir algum segmento do seu cluster local em vez de outro. Diferentes configurações de hardware devem ser distribuídas entre domínios de falha e manipuladas por propriedades de nó e restrições de posicionamento normais.

## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre como configurar os serviços, [Saiba mais sobre como configurar serviços](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
