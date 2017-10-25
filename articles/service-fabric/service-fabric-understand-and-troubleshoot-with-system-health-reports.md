---
title: "Solucionar problemas com relatórios de integridade do sistema | Microsoft Docs"
description: "Descreve os relatórios de integridade enviados por componentes do Service Fabric do Azure e o seu uso para solucionar problemas de cluster ou de aplicativos"
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: 
ms.assetid: 52574ea7-eb37-47e0-a20a-101539177625
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: oanapl
ms.openlocfilehash: 21f04c1b01033adcef7b7d73c710dd2b4590f76f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Usar relatórios de integridade do sistema para solução de problemas
Os componentes do Service Fabric do Azure apresentam relatórios de integridade do sistema em todas as entidades no cluster prontos para uso. O [repositório de integridade](service-fabric-health-introduction.md#health-store) cria e exclui entidades baseado nos relatórios do sistema. Ele também os organiza em uma hierarquia que captura interações de entidade.

> [!NOTE]
> Leia mais sobre o [Modelo de Integridade do Service Fabric](service-fabric-health-introduction.md)para entender os conceitos relacionados à integridade.
> 
> 

Os relatórios de integridade do sistema dão visibilidade da funcionalidade do cluster e do aplicativo e sinalizam problemas. Para aplicativos e serviços, os relatórios de integridade do sistema verificam se as entidades são implementadas e estão se comportando corretamente da perspectiva do Service Fabric. Os relatórios não fornecem monitoramento de integridade da lógica de negócios do serviço nem detecção de processos travados. Os serviços de usuário podem enriquecer os dados de integridade com informações específicas à respectiva lógica.

> [!NOTE]
> Os relatórios de integridade enviados pelos watchdogs do usuário ficam visíveis somente *depois* que os componentes do sistema criam uma entidade. Quando uma entidade é excluída, o repositório de integridade exclui automaticamente todos os relatórios de integridade associados a ela. O mesmo acontece quando uma nova instância da entidade é criada, por exemplo, uma nova instância de réplica do serviço persistente com estado é criada. Todos os relatórios associados à instância antiga são excluídos e removidos do repositório.
> 
> 

Os relatórios de componentes do sistema são identificados por sua origem, que começa com o prefixo "**System.**" prefixo. Os watchdogs não podem usar o mesmo prefixo para suas origens, pois os relatórios com parâmetros inválidos são rejeitados.

Vamos examinar alguns relatórios do sistema para entender o que os dispara e aprender a corrigir os possíveis problemas que eles representam.

> [!NOTE]
> O Service Fabric continua a adicionar relatórios sobre as condições de interesse que melhoram a visibilidade sobre o que está acontecendo no cluster e os relatórios Existentes de aplicativos podem ser aprimorados com mais detalhes para ajudar a solucionar o problema mais rapidamente.
> 
> 

## <a name="cluster-system-health-reports"></a>Relatórios de integridade do sistema de cluster
A entidade de integridade do cluster é criada automaticamente no repositório de integridade. Se tudo funcionar corretamente, ele não terá um relatório do sistema.

### <a name="neighborhood-loss"></a>Perda de ambiente
**System.Federation** relata um erro quando detecta uma perda de ambiente. O relatório tem origem em nós individuais e a ID do nó é incluída no nome da propriedade. Se uma vizinhança for perdida em todo o anel do Service Fabric, você geralmente poderá esperar dois eventos representando os dois lados do relatório de lacuna. Se houver mais perdas de ambiente, haverá mais eventos.

O relatório especifica o tempo limite de concessão global como a TTL (vida útil). O relatório é enviado novamente a cada metade da duração do tempo de vida útil, desde que a condição permaneça ativa. O evento é removido automaticamente quando expira. O comportamento de remover quando expirado garante que o relatório seja removido corretamente do repositório de integridade, mesmo que o nó de relatório esteja inoperante.

* **SourceId**: System.Federation
* **Propriedade**: começa com **Vizinhança** e inclui informações sobre o nó.
* **Próximas etapas**: investigue por que a vizinhança foi perdida (por exemplo, verifique a comunicação entre os nós do cluster).

## <a name="node-system-health-reports"></a>Relatórios de integridade do sistema de nó
**System.FM**, que representa o serviço Gerenciador de Failover, é a autoridade que gerencia informações sobre nós de cluster. Cada nó deve ter um relatório de System.FM mostrando seu estado. As entidades de nó são removidas quando o estado do nó é removido. Para obter mais informações, consulte [RemoveNodeStateAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync).

### <a name="node-updown"></a>Nó ativo/inativo
System.FM relata OK quando o nó ingressa no anel (está em execução). Ele relata um erro quando o nó é removido do anel (ele está desativado, seja para atualização ou simplesmente porque falhou). A hierarquia de integridade criada pelo repositório de integridade age nas entidades implantadas em correlação com relatórios de nó do System.FM. Ela considera o nó como um pai virtual de todas as entidades implantadas. As entidades implantadas nesse nó serão expostas por meio de consultas se o nó for reportado como ativo pelo System.FM, com a mesma instância que aquela associada às entidades. Quando System.FM relata que o nó está inativo ou foi reiniciado, como uma nova instância, o repositório de integridade automaticamente limpa as entidades implantadas que podem existir apenas no nó inativo ou na instância anterior do nó.

* **SourceId**: System.FM
* **Propriedade**: estado.
* **Próximas etapas**: se o nó estiver inoperante para uma atualização, ele deverá aparecer novamente depois que do upgrade. Nesse caso, o estado de integridade deve ser alternado de volta para OK. Se o nó não voltar ou falhar, será preciso investigar mais.

O exemplo a seguir mostra o evento System.FM com estado de integridade OK para o nó ativo:

```PowerShell
PS C:\> Get-ServiceFabricNodeHealth  _Node_0

NodeName              : _Node_0
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 8
                        SentAt                : 7/14/2017 4:54:51 PM
                        ReceivedAt            : 7/14/2017 4:55:14 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```


### <a name="certificate-expiration"></a>Expiração de certificado
**System.FabricNode** relata um aviso quando os certificados usados pelo nó estão prestes a expirar. Há três certificados por nó: **Certificate_cluster**, **Certificate_server** e **Certificate_default_client**. Quando falta pelo menos duas semanas para expirar, o estado de integridade do relatório é OK. Quando a expiração é dentro de duas semanas, o tipo de relatório é um aviso. O tempo de vida útil desses eventos é infinito; eles são removidos quando um nó deixa o cluster.

* **SourceId**: System.FabricNode
* **Propriedade**: começa com **Certificate** e contém mais informações sobre o tipo de certificado.
* **Próximas etapas**: atualize os certificados se eles estiverem prestes a expirar.

### <a name="load-capacity-violation"></a>Violação da capacidade de carga
O Service Fabric Load Balancer relata um aviso quando detecta uma violação da capacidade do nó.

* **SourceId**: System.PLB
* **Propriedade**: começa com **Capacidade**.
* **Próximas etapas**: verifique as métricas fornecidas e exiba a capacidade atual do nó.

## <a name="application-system-health-reports"></a>Relatórios de integridade do sistema de aplicativo
**System.CM**, que representa o serviço do Gerenciador de Cluster, é a autoridade que gerencia as informações sobre um aplicativo.

### <a name="state"></a>Estado
System.CM relata OK quando o aplicativo é criado ou atualizado. Informa ao repositório de integridade quando o aplicativo foi excluído para que ele possa ser removido do repositório.

* **SourceId**: System.CM
* **Propriedade**: estado.
* **Próximas etapas**: se o aplicativo tiver sido criado ou atualizado, ele deverá incluir o relatório de integridade do Gerenciador de Cluster. Caso contrário, verifique o estado do aplicativo emitindo uma consulta, por exemplo, o cmdlet do PowerShell **Get-ServiceFabricApplication -ApplicationName** *applicationName*.

O exemplo a seguir mostra o evento de estado no aplicativo **fabric:/WordCount** :

```PowerShell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/14/2017 4:55:10 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="service-system-health-reports"></a>Relatórios de integridade do sistema de serviço
**System.FM**, que representa o serviço do Gerenciador de Failover, é a autoridade que gerencia as informações sobre serviços.

### <a name="state"></a>Estado
System.FM relata OK quando o serviço é criado. Ele exclui a entidade do repositório de integridade quando o serviço é excluído.

* **SourceId**: System.FM
* **Propriedade**: estado.

O exemplo a seguir mostra o evento de estado no serviço **fabric:/WordCount/WordCountWebService**:

```PowerShell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountWebService -ExcludeHealthStatistics


ServiceName           : fabric:/WordCount/WordCountWebService
AggregatedHealthState : Ok
PartitionHealthStates : 
                        PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
                        AggregatedHealthState : Ok
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 14
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="service-correlation-error"></a>Erro de correlação de serviço
**System.PLB** relata um erro ao detectar que atualizar um serviço está correlacionado a outro serviço que cria uma cadeia de afinidade. O relatório é limpo quando a atualização é bem-sucedida.

* **SourceId**: System.PLB
* **Propriedade**: ServiceDescription.
* **Próximas etapas**: verifique as descrições de serviços correlacionadas.

## <a name="partition-system-health-reports"></a>Relatórios de integridade do sistema de partição
**System.FM**, que representa o serviço do Gerenciador de Failover, é a autoridade que gerencia as informações sobre partições de serviço.

### <a name="state"></a>Estado
System.FM relata OK quando a partição é criada e está íntegra. Ele exclui a entidade do repositório de integridade quando a partição é excluída.

Se a partição estiver abaixo da contagem mínima de réplica, ela relatará um erro. Se a partição não estiver abaixo da contagem mínima de réplica, mas estiver abaixo da contagem de réplica de destino, ela relatará um aviso. Se a partição estiver na perda de quórum, o System.FM relatará um erro.

Outros eventos importantes incluem avisos quando a reconfiguração demora mais que o esperado e quando o build demora mais que o esperado. Os tempos esperados para build ou reconfiguração podem ser configurados conforme os cenários de serviço. Por exemplo, se um serviço tiver um terabyte de estado, como o Banco de Dados SQL do Azure, o build demorará mais do que demoraria para um serviço com uma pequena quantidade de estado.

* **SourceId**: System.FM
* **Propriedade**: estado.
* **Próximas etapas**: se o estado de integridade não está OK, é possível que algumas réplicas não tenham sido criadas, abertas ou promovidas para o primário ou secundário corretamente. 

Se a descrição indicar uma perda de quorum, examinar o relatório de integridade detalhado quanto a réplicas inoperantes e deixá-las operantes novamente ajudará a deixar a partição online novamente.

Se a descrição for de uma partição paralisada na [reconfiguração](service-fabric-concepts-reconfiguration.md), o relatório de integridade na réplica primária fornecerá informações adicionais.

Para outros relatórios de integridade System.FM, haveria relatórios sobre as réplicas ou sobre a partição ou serviço de outros componentes do sistema. 

Os exemplos a seguir descrevem alguns desses relatórios. 

O exemplo a seguir mostra uma partição íntegra:

```PowerShell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountWebService | Get-ServiceFabricPartitionHealth -ExcludeHealthStatistics -ReplicasFilter None

PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 70
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

O exemplo a seguir mostra a integridade de uma partição que está abaixo da contagem de réplica de destino. A etapa seguinte é obter a descrição da partição, que mostra como ela é configurada: **MinReplicaSetSize** é três e **TargetReplicaSetSize** é sete. Em seguida, obtenha o número de nós no cluster, que, neste caso, é cinco. Assim, neste caso, duas réplicas não podem ser colocadas porque o número de réplicas de destino é maior do que o número de nós disponíveis.

```PowerShell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None -ExcludeHealthStatistics


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 123
                        SentAt                : 7/14/2017 4:55:39 PM
                        ReceivedAt            : 7/14/2017 4:55:44 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/S Ready _Node_2 131444422260002646
                          N/S Ready _Node_4 131444422293113678
                          N/S Ready _Node_3 131444422293113679
                          N/S Ready _Node_1 131444422293118720
                          N/P Ready _Node_0 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:55:44 PM, LastOk = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131445250939703027
                        SentAt                : 7/14/2017 4:58:13 PM
                        ReceivedAt            : 7/14/2017 4:58:14 PM
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
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:56:14 PM, LastOk = 1/1/0001 12:00:00 AM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | select MinReplicaSetSize,TargetReplicaSetSize

MinReplicaSetSize TargetReplicaSetSize
----------------- --------------------
                2                    7                        

PS C:\> @(Get-ServiceFabricNode).Count
5
```

O exemplo a seguir mostra a integridade de uma partição paralisada na reconfiguração porque o usuário não respeitou o token de cancelamento no método **RunAsync**. Investigar o relatório de integridade de qualquer réplica marcada como P (primária) pode ajudar a fazer drill down adicional do problema.

```PowerShell
PS C:\utilities\ServiceFabricExplorer\ClientPackage\lib> Get-ServiceFabricPartitionHealth 0e40fd81-284d-4be4-a665-13bc5a6607ec -ExcludeHealthStatistics 


PartitionId           : 0e40fd81-284d-4be4-a665-13bc5a6607ec
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                                               
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 7
                        SentAt                : 8/27/2017 3:43:09 AM
                        ReceivedAt            : 8/27/2017 3:43:32 AM
                        TTL                   : Infinite
                        Description           : Partition reconfiguration is taking longer than expected.
                        fabric:/app/test1 3 1 0e40fd81-284d-4be4-a665-13bc5a6607ec
                          P/S Ready Node1 131482789658160654
                          S/P Ready Node2 131482789688598467
                          S/S Ready Node3 131482789688598468
                          (Showing 3 out of 3 replicas. Total available replicas: 3)                        
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/27/2017 3:43:32 AM, LastError = 1/1/0001 12:00:00 AM
```
Este relatório de integridade mostra o estado das réplicas da partição submetidas à reconfiguração: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

Para cada réplica, o relatório de integridade contém:
- Função de configuração anterior
- Função de configuração atual
- [Estado da réplica](service-fabric-concepts-replica-lifecycle.md)
- Nó no qual a réplica está em execução
- ID da réplica

Em casos como o exemplo, investigação adicional é necessária. Investigue a integridade de cada réplica individual, começando com as réplicas marcadas como `Primary` e `Secondary` (131482789658160654 e 131482789688598467) no exemplo anterior.

### <a name="replica-constraint-violation"></a>Violação da restrição de réplica
**System.PLB** relata um aviso se detectar uma violação de restrição de réplica e não puder posicionar todas as réplicas da partição. Os detalhes do relatório mostram quais restrições e propriedades impedem o posicionamento da réplica.

* **SourceId**: System.PLB
* **Propriedade**: começa com **ReplicaConstraintViolation**.

## <a name="replica-system-health-reports"></a>Relatórios de integridade do sistema de réplica
**System.RA**, que representa o componente do agente de reconfiguração, é a autoridade do estado da réplica.

### <a name="state"></a>Estado
System.RA relata OK quando a réplica é criada.

* **SourceId**: System.RA
* **Propriedade**: estado.

O exemplo a seguir mostra uma réplica íntegra:

```PowerShell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422293118721
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131445248920273536
                        SentAt                : 7/14/2017 4:54:52 PM
                        ReceivedAt            : 7/14/2017 4:55:13 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_0
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:13 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="replicaopenstatus-replicaclosestatus-replicachangerolestatus"></a>ReplicaOpenStatus, ReplicaCloseStatus, ReplicaChangeRoleStatus
Essa propriedade é usada para indicar avisos ou falhas ao tentar abrir uma réplica, fechar uma réplica ou fazer a transição de uma réplica de uma função para outra. Para obter mais informações, consulte [Replica lifecycle](service-fabric-concepts-replica-lifecycle.md) (Ciclo de vida da réplica). As falhas podem ser exceções geradas das chamadas à API ou falhas do processo de host de serviço durante esse tempo. Para falhas devido a chamadas à API do código C#, o Service Fabric adiciona a exceção e o rastreamento de pilha ao relatório de integridade.

Esses avisos de integridade são gerados depois de tentar novamente a ação localmente várias vezes (dependendo da política). O Service Fabric repete a ação até um limite máximo. Depois que o limite máximo é atingido, ele pode tentar agir para corrigir a situação. Essa tentativa pode fazer esses avisos serem removidos, uma vez que desistem da ação neste nó. Por exemplo, se uma réplica estiver falhando em ser aberta em um nó, o Service Fabric gerará um aviso de integridade. Se a réplica continuar a falhar em abrir, o Service Fabric agirá para fazer um autorreparo. Esta ação pode envolver a tentar a mesma operação em outro nó. Isso faz o aviso gerado para essa réplica ser apagado. 

* **SourceId**: System.RA
* **Propriedade**: **ReplicaOpenStatus**, **ReplicaCloseStatus** e **ReplicaChangeRoleStatus**.
* **Próximas etapas**: investigue os despejos de memória ou o código do serviço para identificar por que a operação está falhando.

O exemplo a seguir mostra a integridade de uma réplica que está gerando `TargetInvocationException` de seu método aberto. A descrição contém o ponto de falha **IStatefulServiceReplica.Open**, o tipo de exceção **TargetInvocationException** e o rastreamento de pilha.

```PowerShell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 337cf1df-6cab-4825-99a9-7595090c0b1b -ReplicaOrInstanceId 131483509874784794


PartitionId           : 337cf1df-6cab-4825-99a9-7595090c0b1b
ReplicaId             : 131483509874784794
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaOpenStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaOpenStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483510001453159
                        SentAt                : 8/27/2017 11:43:20 PM
                        ReceivedAt            : 8/27/2017 11:43:21 PM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during open on _Node_0 API call: IStatefulServiceReplica.Open(); Error = System.Reflection.TargetInvocationException (-2146232828)
Exception has been thrown by the target of an invocation.
   at Microsoft.ServiceFabric.Replicator.RecoveryManager.d__31.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.LoggingReplicator.d__137.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.DynamicStateManager.d__109.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.TransactionalReplicator.d__79.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.StatefulServiceReplica.d__21.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Services.Runtime.StatefulServiceReplicaAdapter.d__0.MoveNext()

    For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/27/2017 11:43:21 PM, LastOk = 1/1/0001 12:00:00 AM                        
```

O exemplo a seguir mostra a réplica que está falhando constantemente durante o fechamento:

```PowerShell
C:>Get-ServiceFabricReplicaHealth -PartitionId dcafb6b7-9446-425c-8b90-b3fdf3859e64 -ReplicaOrInstanceId 131483565548493142


PartitionId           : dcafb6b7-9446-425c-8b90-b3fdf3859e64
ReplicaId             : 131483565548493142
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaCloseStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaCloseStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483565611258984
                        SentAt                : 8/28/2017 1:16:01 AM
                        ReceivedAt            : 8/28/2017 1:16:03 AM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during close on _Node_1. The application 
                        host has crashed.
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 1:16:03 AM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="reconfiguration"></a>Reconfiguração
Esta propriedade é usada para indicar quando uma réplica que está executando uma [reconfiguração](service-fabric-concepts-reconfiguration.md) detecta que a reconfiguração é interrompida ou paralisada. Esse relatório de integridade poder estar na réplica cuja função atual é primária (exceto nos casos de uma reconfiguração primária de troca, em que talvez esteja na réplica que está sendo rebaixada de primária para secundária ativa).

A reconfiguração pode ficar paralisada por um destes motivos:

- Uma ação na réplica local, a mesma réplica que a que está executando a reconfiguração, não está sendo concluída. Neste caso, investigar os relatórios de integridade nesta réplica de outros componentes, System.RAP ou System.RE, talvez forneça informações adicionais.

- Uma ação não está sendo concluída em uma réplica remota. As réplicas para as quais as ações estão pendentes são listadas no relatório de integridade. Deverá ser realizada uma investigação adicional nos relatórios de integridade quanto a réplicas remotas. Também poderia haver problemas de comunicação entre esse nó e o nó remoto.

Em casos raros, talvez a reconfiguração fique paralisada devido à comunicação ou outros problemas entre esse nó e o serviço de Gerenciador de Failover.

* **SourceId**: System.RA
* **Propriedade**: **reconfiguração**.
* **Próximas etapas**: investigue réplicas locais ou remotas dependendo da descrição do relatório de integridade.

O exemplo a seguir mostra um relatório de integridade em que uma reconfiguração está paralisada na réplica local. Neste exemplo, isso se deve a um serviço não aceitar o token de cancelamento.

```PowerShell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 9a0cedee-464c-4603-abbc-1cf57c4454f3 -ReplicaOrInstanceId 131483600074836703


PartitionId           : 9a0cedee-464c-4603-abbc-1cf57c4454f3
ReplicaId             : 131483600074836703
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483600309264482
                        SentAt                : 8/28/2017 2:13:50 AM
                        ReceivedAt            : 8/28/2017 2:13:57 AM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from the local replica
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 2:13:57 AM, LastOk = 1/1/0001 12:00:00 AM
```

O exemplo a seguir mostra um relatório de integridade em que a reconfiguração está paralisada aguardando uma resposta de duas réplicas remotas. Neste exemplo, há três réplicas na partição, incluindo a primária atual. 

```Powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId  579d50c6-d670-4d25-af70-d706e4bc19a2 -ReplicaOrInstanceId 131483956274977415


PartitionId           : 579d50c6-d670-4d25-af70-d706e4bc19a2
ReplicaId             : 131483956274977415
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483960376212469
                        SentAt                : 8/28/2017 12:13:57 PM
                        ReceivedAt            : 8/28/2017 12:14:07 PM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from 2 replicas
                        
                        Pending Replicas: 
                        P/I Down 40 131483956244554282
                        S/S Down 20 131483956274972403
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:07:37 PM, LastOk = 1/1/0001 12:00:00 AM
```

Esse relatório de integridade mostra que a reconfiguração está paralisada aguardando uma resposta de duas réplicas: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

Para cada réplica, as informações a seguir são fornecidas:
- Função de configuração anterior
- Função de configuração atual
- [Estado da réplica](service-fabric-concepts-replica-lifecycle.md)
- ID do nó
- ID da réplica

Para desbloquear a reconfiguração:
- As réplicas **inoperantes** devem ser colocadas em operação. 
- As réplicas **integradas** devem concluir o build e a transição para estarem prontas.

### <a name="slow-service-api-call"></a>Chamada à API para serviço lento
**System.RAP** e **System.Replicator** relatarão um aviso se uma chamada para o código do serviço do usuário demorar mais que o tempo configurado. Esse status é removido quando a chamada é concluída.

* **SourceId**: System.RAP ou System.Replicator
* **Propriedade**: o nome da API lenta. A descrição fornece mais detalhes sobre o tempo em que a API esteve pendente.
* **Próximas etapas**: investigue o motivo pelo qual a chamada demora mais que o esperado.

O exemplo a seguir mostra o evento de integridade de System.RAP para um serviço confiável que não está respeitando o token de cancelamento em **RunAsync**:

```PowerShell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 5f6060fb-096f-45e4-8c3d-c26444d8dd10 -ReplicaOrInstanceId 131483966141404693


PartitionId           : 5f6060fb-096f-45e4-8c3d-c26444d8dd10
ReplicaId             : 131483966141404693
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          :                         
                        SourceId              : System.RAP
                        Property              : IStatefulServiceReplica.ChangeRole(S)Duration
                        HealthState           : Warning
                        SequenceNumber        : 131483966663476570
                        SentAt                : 8/28/2017 12:24:26 PM
                        ReceivedAt            : 8/28/2017 12:24:56 PM
                        TTL                   : Infinite
                        Description           : The api IStatefulServiceReplica.ChangeRole(S) on _Node_1 is stuck. Start Time (UTC): 2017-08-28 12:23:56.347.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:24:56 PM, LastOk = 1/1/0001 12:00:00 AM
                        
```

A propriedade e o texto indicam qual API ficou paralisada. As próximas etapas a adotar para diferentes APIs paralisadas são diferentes. Qualquer API em *IStatefulServiceReplica* ou no *IStatelessServiceInstance* geralmente é um bug no código de serviço. A seção a seguir descreve como elas são convertidas no [modelo de Reliable Services](service-fabric-reliable-services-lifecycle.md):

- **IStatefulServiceReplica.Open**: indica que uma chamada para `CreateServiceInstanceListeners` ou `ICommunicationListener.OpenAsync` ou, se substituída, `OnOpenAsync` está paralisada.

- **IStatefulServiceReplica.Close** e **IStatefulServiceReplica.Abort**: o caso mais comum é um serviço que não respeita o token de cancelamento passado para `RunAsync`. Também pode ser que `ICommunicationListener.CloseAsync` ou, se substituído, `OnCloseAsync` esteja paralisado.

- **IStatefulServiceReplica.ChangeRole(S)** e **IStatefulServiceReplica.ChangeRole(N)**: o caso mais comum é um serviço que não está respeitando o token de cancelamento passado para `RunAsync`.

- **IStatefulServiceReplica.ChangeRole(P)**: o caso mais comum é que o serviço não retornou uma tarefa de `RunAsync`.

Outras chamadas à API que podem ficar paralisadas estão na interface **IReplicator**. Por exemplo:

- **IReplicator.CatchupReplicaSet**: este aviso indica que um dos seguintes. Há réplicas ativas insuficientes, o que pode ser determinado examinando o status das réplicas na partição ou no relatório de integridade System.FM para uma reconfiguração paralisada. Ou as réplicas não estão confirmando operações. O cmdlet do PowerShell `Get-ServiceFabricDeployedReplicaDetail` pode ser usado para determinar o andamento de todas as réplicas. O problema etá nas réplicas cujo `LastAppliedReplicationSequenceNumber` está atrás do `CommittedSequenceNumber` do principal.

- **IReplicator.BuildReplica (<Remote ReplicaId>)**: este aviso indica um problema no processo de build. Para obter mais informações, consulte [Replica lifecycle](service-fabric-concepts-replica-lifecycle.md) (Ciclo de vida da réplica). Isso pode acontecer devido a uma configuração incorreta do endereço do replicador. Para obter mais informações, consulte [Configurar Reliable Services com estado](service-fabric-reliable-services-configuration.md) e [Especificar recursos em um manifesto de serviço](service-fabric-service-manifest-resources.md). Também pode ser um problema no nó remoto.

### <a name="replication-queue-full"></a>Fila de replicação cheia
**System.Replicator** relata um aviso quando a fila de replicação está cheia. Na primária, a fila de replicação normalmente fica cheia porque uma ou mais réplicas secundárias são lentas em confirmar operações. No local secundário, isso geralmente acontece quando o serviço está lento para aplicar as operações. O aviso será removido quando a fila não estiver mais cheia.

* **SourceId**: System.Replicator
* **Propriedade**: **PrimaryReplicationQueueStatus** ou **SecondaryReplicationQueueStatus**, dependendo da função da réplica.

### <a name="slow-naming-operations"></a>Operações de Nomeação lentas
**System.NamingService** relata a integridade na réplica primária quando uma operação de Nomenclatura demora mais do que o aceitável. Exemplos de operações de Nomeação: [CreateServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) ou [DeleteServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync). Mais métodos podem ser encontrados em FabricClient, por exemplo, em [métodos de gerenciamento do serviço](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient) ou [métodos de gerenciamento de propriedade](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.propertymanagementclient).

> [!NOTE]
> O serviço de nomenclatura resolve nomes de serviço para um local no cluster e permite aos usuários gerenciar propriedades e nomes de serviço. É um serviço particionado-persistente do Service Fabric. Uma das partições representa o *Proprietário da Autoridade*, que contém metadados sobre todos os nomes e serviços do Service Fabric. Os nomes do Service Fabric são mapeados para partições diferentes, chamadas de partições de *Proprietário de Nome*, assim, o serviço é extensível. Leia mais sobre o [Serviço de nomenclatura](service-fabric-architecture.md).
> 
> 

Quando uma operação de Nomeação leva mais tempo do que o esperado, a operação é sinalizada com um relatório de aviso na réplica primária da partição de serviço de Nomeação que atende à operação. Se a operação for concluída com êxito, o aviso será eliminado. Se a operação for concluída com um erro, o relatório de integridade incluirá detalhes sobre o erro.

* **SourceId**: System.NamingService
* **Propriedade**: começa com o prefixo “**Duration_**” e identifica a operação lenta e o nome do Service Fabric em que a operação é aplicada. Por exemplo, se criar um serviço em name **fabric: /MyApp/MyService** levar muito tempo, a propriedade será **Duration_AOCreateService.fabric:/MyApp/MyService**. “AO” aponta para a função da partição de Nomenclatura para esse nome e operação.
* **Próximas etapas**: verifique por que a operação de Nomenclatura falha. Cada operação pode ter causas raiz diferentes. Por exemplo, o serviço de exclusão pode estar paralisado. O serviço pode estar paralisado porque o host do aplicativo fica travando em um nó devido a um bug de usuário no código de serviço.

O exemplo a seguir mostra uma operação de criação de serviço. A operação demorou mais do que a duração configurada. “AO” tenta novamente e envia o trabalho para “NO”. “NO” concluiu a última operação com TIMEOUT. Nesse caso, a mesma réplica é primária para as funções “AO” e “NO”.

```PowerShell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>Relatórios de integridade do sistema DeployedApplication
**System.Hosting** é a autoridade nas entidades implantadas.

### <a name="activation"></a>Ativação
System.Hosting relata OK quando um aplicativo é ativado com êxito no nó. Caso contrário, ele relata um erro.

* **SourceId**: System.Hosting
* **Propriedade**: ativação, incluindo a versão de distribuição.
* **Próximas etapas**: se o aplicativo não estiver íntegro, investigue o motivo pelo qual a ativação falhou.

O exemplo a seguir mostra uma ativação bem-sucedida:

```PowerShell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ExcludeHealthStatistics

ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_1
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131445249083836329
                                     SentAt                : 7/14/2017 4:55:08 PM
                                     ReceivedAt            : 7/14/2017 4:55:14 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Baixar
System.Hosting relatará um erro se o download do pacote de aplicativos falhar.

* **SourceId**: System.Hosting
* **Propriedade**: **download:***RolloutVersion*.
* **Próximas etapas**: investigue o motivo pelo qual o download falhou no nó.

## <a name="deployedservicepackage-system-health-reports"></a>Relatórios de integridade do sistema DeployedServicePackage
**System.Hosting** é a autoridade nas entidades implantadas.

### <a name="service-package-activation"></a>Ativação do pacote de serviço
System.Hosting relatará OK se a ativação do pacote de serviço no nó for bem-sucedida. Caso contrário, ele relata um erro.

* **SourceId**: System.Hosting
* **Propriedade**: ativação.
* **Próximas etapas**: investigue o motivo pelo qual a ativação falhou.

### <a name="code-package-activation"></a>Ativação do pacote de códigos
System.Hosting será relatado como OK para cada pacote de códigos se a ativação for bem-sucedida. Se houver falha na ativação, ele relatará um aviso conforme configurado. Se **CodePackage** falhar em ativar ou terminar com um erro maior que o **CodePackageHealthErrorThreshold** configurado, a hospedagem relatará um erro. Se um pacote de serviço contiver vários pacotes de código, um relatório de ativação será gerado para cada um.

* **SourceId**: System.Hosting
* **Propriedade**: usa o prefixo **CodePackageActivation** e contém o nome do pacote de códigos e o ponto de entrada como **CodePackageActivation:***CodePackageName*:*SetupEntryPoint/EntryPoint*. Por exemplo, **CodePackageActivation:Code:SetupEntryPoint**.

### <a name="service-type-registration"></a>Registro do tipo de serviço
System.Hosting relatará OK se o tipo de serviço tiver sido registrado com êxito. Ele relatará um erro se o registro não tiver sido feito no prazo, conforme configurado usando **ServiceTypeRegistrationTimeout**. Se o tempo de execução estiver fechado, o registro do tipo de serviço será cancelado do nó e a hospedagem relatará um aviso.

* **SourceId**: System.Hosting
* **Propriedade**: usa o prefixo **ServiceTypeRegistration** e contém o nome do tipo de serviço. Por exemplo, **ServiceTypeRegistration:FileStoreServiceType**.

O exemplo a seguir mostra um pacote de serviço íntegro implantado:

```PowerShell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_1
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131445249084026346
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131445249084306362
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131445249088096842
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Baixar
System.Hosting relatará um erro se o download do pacote de serviço falhar.

* **SourceId**: System.Hosting
* **Propriedade**: **download:***RolloutVersion*.
* **Próximas etapas**: investigue o motivo pelo qual o download falhou no nó.

### <a name="upgrade-validation"></a>Validação da atualização
System.Hosting relatará um erro se a validação durante a atualização falhar ou se a atualização falhar no nó.

* **SourceId**: System.Hosting
* **Propriedade**: usa o prefixo **FabricUpgradeValidation** e contém a versão de atualização.
* **Descrição**: aponta para o erro encontrado.

## <a name="next-steps"></a>Próximas etapas
[Como exibir relatórios de integridade do Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Como relatar e verificar a integridade do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização de aplicativos do Service Fabric](service-fabric-application-upgrade.md)

