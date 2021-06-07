---
title: Lista de eventos do Azure Service Fabric
description: Lista abrangente dos eventos fornecida pelo Azure Service Fabric para ajudar a monitorar os clusters.
ms.topic: reference
ms.date: 2/25/2019
ms.openlocfilehash: 3f65d7c3ddec8263ff6f115df9616b48814afc93
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628876"
---
# <a name="list-of-service-fabric-events"></a>Lista de eventos do Service Fabric 

O Service Fabric expõe um conjunto primário de eventos de cluster para informar a você sobre o status do seu cluster como [Eventos do Service Fabric](service-fabric-diagnostics-events.md). Eles são baseados em ações realizadas pelo Service Fabric nos seus nós e no seu cluster ou decisões de gerenciamento tomadas por um proprietário/operador do cluster. Esses eventos podem ser acessados Configurando de várias maneiras, incluindo a configuração de [logs de Azure monitor com o cluster](service-fabric-diagnostics-oms-setup.md)ou a consulta do [EventStore](service-fabric-diagnostics-eventstore.md). Nos computadores Windows, esses eventos são alimentados no EventLog - então você pode ver os Eventos do Service Fabric no Visualizador de Eventos. 

Estas são algumas características desses eventos
* Cada evento está vinculado a uma entidade específica no cluster, por exemplo, aplicativo, serviço, nó, réplica.
* Cada evento contém um conjunto de campos comuns: EventInstanceId, EventName e Category.
* Cada evento contém campos que integram o evento com a entidade que está associada. Por exemplo, o evento ApplicationCreated teria os campos que identificam o nome do aplicativo criado.
* Eventos são estruturados de forma que possam ser consumidos em uma variedade de ferramentas para fazer análise posterior. Além disso, os detalhes relevantes para um evento são definidos como propriedades separadas em vez de uma cadeia de caracteres. 
* Os eventos são gravados por diferentes subsistemas no Service Fabric são identificados por Source(Task) abaixo. Mais informações estão disponíveis nesses subsistemas no [Arquitetura do Service Fabric](service-fabric-architecture.md) e [Visão Geral Técnica do Service Fabric](service-fabric-technical-overview.md).

Aqui está uma lista desses eventos do Service Fabric organizados por entidade.

## <a name="cluster-events"></a>Eventos do cluster

**Eventos de atualização do cluster**

Mais detalhes sobre atualizações de cluster podem ser encontrados [aqui](service-fabric-cluster-upgrade-windows-server.md).

| EventId | Nome | Categoria | Descrição |Origem (Tarefa) | Nível | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | Atualizar | Uma atualização de cluster foi iniciada | CM | Informativo |
| 29628 | ClusterUpgradeCompleted | Atualizar | Uma atualização de cluster foi concluída | CM | Informativo | 
| 29629 | ClusterUpgradeRollbackStarted | Atualizar | A reversão de uma atualização de cluster foi iniciada  | CM | Aviso | 
| 29630 | ClusterUpgradeRollbackCompleted | Atualizar | A reversão de uma atualização de cluster foi concluída | CM | Aviso | 
| 29631 | ClusterUpgradeDomainCompleted | Atualizar | Um domínio de atualização terminou de atualizar durante uma atualização de cluster | CM | Informativo | 

## <a name="node-events"></a>Eventos de nó

**Eventos de ciclo de vida do nó** 

| EventId | Nome | Categoria | Descrição |Origem (Tarefa) | Nível |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | StateTransition | A desativação de um nó foi concluída | FM | Informativo | 
| 18603 | NodeUp | StateTransition | O cluster detectou que um nó foi iniciado | FM | Informativo | 
| 18604 | NodeDown | StateTransition | O cluster detectou que um nó foi encerrado. Durante uma reinicialização do nó, você verá um evento NodeDown seguido por um evento NodeUp |  FM | Erro | 
| 18605 | NodeAddedToCluster | StateTransition |  Um novo nó foi adicionado ao cluster e o Service Fabric pode implantar aplicativos para este nó | FM | Informativo | 
| 18606 | NodeRemovedFromCluster | StateTransition |  Um nó foi removido do cluster. O Service Fabric não implantará aplicativos para este nó | FM | Informativo | 
| 18607 | NodeDeactivateStarted | StateTransition |  A desativação de um nó foi iniciada | FM | Informativo | 
| 25621 | NodeOpenSucceeded | StateTransition |  O nó foi iniciado com êxito | FabricNode | Informativo | 
| 25622 | NodeOpenFailed | StateTransition |  Um nó falha ao iniciar e unir o anel | FabricNode | Erro | 
| 25624 | NodeClosed | StateTransition |  Um nó foi desligado com êxito | FabricNode | Informativo | 
| 25626 | NodeAborted | StateTransition |  Um nó foi desligado de maneira brusca | FabricNode | Erro | 

**Eventos de nó do KTL logger** 

| EventId | Nome | Categoria | Descrição |Origem (Tarefa) | Nível |
| --- | --- | --- | --- | --- | --- | 
| 50187 | SharedLogWriteThrottled | Saúde | As gravações no log compartilhado estão sendo limitadas | KtlLoggerNode | Informativo | 
| 50188 | SharedLogWriteUnthrottled | Saúde | As gravações no log compartilhado não foram limitadas | KtlLoggerNode | Informativo | 

## <a name="application-events"></a>Eventos de aplicativo

**Eventos do ciclo de vida do aplicativo**

| EventId | Nome | Categoria | Descrição |Origem (Tarefa) | Nível | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | ApplicationCreated | Ciclo de vida | Um novo aplicativo foi criado | CM | Informativo | 
| 29625 | ApplicationDeleted | Ciclo de vida | Um aplicativo existente foi excluído | CM | Informativo | 
| 23083 | ApplicationProcessExited | Ciclo de vida | Um processo dentro de um aplicativo foi encerrado | Hosting | Informativo | 

**Eventos de atualização do aplicativo**

Mais detalhes sobre as atualizações do aplicativo podem ser encontrados [aqui](service-fabric-application-upgrade.md).

| EventId | Nome | Categoria | Descrição |Origem (Tarefa) | Nível | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | Atualizar | A atualização de um aplicativo foi iniciada | CM | Informativo | 
| 29622 | ApplicationUpgradeCompleted | Atualizar | A atualização de um aplicativo foi concluída | CM | Informativo | 
| 29623 | ApplicationUpgradeRollbackStarted | Atualizar | A reversão de uma atualização de aplicativo foi iniciada |CM | Aviso | 
| 29624 | ApplicationUpgradeRollbackCompleted | Atualizar | A reversão de uma atualização de aplicativo foi concluída | CM | Aviso | 
| 29626 | ApplicationUpgradeDomainCompleted | Atualizar | Um domínio de atualização terminou de atualizar durante uma atualização de aplicativo | CM | Informativo | 

## <a name="service-events"></a>Eventos de serviço

**Eventos de ciclo de vida do serviço**

| EventId | Nome | Categoria | Descrição |Origem (Tarefa) | Nível | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | Ciclo de vida | Um novo serviço foi criado | FM | Informativo | 
| 18658 | ServiceDeleted | Ciclo de vida | Um serviço existente foi excluído | FM | Informativo | 

## <a name="partition-events"></a>Eventos de partição

**Eventos de movimentação de partição**

| EventId | Nome | Categoria | Descrição |Origem (Tarefa) | Nível | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | Ciclo de vida | Uma reconfiguração de partição foi concluída | RA | Informativo | 

## <a name="replica-events"></a>Eventos de réplica

**Eventos de ciclo de vida de réplica**

| EventId | Nome | Categoria | Descrição |Origem (Tarefa) | Nível |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOpened | Ciclo de vida | O dicionário confiável foi aberto | DistributedDictionary | Informativo |
| 61702 | ReliableDictionaryClosed | Ciclo de vida | O dicionário confiável foi fechado | DistributedDictionary | Informativo |
| 61703 | ReliableDictionaryCheckpointRecovered | Ciclo de vida | O dicionário confiável recuperou seu ponto de verificação | DistributedDictionary | Informativo |
| 61704 | ReliableDictionaryCheckpointFilesSent | Ciclo de vida | A réplica enviou arquivos de ponto de verificação de dicionário confiável | DistributedDictionary | Informativo |
| 61705 | ReliableDictionaryCheckpointFilesReceived | Ciclo de vida | A réplica recebeu arquivos de ponto de verificação do dicionário confiável | DistributedDictionary | Informativo |
| 61963 | ReliableQueueOpened | Ciclo de vida | A fila confiável foi aberta | DistributedQueue | Informativo |
| 61964 | ReliableQueueClosed | Ciclo de vida | A fila confiável foi fechada | DistributedQueue | Informativo |
| 61965 | ReliableQueueCheckpointRecovered | Ciclo de vida | A fila confiável recuperou seu ponto de verificação | DistributedQueue | Informativo |
| 61966 | ReliableQueueCheckpointFilesSent | Ciclo de vida | A réplica enviou arquivos de ponto de verificação da fila confiável | DistributedQueue | Informativo |
| 63647 | ReliableQueueCheckpointFilesReceived | Ciclo de vida | A réplica recebeu arquivos de ponto de verificação da fila confiável | DistributedQueue | Informativo |
| 63648 | ReliableConcurrentQueueOpened | Ciclo de vida | A fila simultânea confiável foi aberta | ReliableConcurrentQueue | Informativo |
| 63649 | ReliableConcurrentQueueClosed | Ciclo de vida | A fila simultânea confiável foi fechada | ReliableConcurrentQueue | Informativo |
| 63650 | ReliableConcurrentQueueCheckpointRecovered | Ciclo de vida | A fila simultânea confiável recuperou seu ponto de verificação | ReliableConcurrentQueue | Informativo |
| 61687 | TStoreError | Falha | A coleta confiável recebeu um erro inesperado | TStore | Erro |
| 63831 | PrimaryFullCopyInitiated | Ciclo de vida | A réplica primária iniciou uma cópia completa | TReplicator | Informativo |
| 63832 | PrimaryPartialCopyInitiated | Ciclo de vida | A réplica primária iniciou uma cópia parcial | TReplicator | Informativo |
| 16831 | BuildIdleReplicaStarted | Ciclo de vida | A réplica primária começou a compilar a réplica ociosa | Replicação | Informativo |
| 16832 | BuildIdleReplicaCompleted | Ciclo de vida | A réplica primária concluiu a compilação da réplica ociosa | Replicação | Informativo |
| 16833 | BuildIdleReplicaFailed | Ciclo de vida | A réplica primária falhou ao compilar a réplica ociosa | Replicação | Aviso |
| 16834 | PrimaryReplicationQueueFull | Saúde | A fila de replicação da réplica primária está cheia | Replicação | Aviso |
| 16835 | PrimaryReplicationQueueWarning | Saúde | A fila de replicação da réplica primária está quase cheia | Replicação | Aviso |
| 16836 | PrimaryReplicationQueueWarningMitigated | Saúde | A fila de replicação da réplica primária está ok | Replicação | Informativo |
| 16837 | SecondaryReplicationQueueFull | Saúde | A fila de replicação da réplica secundária está cheia | Replicação | Aviso |
| 16838 | SecondaryReplicationQueueWarning | Saúde | A fila de replicação da réplica secundária está quase cheia | Replicação | Aviso |
| 16839 | SecondaryReplicationQueueWarningMitigated | Saúde | A fila de replicação da réplica secundária está ok | Replicação | Informativo |
| 16840 | PrimaryFaultedSlowSecondary | Saúde | A réplica primária falhou em uma réplica secundária lenta | Replicação | Aviso |
| 16841 | ReplicatorFaulted | Saúde | Falha na réplica | Replicação | Aviso |

## <a name="container-events"></a>Eventos de contêiner

**Eventos de ciclo de vida do contêiner** 

| EventId | Nome | Descrição |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | Um contêiner for iniciado | Hosting | Informativo | 1 |
| 23075 | ContainerDeactivated | Um contêiner for encerrado | Hosting | Informativo | 1 |
| 23082 | ContainerExited | Um contêiner foi fechado – verifique o sinalizador UnexpectedTermination | Hosting | Informativo | 1 |

## <a name="health-reports"></a>Relatórios de integridade

O [Modelo de Integridade do Service Fabric](service-fabric-health-introduction.md) introduz uma avaliação e relatório de integridade rico, flexível e extensível. A partir do Service Fabric versão 6.2, os dados de integridade são gravados como eventos de plataforma para fornecer registros históricos de integridade. Para reduzir o volume de eventos de integridade, podemos gravar apenas os seguintes eventos do Service Fabric:

* Todos `Error` ou `Warning` relatórios de integridade
* `Ok` relatórios de integridade durante as transições
* Quando um `Error` ou `Warning` evento de integridade expira. Isso pode ser usado para determinar quanto tempo uma entidade não foi íntegra

**Eventos de relatório de integridade do cluster**

| EventId | Nome | Descrição |Origem (Tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | Um novo relatório de integridade do cluster está disponível | HM | Informativo | 1 |
| 54437 | ClusterHealthReportExpired | Um relatório de integridade de cluster existente expirou | HM | Informativo | 1 |

**Eventos de relatório de integridade do nó**

| EventId | Nome | Descrição |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | Um novo relatório de integridade do nó está disponível | HM | Informativo | 1 |
| 54432 | NodeHealthReportExpired | Um relatório de integridade do nó existente expirou | HM | Informativo | 1 |

**Eventos de relatório de integridade do aplicativo**

| EventId | Nome | Descrição |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | Um novo relatório de integridade do aplicativo foi criado. É referente a aplicativos não implantados. | HM | Informativo | 1 |
| 54426 | DeployedApplicationNewHealthReport | Um novo relatório de integridade do aplicativo implantado foi criado | HM | Informativo | 1 |
| 54427 | DeployedServicePackageNewHealthReport | Um novo relatório de integridade do serviço implantado foi criado | HM | Informativo | 1 |
| 54434 | ApplicationHealthReportExpired | Um relatório de integridade do aplicativo existente expirou | HM | Informativo | 1 |
| 54435 | DeployedApplicationHealthReportExpired | Um relatório de integridade do aplicativo implantado existente expirou | HM | Informativo | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | Um relatório de integridade do serviço implantado existente expirou | HM | Informativo | 1 |

**Eventos de relatório de integridade do serviço**

| EventId | Nome | Descrição |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Um novo relatório de integridade do serviço foi criado | HM | Informativo | 1 |
| 54433 | ServiceHealthReportExpired | Um relatório de integridade do serviço existente expirou | HM | Informativo | 1 |

**Eventos de relatório de integridade da partição**

| EventId | Nome | Descrição |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Um novo relatório de integridade da partição foi criado | HM | Informativo | 1 |
| 54431 | PartitionHealthReportExpired | Um relatório de integridade da partição existente expirou | HM | Informativo | 1 |

**Eventos de relatório de integridade da réplica**

| EventId | Nome | Descrição |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Um relatório de integridade da réplica com estado foi criado | HM | Informativo | 1 |
| 54430 | StatelessInstanceNewHealthReport | Um novo relatório de integridade da réplica sem estado foi criado | HM | Informativo | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Um relatório de integridade de réplica com estado existente expirou | HM | Informativo | 1 |
| 54439 | StatelessInstanceHealthReportExpired | Um relatório de integridade de instância sem estado existente expirou | HM | Informativo | 1 |

## <a name="chaos-testing-events"></a>Eventos de teste do caos 

**Eventos de sessão de caos**

| EventId | Nome | Descrição |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | Uma sessão de teste do caos foi iniciada | Capacidade de teste | Informativo | 1 |
| 50023 | ChaosStopped | Uma sessão de teste do caos foi interrompida | Capacidade de teste | Informativo | 1 |

**Eventos de nó de caos**

| EventId | Nome | Descrição |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | Um nó foi agendado para ser reiniciado como parte de uma sessão de teste do caos | Capacidade de teste | Informativo | 1 |
| 50087 | ChaosNodeRestartCompleted | A reinicialização de um nó foi concluída como parte de uma sessão de teste do caos | Capacidade de teste | Informativo | 1 |

**Eventos de aplicativo de caos**

| EventId | Nome | Descrição |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | Uma reinicialização do pacote de códigos foi agendada durante um sessão de teste do caos | Capacidade de teste | Informativo | 1 |
| 50101 | ChaosCodePackageRestartCompleted | Uma reinicialização do pacote de códigos foi concluída durante um sessão de teste do caos | Capacidade de teste | Informativo | 1 |

**Eventos de partição de caos**

| EventId | Nome | Descrição |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | Uma partição primária foi agendada para ser movida como parte de uma sessão de teste do caos | Capacidade de teste | Informativo | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | Uma partição secundária foi agendada para ser movida como parte de uma sessão de teste do caos | Capacidade de teste | Informativo | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | Uma análise mais profunda do movimenta da partição primária está disponível | Capacidade de teste | Informativo | 1 |

**Eventos de réplica de caos**

| EventId | Nome | Descrição |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | Uma reinicialização de réplica foi agendada como parte de uma sessão de teste do caos | Capacidade de teste | Informativo | 1 |
| 50051 | ChaosReplicaRemovalScheduled | Uma remoção de réplica foi agendada como parte de uma sessão de teste do caos | Capacidade de teste | Informativo | 1 |
| 50093 | ChaosReplicaRemovalCompleted | Uma remoção de réplica foi concluída como parte de uma sessão de teste do caos | Capacidade de teste | Informativo | 1 |

## <a name="other-events"></a>Outros eventos

**Eventos de correlação**

| EventId | Nome | Descrição |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | Uma correlação foi detectada | Capacidade de teste | Informativo | 1 |

## <a name="events-prior-to-version-62"></a>Eventos anteriores à versão 6.2

Esta é uma lista abrangente dos eventos fornecidos pelo Service Fabric antes da versão 6.2.

| EventId | Nome | Origem (Tarefa) | Nível |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Informativo |
| 25621 | NodeOpenedSuccess | FabricNode | Informativo |
| 25622 | NodeOpenedFailed | FabricNode | Informativo |
| 25623 | NodeClosing | FabricNode | Informativo |
| 25624 | NodeClosed | FabricNode | Informativo |
| 25625 | NodeAborting | FabricNode | Informativo |
| 25626 | NodeAborted | FabricNode | Informativo |
| 29627 | ClusterUpgradeStart | CM | Informativo |
| 29628 | ClusterUpgradeComplete | CM | Informativo |
| 29629 | ClusterUpgradeRollback | CM | Informativo |
| 29630 | ClusterUpgradeRollbackComplete | CM | Informativo |
| 29631 | ClusterUpgradeDomainComplete | CM | Informativo |
| 23074 | ContainerActivated | Hosting | Informativo |
| 23075 | ContainerDeactivated | Hosting | Informativo |
| 29620 | ApplicationCreated | CM | Informativo |
| 29621 | ApplicationUpgradeStart | CM | Informativo |
| 29622 | ApplicationUpgradeComplete | CM | Informativo |
| 29623 | ApplicationUpgradeRollback | CM | Informativo |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Informativo |
| 29625 | ApplicationDeleted | CM | Informativo |
| 29626 | ApplicationUpgradeDomainComplete | CM | Informativo |
| 18566 | ServiceCreated | FM | Informativo |
| 18567 | ServiceDeleted | FM | Informativo |

## <a name="next-steps"></a>Próximas etapas

* Obtenha uma visão geral de [diagnóstico no Service Fabric](service-fabric-diagnostics-overview.md)
* Saiba mais sobre o EventStore na [Visão geral de Eventstore do Service Fabric](service-fabric-diagnostics-eventstore.md)
* Modificando a configuração do [Diagnóstico do Azure](service-fabric-diagnostics-event-aggregation-wad.md) para coletar mais logs
* [Configurando o Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) para ver os logs do Canal operacional
