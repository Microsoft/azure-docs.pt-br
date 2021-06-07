---
title: Consultar eventos de cluster usando as APIs EventStore
description: Saiba como usar as APIs EventStore do Azure Service Fabric para consultar eventos de plataforma
ms.topic: conceptual
ms.date: 02/25/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: bc8909a6f25c3adcba60de639d822769baff703f
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628791"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>Consultar APIs do EventStore para eventos de cluster

Este artigo mostra como consultar as APIs EventStore que estão disponíveis no Service Fabric versão 6.2 e posterior - se você quiser saber mais sobre o serviço EventStore, confira a [Visão geral do serviço EventStore](service-fabric-diagnostics-eventstore.md). No momento, o serviço EventStore só pode acessar dados para os últimos sete dias (isso se baseia na política de retenção de dados de diagnóstico do cluster).

>[!NOTE]
>As APIs de EventStore são GA a partir do Service Fabric versão 6.4 para somente os clusters do Windows em execução no Azure.

As APIs EventStore pode ser acessada diretamente por meio de um ponto de extremidade REST ou programaticamente. Dependendo da consulta, há vários parâmetros necessários para coletar os dados certos. Normalmente, esses parâmetros incluem:
* `api-version`: a versão das APIs EventStore que você está usando
* `StartTimeUtc`: define o início do período que você está interessado em examinar
* `EndTimeUtc`: final do período de tempo

Além desses parâmetros, há parâmetros opcionais disponíveis como:
* `timeout`: substituir o tempo limite padrão de 60 segundos para executar a operação de solicitação
* `eventstypesfilter`: isso dá a você a opção para filtrar os tipos de evento específicos
* `ExcludeAnalysisEvents`: não retornam eventos do 'Analysis'. Por padrão, consultas de EventStore retornarão com eventos de "análise" sempre que possível. Os eventos de análise são eventos de canal operacional mais avançados que contêm informações ou contexto adicional além de um evento normal do Service Fabric e fornecem mais detalhes.
* `SkipCorrelationLookup`: não procura por eventos correlacionados potenciais no cluster. Por padrão, o EventStore tentará correlacionar eventos em um cluster e vincular os eventos sempre que possível. 

As entidades em um cluster podem ser consultas de eventos. Você também pode consultar eventos para todas as entidades do tipo. Por exemplo, você pode consultar eventos de um nó específico ou de todos os nós no cluster. É o conjunto atual de entidades para o qual você pode consultar eventos (com como a consulta seria estruturada):
* Cluster: `/EventsStore/Cluster/Events`
* Nós: `/EventsStore/Nodes/Events`
* Nó: `/EventsStore/Nodes/<NodeName>/$/Events`
* Aplicativos: `/EventsStore/Applications/Events`
* Aplicativo: `/EventsStore/Applications/<AppName>/$/Events`
* Serviços: `/EventsStore/Services/Events`
* Serviço: `/EventsStore/Services/<ServiceName>/$/Events`
* Partições: `/EventsStore/Partitions/Events`
* Partição: `/EventsStore/Partitions/<PartitionID>/$/Events`
* Réplicas: `/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* Réplica: `/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>Ao fazer referência a um aplicativo ou nome do serviço, a consulta não precisará incluir o prefixo "fabric:/". Além disso, se os nomes do seu aplicativo ou serviço tiver um "/" neles, alterne para um "~" para manter a consulta funcionando. Por exemplo, se seu aplicativo aparecer como "fabric:/App1/FrontendApp", as consultas específicas de aplicativo seriam estruturadas como `/EventsStore/Applications/App1~FrontendApp/$/Events`.
>Além disso, os relatórios de integridade para serviços hoje são mostrados sob o aplicativo correspondente, portanto você consultaria eventos `DeployedServiceHealthReportCreated` para a entidade de aplicativo correta. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>Consultar EventStore por meio de pontos de extremidade da API REST

Você pode consultar o EventStore diretamente por meio de um ponto de extremidade REST, fazendo solicitações `GET` para: `<your cluster address>/EventsStore/<entity>/Events/`.

Por exemplo, para consultar todos os eventos de Cluster entre `2018-04-03T18:00:00Z` e `2018-04-04T18:00:00Z`, sua solicitação seria semelhante a:

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.4&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

Isso também pode retornar nenhum evento ou a lista de eventos retornados em json:

```json
Response: 200
Body:
[
  {
    "Kind": "ClusterUpgradeStart",
    "CurrentClusterVersion": "0.0.0.0:",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeType": "Rolling",
    "RollingUpgradeMode": "UnmonitoredAuto",
    "FailureAction": "Manual",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:18:59.4313064Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(0 1 2)",
    "UpgradeDomainElapsedTimeInMs": "78.5288",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:19:59.5729953Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(3 4)",
    "UpgradeDomainElapsedTimeInMs": "0",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.6271949Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeComplete",
    "TargetClusterVersion": "6.2:1.0",
    "OverallUpgradeElapsedTimeInMs": "120196.5212",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.8134457Z",
    "HasCorrelatedEvents": false
  }
]
```

Aqui, podemos ver que entre `2018-04-03T18:00:00Z` e `2018-04-04T18:00:00Z`, este cluster concluiu com êxito seu primeira upgrade quando ele foi posicionado pela primeira vez, de `"CurrentClusterVersion": "0.0.0.0:"` para `"TargetClusterVersion": "6.2:1.0"`, em `"OverallUpgradeElapsedTimeInMs": "120196.5212"`.

## <a name="query-the-eventstore-programmatically"></a>Consultar EventStore programaticamente

Você também pode consultar EventStore programaticamente, por meio da [biblioteca de cliente do Service Fabric](/dotnet/api/overview/azure/service-fabric#client-library).

Assim que seu Cliente do Service Fabric for configurado, você poderá consultar eventos acessando o EventStore desta maneira: `sfhttpClient.EventStore.<request>`

Aqui está uma solicitação de exemplo para todos os eventos do cluster `2018-04-03T18:00:00Z` e `2018-04-04T18:00:00Z`, por meio da função `GetClusterEventListAsync`.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Aqui está outro exemplo que consultará a integridade do cluster e todos os eventos de nó em setembro de 2018 e imprime-os.

```csharp
  const int timeoutSecs = 60;
  var clusterUrl = new Uri(@"http://localhost:19080"); // This example is for a Local cluster
  var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl);

  var clusterHealth = sfhttpClient.Cluster.GetClusterHealthAsync().GetAwaiter().GetResult();
  Console.WriteLine("Cluster Health: {0}", clusterHealth.AggregatedHealthState.Value.ToString());

  
  Console.WriteLine("Querying for node events...");
  var nodesEvents = sfhttpClient.EventsStore.GetNodesEventListAsync(
      "2018-09-01T00:00:00Z",
      "2018-09-30T23:59:59Z",
      timeoutSecs,
      "NodeDown,NodeUp")
      .GetAwaiter()
      .GetResult()
      .ToList();
  Console.WriteLine("Result Count: {0}", nodesEvents.Count());

  foreach (var nodeEvent in nodesEvents)
  {
      Console.Write("Node event happened at {0}, Node name: {1} ", nodeEvent.TimeStamp, nodeEvent.NodeName);
      if (nodeEvent is NodeDownEvent)
      {
          var nodeDownEvent = nodeEvent as NodeDownEvent;
          Console.WriteLine("(Node is down, and it was last up at {0})", nodeDownEvent.LastNodeUpAt);
      }
      else if (nodeEvent is NodeUpEvent)
      {
          var nodeUpEvent = nodeEvent as NodeUpEvent;
          Console.WriteLine("(Node is up, and it was last down at {0})", nodeUpEvent.LastNodeDownAt);
      }
  }
```

## <a name="sample-scenarios-and-queries"></a>Cenários e consultas de exemplo

Aqui estão alguns exemplos sobre como você pode chamar as APIs REST EventStore para reconhecer o status do cluster.

*Atualizações de cluster:*

Para ver a última vez em que o upgrade do cluster foi bem-sucedido ou foi tentado na semana passada, você poderá consultar as APIs para os upgrades concluídos recentemente para seu cluster consultando os eventos "ClusterUpgradeCompleted" no EventStore: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeCompleted`

*Problemas de upgrade de cluster:*

Da mesma forma, se houver problemas com uma atualização recente do cluster, você poderia consultar todos os eventos para a entidade de cluster. Você verá diversos eventos, incluindo o início de upgrades e cada UD para os quais o upgrade foi distribuído com êxito. Você também verá eventos para o ponto em que a reversão foi iniciada e os eventos de integridade correspondentes. Aqui está a consulta que você deseja usar para isso: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Alterações de status de nó:*

Para ver as alterações do status do seu nó nos últimos dias - quando os nós são ativados ou desativados (pela plataforma, o serviço caos ou da entrada do usuário) - use a seguinte consulta: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Eventos de aplicativo:*

Você também pode acompanhar suas implantações e upgrades recentes de aplicativos. Use a consulta a seguir para ver todos os eventos de aplicativo no seu cluster: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Integridade do histórico de um aplicativo:*

Além de ver apenas eventos de ciclo de vida do aplicativo, você talvez queira ver os dados históricos sobre a integridade de um aplicativo específico. Você pode fazer isso especificando o nome do aplicativo para o qual você deseja coletar os dados. Use esta consulta para obter todos os eventos de integridade do aplicativo: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.4&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ApplicationNewHealthReport`. Se você deseja incluir eventos de integridade que possam ter expirado (o tempo de vida (TTL) passou), adicione `,ApplicationHealthReportExpired` ao final da consulta para filtrar dois tipos de eventos.

*Integridade do histórico para todos os serviços em "myApp":*

Atualmente, os eventos de relatório de integridade para serviços aparecem como eventos do `DeployedServicePackageNewHealthReport` sob a entidade correspondente do aplicativo. Para ver como seus serviços têm feito para "App1", use a seguinte consulta: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServicePackageNewHealthReport`

*Reconfiguração da partição:*

Para ver todos os movimentos de partição que ocorreram em seu cluster, consulte o evento `PartitionReconfigured`. Isso pode ajudá-lo a descobrir quais cargas de trabalho foram executadas em qual nó em horários específicos ao diagnosticar problemas no seu cluster. Aqui está um exemplo de consulta que faz isso: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.4&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigured`

*Serviço Caos:*

Há um evento para quando o serviço Caos é iniciado ou interrompido que é exposto no nível do cluster. Para ver seu uso recente do serviço Caos, use a seguinte consulta: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`
