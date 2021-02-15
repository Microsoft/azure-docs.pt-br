---
title: 'Gerenciador de recursos de Cluster Service Fabric: custo de movimento'
description: Saiba mais sobre o custo de movimento para serviços de Service Fabric e como ele pode ser especificado para se adequar a qualquer necessidade de arquitetura, incluindo configuração dinâmica.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 0fdcfb02851d56ed996ae4bf32671ab545782733
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89005336"
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

Os snippets de código anteriores são todos voltados a especificar MoveCost para um serviço inteiro de uma vez, de fora do próprio serviço. No entanto, o custo de movimentação é mais útil quando muda ao longo do tempo de vida de um objeto de serviço específico. Como os próprios serviços provavelmente sabem melhor qual é o custo de sua movimentação em um determinado momento, há uma API para os serviços relatarem seu próprio custo de movimentação individual durante o runtime. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Impacto do custo de movimentação
MoveCost tem cinco níveis: zero, baixo, médio, alto e VeryHigh. As seguintes regras se aplicam:

* MoveCosts são relativos entre si, exceto para zero e VeryHigh. 
* O custo de movimentação Zero significa que a movimentação é gratuita e não deve contar na pontuação da solução.
* Definir o custo de movimentação como alto ou VeryHigh *não fornece uma* garantia de que a réplica *nunca* será movida.
* As réplicas com o custo de movimentação de VeryHigh serão movidas somente se houver uma violação de restrição no cluster que não possa ser corrigida de nenhuma outra maneira (mesmo que seja necessário mover muitas outras réplicas para corrigir a violação)



<center>

![O custo de movimentação como um fator na seleção de réplicas para movimentação][Image1]
</center>

O MoveCost ajuda a encontrar as soluções que causam, em geral, o mínimo de interrupções e que sejam mais fáceis de conseguir enquanto ainda alcançam o equilíbrio equivalente. A noção de custo de um serviço pode ser relativa a muitas coisas. Os fatores mais comuns ao calcular o custo do movimento são:

- a quantidade de estados ou de dados que o serviço deve mover.
- o custo de desconexão de clientes. O custo de movimentar uma réplica primária normalmente é mais alto do que o custo de movimentar uma réplica secundária.
- o custo de interromper uma operação em andamento. Algumas operações no nível do armazenamento de dados ou operações realizadas em resposta a uma chamada de cliente são caras. Depois de um certo ponto, você não quer interrompê-las a não ser que seja necessário. Assim, enquanto a operação estiver em andamento, você aumenta o custo de movimentação desse objeto de serviço para reduzir a probabilidade de que ele se mova. Quando a operação estiver concluída, defina o custo de volta para normal.

> [!IMPORTANT]
> Usar o custo de movimentação do VeryHigh deve ser considerado cuidadosamente, pois ele restringe significativamente a capacidade do Gerenciador de recursos de cluster de encontrar uma solução de posicionamento globalmente ideal no cluster. As réplicas com o custo de movimentação de VeryHigh serão movidas somente se houver uma violação de restrição no cluster que não possa ser corrigida de nenhuma outra maneira (mesmo que seja necessário mover muitas outras réplicas para corrigir a violação)

## <a name="enabling-move-cost-in-your-cluster"></a>Habilitando o custo de movimentação em seu cluster
Para que os MoveCosts mais granulares sejam levados em conta, MoveCost deve estar habilitado em seu cluster. Sem essa configuração, o modo padrão de contar movimentações é usado para calcular MoveCost e relatórios de MoveCost são ignorados.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

via ClusterConfig.json para implantações Autônomas ou Template.json para clusters hospedados pelo Azure:

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
