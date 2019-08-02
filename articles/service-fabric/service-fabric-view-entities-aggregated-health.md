---
title: Como exibir a integridade agregada das entidades do Azure Service Fabric | Microsoft Docs
description: Descreve como consultar, exibir e avaliar a integridade agregada das entidades do Azure Service Fabric, por meio de consultas de integridade e consultas gerais.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: chackdan
editor: ''
ms.assetid: fa34c52d-3a74-4b90-b045-ad67afa43fe5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: 1721f10f8950577080a89ba58a3eb4dd3a25c188
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249179"
---
# <a name="view-service-fabric-health-reports"></a>Como exibir relatórios de integridade do Service Fabric
O Azure Service Fabric apresenta um [modelo de integridade](service-fabric-health-introduction.md) com entidades de integridade nas quais os componentes do sistema e watchdogs podem relatar condições locais que estão monitorando. O [repositório de integridade](service-fabric-health-introduction.md#health-store) agrega todos os dados de integridade para determinar se as entidades estão íntegras.

O cluster é populado automaticamente com relatórios de integridade enviados pelos componentes do sistema. Leia mais em [Usar relatórios de integridade do sistema para solução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

O Service Fabric fornece várias maneiras de obter a integridade agregada de entidades:

* [Gerenciador de Malha do Serviço](service-fabric-visualizing-your-cluster.md) ou outras ferramentas de visualização
* Consultas de integridade (por meio do PowerShell, da API ou de REST)
* Consultas gerais que retornam uma lista de entidades que têm a integridade como uma das propriedades (por meio do PowerShell, da API ou de REST)

Para demonstrar essas opções, vamos usar um cluster local com cinco nós e o [aplicativo fabric:/WordCount](https://aka.ms/servicefabric-wordcountapp). O aplicativo **fabric:/WordCount** contém dois serviços de padrão, um serviço com estado do tipo `WordCountServiceType`e um serviço sem estado do tipo `WordCountWebServiceType`. Alterei o `ApplicationManifest.xml` para exigir sete réplicas de destino para o serviço com estado e uma partição. Como há apenas cinco nós no cluster, os componentes do sistema relatam um aviso na partição de serviço, pois ele está abaixo da contagem exigida.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Integridade no Gerenciador da Malha do Serviço
O Gerenciador da Malha do Serviço fornece uma exibição visual do cluster. Na imagem abaixo, você pode ver que:

* O aplicativo **fabric:/WordCount** está vermelho (em erro) porque ele tem um evento de erro relatado por **MyWatchdog** para a propriedade **Disponibilidade**.
* Um de seus serviços, **fabric:/WordCount/WordCountService** está amarelo (em aviso). O serviço está configurado com sete réplicas e o cluster tem cinco nós, ou seja, duas réplicas não têm lugar. Embora não seja mostrado aqui, a partição de serviço está amarela porque o relatório do sistema `System.FM` indica `Partition is below target replica or instance count`. A partição amarela dispara o serviço amarelo.
* O cluster está vermelho devido ao aplicativo vermelho.

A avaliação usa políticas padrão do manifesto do cluster e do manifesto do aplicativo. Elas são políticas rígidas e não toleram falhas.

Visualização do cluster com o Gerenciador do Service Fabric:

![Visualização do cluster com o Gerenciador do Service Fabric.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> Leia mais sobre o [Gerenciador da Malha do Serviço](service-fabric-visualizing-your-cluster.md).
>
>

## <a name="health-queries"></a>Consultas de integridade
A Malha do Serviço expõe consultas de integridade para cada um dos [tipos de entidade](service-fabric-health-introduction.md#health-entities-and-hierarchy)com suporte. Elas podem ser acessados pela API usando métodos de [FabricClient.HealthManager](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), por cmdlets do PowerShell e por REST. Essas consultas retornam informações de integridade completas sobre a entidade: o estado de integridade agregada, eventos de integridade da entidade, estados de integridade dos filhos (quando aplicável), avaliações não íntegras (quando a entidade não está íntegra) e estatísticas sobre a integridade dos filhos (quando aplicável).

> [!NOTE]
> Uma entidade de integridade retorna para o usuário quando ela é totalmente populada no repositório de integridade. A entidade deve estar ativa (não excluída) e ter um relatório do sistema. Suas entidades pai na cadeia hierárquica também devem ter relatórios do sistema. Se alguma dessas condições não for atendida, as consultas de integridade retornam um [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) com [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) `FabricHealthEntityNotFound` que mostra por que a entidade não é retornada.
>
>

As consultas de integridade devem passar pelo identificador da entidade, que depende do tipo de entidade. As consultas aceitam parâmetros opcionais da política de integridade. Se nenhuma política de integridade for especificada, as [políticas de integridade](service-fabric-health-introduction.md#health-policies) do manifesto do cluster ou do aplicativo serão usadas para avaliação. Se os manifestos não contêm uma definição para políticas de integridade, as políticas de integridade padrão são usadas para avaliação. As políticas de integridade padrão não toleram falhas. As consultas também aceitam filtros para retornar apenas eventos ou filhos parciais; aqueles que respeitarem os filtros especificados. Outro filtro permite a exclusão das estatísticas de filhos.

> [!NOTE]
> Os filtros de saída são aplicados no lado do servidor, de modo que o tamanho da resposta da mensagem é reduzido. Recomendamos o uso dos filtros de saída para limitar os dados retornados, em vez de aplicar filtros no lado do cliente.
>
>

A integridade da entidade contém:

* O estado da integridade agregada da entidade. Ele é calculado pelo repositório de integridade com base nos relatórios de integridade da entidade, pelos estados da integridade dos filhos (quando aplicável) e por políticas de integridade. Leia mais sobre [Avaliação de integridade da entidade](service-fabric-health-introduction.md#health-evaluation).  
* Os eventos de integridade da entidade.
* A coleção de estados de integridade de todos os filhos das entidades que podem ter filhos. Os estados da integridade contêm identificadores de entidade e o estado da integridade agregada. Para obter toda a integridade de um filho, chame a integridade de consulta do tipo de entidade filho e passe o identificador do filho.
* As avaliações não íntegras que apontam para o relatório que disparou o estado da entidade, se a entidade não estiver íntegra. As avaliações são recursivas, contendo as avaliações de integridade de filhos que dispararam o estado de integridade atual. Por exemplo, um watchdog relatou um erro em uma réplica. A integridade do aplicativo mostra uma avaliação não íntegra devido a um serviço não íntegro; o serviço não está íntegro devido a uma partição com erro; a partição não está íntegra devido a uma réplica com erro; a réplica não está íntegra devido ao relatório de integridade do watchdog com erro.
* As estatísticas de integridade para todos os tipos de filho de entidades que têm filhos. Por exemplo, a integridade do cluster mostra o número total de aplicativos, serviços, partições, réplicas e entidades implantadas no cluster. A integridade do serviço mostra o número total de partições e réplicas no serviço especificado.

## <a name="get-cluster-health"></a>Obter integridade do cluster
Retorna a integridade da entidade do cluster e contém os estados da integridade de aplicativos e nós (filhos do cluster). Entrada:

* [Opcional] A política de integridade do cluster usada para avaliar os nós e os eventos de cluster.
* [Opcional] Mapa da política de integridade do aplicativo, com políticas de integridade usadas para substituir as políticas do manifesto do aplicativo.
* [Opcional] Filtros de eventos, nós e aplicativos que especificam quais entradas são interessantes e devem retornar nos resultados (por exemplo, apenas erros ou avisos e erros). Todos os eventos, nós e aplicativos são usados para avaliar a integridade agregada da entidade, independentemente do filtro.
* [Opcional] Filtrar para excluir as estatísticas de integridade.
* [Opcional] Filtrar para incluir as estatísticas de integridade do fabric:/System nas estatísticas de integridade. Aplicável somente quando as estatísticas de integridade não são excluídas. Por padrão, as estatísticas de integridade incluem somente as estatísticas de aplicativos de usuário e não o aplicativo System.

### <a name="api"></a>API
Para obter a integridade do cluster, crie um `FabricClient` e chame o método [GetClusterHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) em seu **HealthManager**.

A chamada a seguir obtém a integridade do cluster:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

O código a seguir obtém a integridade do cluster usando política de integridade de cluster personalizada para nós e aplicativos. Especifica que as estatísticas de integridade incluem as estatísticas de fabric:/System. Ele cria [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthquerydescription), que contém as informações de entrada.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var healthStatisticsFilter = new ClusterHealthStatisticsFilter()
{
    ExcludeHealthStatistics = false,
    IncludeSystemApplicationHealthStatistics = true
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
    HealthStatisticsFilter = healthStatisticsFilter
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade do cluster é [Get-ServiceFabricClusterHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealth). Primeiro, conecte-se ao cluster usando o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .

Estado do cluster é de cinco nós, o aplicativo do sistema e fabric:/WordCount configurados conforme descrito.

O cmdlet a seguir obtém a integridade do cluster usando políticas de integridade padrão. O estado de integridade agregada é de aviso, pois o aplicativo fabric:/WordCount está em aviso. Observe como as avaliações não íntegras mostram com detalhes a condição que disparou a integridade agregada.

```xml
PS D:\ServiceFabric> Get-ServiceFabricClusterHealth


AggregatedHealthState   : Warning
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.


NodeHealthStates        : 
                          NodeName              : _Node_4
                          AggregatedHealthState : Ok

                          NodeName              : _Node_3
                          AggregatedHealthState : Ok

                          NodeName              : _Node_2
                          AggregatedHealthState : Ok

                          NodeName              : _Node_1
                          AggregatedHealthState : Ok

                          NodeName              : _Node_0
                          AggregatedHealthState : Ok

ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
HealthStatistics        : 
                          Node                  : 5 Ok, 0 Warning, 0 Error
                          Replica               : 6 Ok, 0 Warning, 0 Error
                          Partition             : 1 Ok, 1 Warning, 0 Error
                          Service               : 1 Ok, 1 Warning, 0 Error
                          DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                          DeployedApplication   : 5 Ok, 0 Warning, 0 Error
                          Application           : 0 Ok, 1 Warning, 0 Error
```

O cmdlet do PowerShell a seguir obtém a integridade do cluster usando uma política de aplicativo personalizada. Ele filtra os resultados para obter apenas os aplicativos e nós com erro ou aviso. Como resultado nenhum nó é retornado, pois todos estão íntegros. Somente o aplicativo fabric:/WordCount respeita o filtro de aplicativos. Como a política personalizada especifica a consideração de avisos como erros para o aplicativo fabric:/WordCount, o aplicativo é avaliado como em estado de erro, assim como o cluster.

```powershell
PS D:\ServiceFabric> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics


AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None
```

### <a name="rest"></a>REST
Você pode obter a integridade do cluster com uma [solicitação GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster) ou uma [solicitação POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy), que inclui as políticas de integridade descritas no corpo.

## <a name="get-node-health"></a>Obter integridade do nó
Retorna a integridade de uma entidade de nó e contém os eventos de integridade reportados no nó. Entrada:

* [Obrigatório] O nome do nó que identifica o nó.
* [Opcional] As configurações da política de integridade do cluster usadas para avaliar a integridade.
* [Opcional] Filtros de eventos que especificam quais entradas são interessantes e devem retornar nos resultados (por exemplo, apenas erros ou avisos e erros). Todos os eventos são usados para avaliar a integridade agregada da entidade, independentemente do filtro.

### <a name="api"></a>API
Para obter a integridade do nó por meio da API, crie um `FabricClient` e chame o método [GetNodeHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) em seu HealthManager.

O código a seguir obtém a integridade de nó para o nome de nó especificado:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

O código a seguir obtém a integridade de nó para o nome do nó especificado e passa um filtro de eventos e política personalizada por meio de [NodeHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.nodehealthquerydescription):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade do nó é [Get-ServiceFabricNodeHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnodehealth). Primeiro, conecte-se ao cluster usando o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .
O cmdlet a seguir obtém a integridade do nó usando políticas de integridade padrão:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 7/13/2017 4:39:23 PM
                        ReceivedAt            : 7/13/2017 4:40:47 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 4:40:47 PM, LastWarning = 1/1/0001 12:00:00 AM
```

O cmdlet a seguir obtém a integridade de todos os nós no cluster.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_4                     Ok
_Node_3                     Ok
_Node_2                     Ok
_Node_1                     Ok
_Node_0                     Ok
```

### <a name="rest"></a>REST
Você pode obter a integridade do nó com uma [solicitação GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node) ou uma [solicitação POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy), que inclui as políticas de integridade descritas no corpo.

## <a name="get-application-health"></a>Obter integridade do aplicativo
Retorna a integridade de uma entidade de aplicativo. Contém os estados de integridade do aplicativo implantado e filhos de serviço. Entrada:

* [Obrigatório] O nome do aplicativo (URI) que identifica o aplicativo.
* [Opcional] A política de integridade do aplicativo usada para substituir as políticas do manifesto do aplicativo.
* [Opcional] Filtros de eventos, serviços e aplicativos implantados que especificam quais entradas são interessantes e devem retornar nos resultados (por exemplo, apenas erros ou avisos e erros). Todos os eventos, serviços e aplicativos implantados são usados para avaliar a integridade agregada da entidade, independentemente do filtro.
* [Opcional] Filtrar para excluir as estatísticas de integridade. Se não for especificado, as estatísticas de integridade incluem a contagem de OK, avisos e erros para todos os filhos do aplicativo: serviços, partições, réplicas, aplicativos implantados e pacotes de serviço implantados.

### <a name="api"></a>API
Para obter a integridade do aplicativo, crie um `FabricClient` e chame o método [GetApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) em seu HealthManager.

O código a seguir obtém a integridade do aplicativo para o nome do aplicativo especificado (URI):

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

O código a seguir obtém a integridade do aplicativo para o nome do aplicativo especificado (URI), com filtros e políticas personalizadas especificadas por meio de [ApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.applicationhealthquerydescription).

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade do aplicativos é [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps). Primeiro, conecte-se ao cluster usando o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .

O cmdlet a seguir retorna a integridade do aplicativo **fabric:/WordCount** :

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning

DeployedApplicationHealthStates : 
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/13/2017 5:57:05 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics                : 
                                  Replica               : 6 Ok, 0 Warning, 0 Error
                                  Partition             : 1 Ok, 1 Warning, 0 Error
                                  Service               : 1 Ok, 1 Warning, 0 Error
                                  DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                                  DeployedApplication   : 5 Ok, 0 Warning, 0 Error
```

O cmdlet do PowerShell a seguir passa políticas personalizadas. Ele também filtra filhos e eventos.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.

ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>REST
Você pode obter a integridade do aplicativo com uma [solicitação GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application) ou uma [solicitação POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy), que inclui as políticas de integridade descritas no corpo.

## <a name="get-service-health"></a>Obter integridade do serviço
Retorna a integridade de uma entidade de serviço. Além disso, contém os estados de integridade da partição. Entrada:

* [Obrigatório] O nome de serviço (URI) que identifica o serviço.
* [Opcional] A política de integridade do aplicativo usada para substituir a política do manifesto do aplicativo.
* [Opcional] Filtros de eventos e partições que especificam quais entradas são interessantes e devem retornar nos resultados (por exemplo, apenas erros ou avisos e erros). Todos os eventos e partições são usados para avaliar a integridade agregada da entidade, independentemente do filtro.
* [Opcional] Filtrar para excluir as estatísticas de integridade. Se não for especificado, as estatísticas de integridade mostram a contagem de OK, avisos e erros de todas as partições e réplicas do serviço.

### <a name="api"></a>API
Para obter a integridade do serviço por meio de API, crie um `FabricClient` e chame o método [GetServiceHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) em seu HealthManager.

O exemplo a seguir obtém a integridade de um serviço com o nome do serviço especificado (URI):

```csharp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

O código a seguir obtém a integridade do serviço para o nome do serviço especificado (URI), especificando filtros e política personalizada por meio de [ServiceHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicehealthquerydescription):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade do serviço é [Get-ServiceFabricServiceHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicehealth). Primeiro, conecte-se ao cluster usando o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .

O cmdlet a seguir obtém a integridade do serviço usando políticas de integridade padrão:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                        Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                            Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

PartitionHealthStates : 
                        PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                        AggregatedHealthState : Warning

HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 15
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
                        Partition             : 0 Ok, 1 Warning, 0 Error
```

### <a name="rest"></a>REST
Você pode obter a integridade do serviço com uma [solicitação GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service) ou uma [solicitação POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy), que inclui as políticas de integridade descritas no corpo.

## <a name="get-partition-health"></a>Obter integridade da partição
Retorna a integridade de uma entidade de partição. Além disso, contém os estados de integridade da réplica. Entrada:

* [Obrigatório] A ID de partição (GUID) que identifica a partição.
* [Opcional] A política de integridade do aplicativo usada para substituir a política do manifesto do aplicativo.
* [Opcional] Filtros para eventos e réplicas que especificam quais entradas são interessantes e devem retornar nos resultados (por exemplo, apenas erros ou avisos e erros). Todos os eventos e réplicas são usados para avaliar a integridade agregada da entidade, independentemente do filtro.
* [Opcional] Filtrar para excluir as estatísticas de integridade. Se não for especificado, as estatísticas de integridade mostram quantas réplicas estão nos estados OK, aviso e erro.

### <a name="api"></a>API
Para obter a integridade da partição por meio de API, crie um `FabricClient` e chame o método [GetPartitionHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) em seu HealthManager. Para especificar parâmetros opcionais, crie [PartitionHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.partitionhealthquerydescription).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade da partição é [Get-ServiceFabricPartitionHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricpartitionhealth). Primeiro, conecte-se ao cluster usando o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .

O cmdlet a seguir obtém a integridade de todas as partições do serviço **fabric:/WordCount/WordCountService** e filtra excluindo os estados de integridade replicados:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 72
                        SentAt                : 7/13/2017 5:57:29 PM
                        ReceivedAt            : 7/13/2017 5:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/P RD _Node_2 Up 131444422260002646
                          N/S RD _Node_4 Up 131444422293113678
                          N/S RD _Node_3 Up 131444422293113679
                          N/S RD _Node_1 Up 131444422293118720
                          N/S RD _Node_0 Up 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5.)

                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 7/13/2017 5:57:48 PM, LastError = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131444445174851664
                        SentAt                : 7/13/2017 6:35:17 PM
                        ReceivedAt            : 7/13/2017 6:35:18 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A

                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None

                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/13/2017 5:57:48 PM, LastOk = 1/1/0001 12:00:00 AM

HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Você pode obter a integridade da partição com uma [solicitação GET](/rest/api/servicefabric/sfclient-api-getpartitionhealth) ou uma [solicitação POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy), que inclui as políticas de integridade descritas no corpo.

## <a name="get-replica-health"></a>Obter integridade da réplica
Retorna a integridade de uma réplica de serviço com estado ou uma instância de serviço sem estado. Entrada:

* [Obrigatório] A ID da partição (GUID) e a ID da réplica que identifica a réplica.
* [Opcional] Os parâmetros da política de integridade do aplicativo usados para substituir as políticas de manifesto do aplicativo.
* [Opcional] Filtros de eventos que especificam quais entradas são interessantes e devem retornar nos resultados (por exemplo, apenas erros ou avisos e erros). Todos os eventos são usados para avaliar a integridade agregada da entidade, independentemente do filtro.

### <a name="api"></a>API
Para obter a integridade da réplica por meio de API, crie um `FabricClient` e chame o método [GetReplicaHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) em seu HealthManager. Para especificar parâmetros avançados, use [ReplicaHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.replicahealthquerydescription).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade da réplica é [Get-ServiceFabricReplicaHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricreplicahealth). Primeiro, conecte-se ao cluster usando o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .

O cmdlet a seguir obtém a integridade da réplica primária para todas as partições do serviço.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Você pode obter a integridade da réplica com uma [solicitação GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica) ou uma [solicitação POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy), que inclui as políticas de integridade descritas no corpo.

## <a name="get-deployed-application-health"></a>Obter integridade do aplicativo implantado
Retorna a integridade de um aplicativo implantado em uma entidade de nó. Além disso, contém os estados de integridade do pacote de serviço implantado. Entrada:

* [Obrigatório] O nome do aplicativo (URI) e o nome do nó (cadeia de caracteres) que identificam o aplicativo implantado
* [Opcional] A política de integridade do aplicativo usada para substituir as políticas do manifesto do aplicativo.
* [Opcional] Filtros de eventos e pacotes de serviço implantados que especificam quais entradas são interessantes e devem retornar nos resultados (por exemplo, apenas erros ou avisos e erros). Todos os eventos e pacotes de serviço implantados são usados para avaliar a integridade agregada da entidade, independentemente do filtro.
* [Opcional] Filtrar para excluir as estatísticas de integridade. Se não for especificado, as estatísticas de integridade mostram a quantidade de pacotes de serviço implantados nos estados de integridade OK, aviso e erro.

### <a name="api"></a>API
Para obter a integridade de um aplicativo implantado em um nó por meio de API, crie um `FabricClient` e chame o método [GetDeployedApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) em seu HealthManager. Para especificar parâmetros opcionais, use [DeployedApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade do aplicativo implantado é [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps). Primeiro, conecte-se ao cluster usando o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) . Para descobrir onde um aplicativo está implantado, execute [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) e observe os filhos do aplicativo implantado.

O cmdlet a seguir obtém a integridade do aplicativo **fabric:/WordCount** implantado em **_Node_2**.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_0


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_0
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCountWebServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok

HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131444422261848308
                                     SentAt                : 7/13/2017 5:57:06 PM
                                     ReceivedAt            : 7/13/2017 5:57:17 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/13/2017 5:57:17 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics                   : 
                                     DeployedServicePackage : 2 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Você pode obter a integridade do aplicativo implantado com uma [solicitação GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application) ou uma [solicitação POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy), que inclui as políticas de integridade descritas no corpo.

## <a name="get-deployed-service-package-health"></a>Obter integridade do pacote de serviço implantado
Retorna a integridade de uma entidade de pacote de serviço implantada. Entrada:

* [Obrigatório] O nome do aplicativo (URI), nome do nó (cadeia de caracteres) e nome do manifesto do serviço (cadeia de caracteres) que identificam o pacote de serviço implantado
* [Opcional] A política de integridade do aplicativo usada para substituir a política do manifesto do aplicativo.
* [Opcional] Filtros de eventos que especificam quais entradas são interessantes e devem retornar nos resultados (por exemplo, apenas erros ou avisos e erros). Todos os eventos são usados para avaliar a integridade agregada da entidade, independentemente do filtro.

### <a name="api"></a>API
Para obter a integridade de um pacote de serviço implantado por meio de API, crie um `FabricClient` e chame o método [GetDeployedServicePackageHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) em seu HealthManager. Para especificar parâmetros opcionais, use [DeployedServicePackageHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade do pacote de serviço implantado é [Get-ServiceFabricDeployedServicePackageHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth). Primeiro, conecte-se ao cluster usando o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) . Para descobrir onde um aplicativo está implantado, execute [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) e observe os aplicativos implantados. Para ver quais pacotes de serviço estão em um aplicativo, observe os filhos do pacote de serviço implantado na saída de [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps) .

O cmdlet a seguir obtém a integridade do pacote de serviço **WordCountServicePkg** do aplicativo **fabric:/WordCount** implantado em **_Node_2**. A entidade tem relatórios **System.Hosting** para ativação bem-sucedida do pacote de serviço e do ponto de entrada e registro bem-sucedido do tipo de serviço.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_2
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131444422267693359
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131444422267903345
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131444422272458374
                             SentAt                : 7/13/2017 5:57:07 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Você pode obter a integridade do pacote de serviço implantado com uma [solicitação GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package) ou uma [solicitação POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy), que inclui as políticas de integridade descritas no corpo.

## <a name="health-chunk-queries"></a>Consultas de integridade em blocos
As consultas de integridade em bloco podem retornar os filhos de cluster em vários níveis (recursivamente), de acordo com os filtros de entrada. Elas dão suporte a filtros avançados que permitem uma grande flexibilidade na escolha dos filhos a serem retornados. Os filtros podem especificar filhos pelo identificador exclusivo ou por outros identificadores de grupo e/ou os estados de integridade. Por padrão, nenhum filho é incluído, ao contrário dos comandos de integridade que incluem sempre o filho de primeiro nível.

As [consultas de integridade](service-fabric-view-entities-aggregated-health.md#health-queries) retornam apenas os filhos de primeiro nível da entidade especificada, de acordo com os filtros necessários. Para obter os filhos dos filhos, você deve chamar outras APIs de integridade para cada entidade de interesse. Da mesma forma, para obter a integridade de entidades específicas, você deve chamar uma API e integridade para cada entidade desejada. A filtragem avançada da consulta em bloco permite a você solicitar vários itens de interesse em uma consulta, reduzindo o tamanho da mensagem e o número de mensagens.

O valor da consulta em blocos é que você pode obter o estado da integridade para mais entidades de cluster (potencialmente todas as entidades de cluster começando na raiz exigida) em uma chamada. Você pode expressar uma consulta de integridade complexa da seguinte maneira:

* Retorne apenas os aplicativos com erro e, para esses aplicativos, inclua todos os serviços em aviso|erro. Para os serviços retornados, inclua todas as partições.
* Retorne apenas a integridade dos quatro aplicativos, especificados por seus nomes.
* Retorne apenas a integridade dos aplicativos de um tipo de aplicativo desejado.
* Retorne todas as entidades implantadas em um nó. Retorna todos os aplicativos, todos os aplicativos implantados no nó especificado e todos os pacotes de serviço implantados nesse nó.
* Retorna todas as réplicas com erro. Retorna todos os aplicativos, serviços, partições e somente réplicas com erro.
* Retorna todos os aplicativos. Para um serviço específico, inclua todas as partições.

Atualmente, a consulta de integridade em blocos é exposta somente para a entidade do cluster. Ela retorna uma parte da integridade do cluster, que contém:

* O estado de integridade agregado do cluster.
* A lista de nós da parte de estado de integridade que respeita os filtros de entrada.
* A lista de aplicativos da parte de estado de integridade que respeita os filtros de entrada. Cada parte do estado de integridade do aplicativo contém uma lista com todos os serviços que respeitam os filtros de entrada e uma lista com todos os aplicativos implantados que respeitam os filtros. O mesmo vale para os filhos dos serviços e aplicativos implantados. Dessa forma, todas as entidades no cluster podem retornar caso solicitado, de uma maneira hierárquica.

### <a name="cluster-health-chunk-query"></a>Consulta em bloco sobre a integridade do cluster
Retorna a integridade da entidade do cluster e contém as partes hierárquicas do estado de integridade dos filhos necessários. Entrada:

* [Opcional] A política de integridade do cluster usada para avaliar os nós e os eventos de cluster.
* [Opcional] Mapa da política de integridade do aplicativo, com políticas de integridade usadas para substituir as políticas do manifesto do aplicativo.
* [Opcional] Filtros de eventos, nós e aplicativos que especificam quais entradas são interessantes e devem retornar no resultado. Os filtros são específicos a uma entidade/grupo de entidades ou são aplicáveis a todas as entidades nesse nível. A lista de filtros pode conter um filtro geral e/ou filtros para identificadores específicos, a fim de refinar as entidades retornadas pela consulta. Se estiver vazia, os filhos não retornarão por padrão.
  Leia mais sobre os filtros em [NodeHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.nodehealthstatefilter) e [ApplicationHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthstatefilter). O filtros de aplicativo pode especificar de forma recursiva os filtros avançados para os filhos.

O resultado da parte inclui os filhos que respeitam os filtros.

Atualmente, a consulta em bloco não retorna avaliações não íntegras ou eventos de entidade. Essas informações extras podem ser obtidas com a consulta de integridade de cluster existente.

### <a name="api"></a>API
Para obter a parte da integridade do cluster, crie um `FabricClient` e chame o método [GetClusterHealthChunkAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) em seu **HealthManager**. Você pode passar [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) para descrever as políticas de integridade e os filtros avançados.

O código a seguir obtém parte da integridade do cluster com filtros avançados.

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter a integridade do cluster é [Get-ServiceFabricClusterChunkHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealthchunk). Primeiro, conecte-se ao cluster usando o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .

O código a seguir obterá nós apenas se eles apresentarem Erro, exceto para um nó específico, que sempre deverá ser retornado.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters


HealthState                  : Warning
NodeHealthStateChunks        : 
                               TotalCount            : 1

                               NodeName              : _Node_1
                               HealthState           : Ok

ApplicationHealthStateChunks : None
```

O cmdlet a seguir obtém parte do cluster com os filtros de aplicativo.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 1

                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks : 
                                TotalCount            : 1

                                ServiceName           : fabric:/WordCount/WordCountService
                                HealthState           : Error
                                PartitionHealthStateChunks : 
                                    TotalCount            : 1

                                    PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                                    HealthState           : Error
                                    ReplicaHealthStateChunks : 
                                        TotalCount            : 5

                                        ReplicaOrInstanceId   : 131444422293118720
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293118721
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293113678
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293113679
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422260002646
                                        HealthState           : Error
```

O cmdlet a seguir retorna todas as entidades implantadas em um nó.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 2

                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1

                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1

                                    ServiceManifestName   : FAS
                                    ServicePackageActivationId : 
                                    HealthState           : Ok



                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1

                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1

                                    ServiceManifestName   : WordCountServicePkg
                                    ServicePackageActivationId : 
                                    HealthState           : Ok
```

### <a name="rest"></a>REST
Você pode obter parte da integridade do cluster com uma [solicitação GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) ou uma [solicitação POST](https://docs.microsoft.com/rest/api/servicefabric/health-of-cluster), que inclui os filtros avançados e políticas de integridade descritos no corpo.

## <a name="general-queries"></a>Consultas gerais
As consultas gerais retornam a lista de entidades do Service Fabric de um tipo especificado. Elas são expostas por meio da API (métodos em **FabricClient.QueryManager**), dos cmdlets do PowerShell e da REST. Essas consultas agregam subconsultas de vários componentes. Um deles é o [Repositório de Integridade](service-fabric-health-introduction.md#health-store), que preenche o estado de integridade agregada para cada resultado da consulta.  

> [!NOTE]
> As consultas gerais retornam o estado da integridade da entidade agregada e não contêm dados de integridade avançados. Se uma entidade não estiver íntegra, você poderá acompanhá-la com consultas de integridade a fim de obter todas as informações de integridade, incluindo eventos, estados da integridade dos filhos e avaliações não íntegras.
>
>

Se as consultas gerais retornarem um estado de integridade desconhecido para uma entidade, talvez o repositório de integridade não tenha dados completos sobre a entidade. Também é possível que uma subconsulta ao repositório de integridade não tenha êxito (por exemplo, ocorreu um erro de comunicação ou repositório de integridade apresentava alguma restrição). Acompanhe uma consulta de integridade da entidade. Se a subconsulta tiver encontrado erros transitórios, como problemas de rede, essa consulta de acompanhamento poderá ser bem-sucedida. Ela também pode fornecer mais detalhes, com base no repositório de integridade, sobre o motivo de a entidade não ser exposta.

As consultas que contêm **HealthState** para entidades são:

* Lista de nós: retorna os nós da lista no cluster (paginados).
  * API: [FabricClient.QueryClient.GetNodeListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * PowerShell: Get-ServiceFabricNode
* Lista de aplicativos: Retorna a lista de aplicativos no cluster (paginados).
  * API: [FabricClient.QueryClient.GetApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * PowerShell: Get-ServiceFabricApplication
* Lista de serviços: Retorna a lista de serviços em um aplicativo (paginados).
  * API: [FabricClient.QueryClient.GetServiceListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * PowerShell: Get-ServiceFabricService
* Lista de partições: Retorna a lista de partições em um serviço (paginadas).
  * API: [FabricClient.QueryClient.GetPartitionListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * PowerShell: Get-ServiceFabricPartition
* Lista de réplicas: Retorna a lista de réplicas em uma partição (paginadas).
  * API: [FabricClient.QueryClient.GetReplicaListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * PowerShell: Get-ServiceFabricReplica
* Lista de aplicativos implantados: Retorna a lista de aplicativos implantados em um nó.
  * API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication
* Lista de pacotes de serviço implantados: Retorna a lista de pacotes de serviço em um aplicativo implantado.
  * API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication

> [!NOTE]
> Algumas das consultas retornam resultados paginados. O retorno dessas consultas é uma lista derivada de [\<PagedList T >](https://docs.microsoft.com/dotnet/api/system.fabric.query.pagedlist-1). Se os resultados não couberem em uma mensagem, serão retornados apenas uma página e um ContinuationToken que acompanha onde a enumeração parou. Continue a chamar a mesma consulta e a passar o token de continuação da consulta anterior a fim de obter os próximos resultados.

### <a name="examples"></a>Exemplos
O código a seguir obtém os aplicativos não íntegros no cluster:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

O cmdlet a seguir obtém detalhes de aplicativo para o aplicativo fabric:/WordCount. Observe que o estado de integridade é de aviso.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

O cmdlet abaixo obtém os serviços com o estado de integridade de erro:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Error"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Error
```

## <a name="cluster-and-application-upgrades"></a>Atualização de cluster e aplicativo
Durante a atualização monitorada do cluster e do aplicativo, o Service Fabric verifica a integridade para garantir que tudo esteja íntegro e permaneça assim. Se uma entidade não estiver íntegra, conforme avaliação por meio de políticas de integridade, a atualização aplica políticas específicas à atualização para determinar a próxima ação. A atualização pode ser pausada para permitir a interação do usuário (por exemplo, corrigir condições de erro ou alterar políticas), ou pode ser revertida automaticamente para a versão anterior em boa qualidade.

Durante uma atualização de *cluster* , você pode obter o status de atualização do cluster. O status de atualização inclui quaisquer avaliações não íntegras, que apontam para o que não está íntegro no cluster. Se a atualização for revertida devido a problemas de integridade, o status da atualização se lembrará dos últimos motivos não íntegros. Essas informações podem ajudar os administradores a investigar o que deu errado após a atualização ser revertida ou interrompida.

Da mesma forma, durante uma atualização do *aplicativo* , quaisquer avaliações não íntegras ficam contidas no status de atualização do aplicativo.

O exemplo a seguir mostra o status da atualização do aplicativo para um aplicativo fabric:/WordCount modificado. Um watchdog relatou um erro em uma de suas réplicas. A atualização está sendo revertida porque as verificações de integridade não são respeitadas.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2017 5:23:26 PM
FailureTimestampUtc           : 4/21/2017 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Leia mais sobre a [Atualização de aplicativo do Service Fabric](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Usar avaliações de integridade para solucionar problemas
Sempre que houver um problema no cluster ou aplicativo, observe a integridade do cluster ou do aplicativo para identificar o que está errado. As avaliações não íntegras mostram detalhes sobre o que disparou o estado não íntegro atual. Se for necessário, você poderá analisar entidades filho não íntegras para identificar a causa raiz.

Por exemplo, pense em um aplicativo não íntegro porque há um relatório de erros em uma de suas réplicas. O seguinte cmdlet do Powershell mostra as avaliações não íntegras:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount -EventsFilter None -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                        Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                        Unhealthy replica: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', ReplicaOrInstanceId='131444422260002646', AggregatedHealthState='Error'.

                                            Error event: SourceId='MyWatchdog', Property='Memory'.

ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : None
```

Você pode examinar a réplica para saber mais:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricReplicaHealth -ReplicaOrInstanceId 131444422260002646 -PartitionId af2e3e44-a8f8-45ac-9f31-4093eb897600


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Error
UnhealthyEvaluations  : 
                        Error event: SourceId='MyWatchdog', Property='Memory'.

HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : MyWatchdog
                        Property              : Memory
                        HealthState           : Error
                        SequenceNumber        : 131444451657749403
                        SentAt                : 7/13/2017 6:46:05 PM
                        ReceivedAt            : 7/13/2017 6:46:05 PM
                        TTL                   : Infinite
                        Description           : 
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 7/13/2017 6:46:05 PM, LastOk = 1/1/0001 12:00:00 AM
```

> [!NOTE]
> As avaliações não íntegras mostram o primeiro motivo pelo qual a entidade é avaliada com o estado de integridade atual. Pode haver vários outros eventos que disparam esse estado, mas eles não são mostrados nas avaliações. Para obter mais informações, faça uma busca detalhada das entidades de integridade para descobrir todos os relatórios não íntegros no cluster.
>
>

## <a name="next-steps"></a>Próximas etapas
[Usar relatórios de integridade do sistema para solução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Adicionar relatórios de integridade personalizados do Service Fabric](service-fabric-report-health.md)

[Como relatar e verificar a integridade do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização de aplicativos do Service Fabric](service-fabric-application-upgrade.md)
