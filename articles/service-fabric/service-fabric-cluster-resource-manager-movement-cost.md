---
title: 'Gerenciador de Recursos de Cluster do Service Fabric: custo de movimento | Microsoft Docs'
description: "Visão geral do custo dos movimentos de serviços do Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: f022f258-7bc0-4db4-aa85-8c6c8344da32
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 3063647671fea94da3ce635b887f6f0f7de89f70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-movement-cost"></a>Custo do movimentação de serviços
Um fator que o Gerenciador de Recursos de Cluster do Service Fabric considera ao tentar determinar quais alterações fazer em um cluster é o custo dessas alterações. A noção de "custo" é avaliada considerando quanto o cluster pode ser melhorado. O custo é considerado ao mover serviços para balanceamento, desfragmentação e outros requisitos. A meta é atender aos requisitos da maneira menos perturbadora ou cara. 

Movimentar serviços custa, no mínimo, tempo de CPU e largura de banda de rede. Para serviços com estado, é necessário copiar o estado desses serviços, consumindo mais disco e memória. Minimizar o custo das soluções que o Gerenciador de Recursos de Cluster do Azure Service Fabric oferece ajuda a garantir que recursos do cluster não sejam gastos desnecessariamente. No entanto, não seria bom ignorar soluções que melhorariam significativamente a alocação de recursos no cluster.

O Gerenciador de Recursos de Cluster tem duas maneiras de calcular custos e limitá-los, enquanto tenta gerenciar o cluster. O primeiro mecanismo é simplesmente contar cada movimentação que seria feita. Se duas soluções forem geradas com quase o mesmo saldo (pontuação), o Gerenciador de Recursos de Cluster preferirá a que tem o custo mais baixo (número total de movimentações).

Esta estratégia funciona bem. Mas, como ocorre com cargas estáticas ou padrão, é improvável em qualquer sistema complexo que todas as mudanças sejam iguais. É provável que algumas delas sejam muito mais caras.

## <a name="setting-move-costs"></a>Definindo custos de movimentação 
Você pode especificar o custo de movimentação padrão para um serviço quando ele é criado:

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -DefaultMoveCost Medium
```

C#: 

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
serviceDescription.DefaultMoveCost = MoveCost.Medium;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Você também pode especificar ou atualizar MoveCost dinamicamente para um serviço após ele ter sido criado: 

PowerShell: 

```posh
Update-ServiceFabricService -Stateful -ServiceName "fabric:/AppName/ServiceName" -DefaultMoveCost High
```

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.DefaultMoveCost = MoveCost.High;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), updateDescription);
```

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Especificando dinamicamente o custo de movimentação por réplica

Os trechos de código anteriores são todos voltados a especificar MoveCost para um serviço inteiro de uma vez, de fora do próprio serviço. No entanto, o custo de movimentação é mais útil quando muda ao longo do tempo de vida de um objeto de serviço específico. Como os próprios serviços provavelmente sabem melhor qual é o custo de sua movimentação em um determinado momento, há uma API para os serviços relatarem seu próprio custo de movimentação individual durante o tempo de execução. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Impacto do custo de movimentação
O MoveCost tem quatro níveis: Zero, Baixo, Médio e Alto. Os Custos de Movimentação têm relação uns com os outros, exceto Zero. O custo de movimentação Zero significa que a movimentação é gratuita e não deve contar na pontuação da solução. Configurar o custo de movimentação para Alto *não* garante que a réplica permaneça em um só lugar.

<center>
![O custo de movimentação como um fator na seleção de réplicas para movimentação][Image1]
</center>

O MoveCost ajuda a encontrar as soluções que causam, em geral, o mínimo de interrupções e que sejam mais fáceis de conseguir enquanto ainda alcançam o equilíbrio equivalente. A noção de custo de um serviço pode ser relativa a muitas coisas. Os fatores mais comuns ao calcular o custo do movimento são:

- a quantidade de estados ou de dados que o serviço deve mover.
- o custo de desconexão de clientes. O custo de movimentar uma réplica primária normalmente é mais alto do que o custo de movimentar uma réplica secundária.
- o custo de interromper uma operação em andamento. Algumas operações no nível do armazenamento de dados ou operações realizadas em resposta a uma chamada de cliente são caras. Depois de um certo ponto, você não quer interrompê-las a não ser que seja necessário. Assim, enquanto a operação estiver em andamento, você aumenta o custo de movimentação desse objeto de serviço para reduzir a probabilidade de que ele se mova. Quando a operação estiver concluída, defina o custo de volta para normal.

## <a name="enabling-move-cost-in-your-cluster"></a>Habilitando o custo de movimentação em seu cluster
Para que os MoveCosts mais granulares sejam levados em conta, MoveCost deve estar habilitado em seu cluster. Sem essa configuração, o modo padrão de contar movimentações é usado para calcular MoveCost e relatórios de MoveCost são ignorados.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

via ClusterConfig.json para implantações autônomas ou Template.json para clusters hospedados pelo Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "UseMoveCostReports",
          "value": "true"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Próximas etapas
- O Gerenciador de Recursos de Cluster do Service Fabric usa métricas para gerenciar o consumo e a capacidade no cluster. Para saber mais sobre as métricas e como configurá-las, confira [Gerenciando o consumo e a carga de recursos no Service Fabric com métricas](service-fabric-cluster-resource-manager-metrics.md).
- Para saber como o Gerenciador de Recursos de Cluster gerencia e balanceia carga no cluster, confira [Balanceamento do cluster do Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
