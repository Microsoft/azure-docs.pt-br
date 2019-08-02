---
title: EventStore do Azure Service Fabric | Microsoft Docs
description: Saiba mais sobre o EventStore do Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/6/2019
ms.author: srrengar
ms.openlocfilehash: e7ae4c77f958bacabea50b7193817cd41ea54aa9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449776"
---
# <a name="eventstore-overview"></a>Visão geral de EventStore

>[!NOTE]
>A partir do Service Fabric 6.4. as APIs do EventStore estão apenas disponíveis para clusters do Windows somente na execução no Azure. Estamos trabalhando mover essa funcionalidade para Linux, assim como para nossos clusters independentes.

## <a name="overview"></a>Visão Geral

O serviço de EventStore introduzido na versão 6.2, é uma opção de monitoramento no Service Fabric. O EventStore fornece uma maneira de entender o estado do cluster ou cargas de trabalho em um determinado ponto no tempo.
O EventStore é um serviço do Service Fabric com monitoramento de estado que mantém os eventos do cluster. Os eventos são expostos por meio do Service Fabric Explorer, REST e APIs. As consultas do EventStore permitem que você consulte o cluster diretamente para obter dados de diagnóstico em qualquer entidade no cluster e devem ser usadas para ajudar a:

* Diagnosticar problemas em desenvolvimento ou teste, ou em que talvez você possa estar usando um pipeline de monitoramento
* Confirmar que as ações de gerenciamento que você está realizando no cluster estão sendo processadas corretamente por ele
* Obter um "instantâneo" de como o Service Fabric está interagindo com uma entidade específica

![EventStore](media/service-fabric-diagnostics-eventstore/eventstore.png)

Para ver uma lista completa de eventos disponíveis no EventStore, consulte [Eventos do Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

>[!NOTE]
>A partir do Service Fabric 6.4. as APIs de EventStore e a experiência do usuário geralmente estão disponíveis para clusters do Windows Azure. Estamos trabalhando mover essa funcionalidade para Linux, assim como para nossos clusters independentes.

O serviço EventStore pode ser consultado para eventos que estão disponíveis para cada entidade e tipo de entidade em seu cluster. Isso significa que você pode consultar eventos nos seguintes níveis:
* Cluster: eventos específicos para o cluster em si (por exemplo, a atualização de cluster)
* Nós: eventos todos os eventos no nível do nó
* Nó: eventos específicos a um nó, com base em `nodeName`
* Aplicativos: todos os eventos no nível do aplicativo
* Aplicativo: eventos específicos para um aplicativo identificado por `applicationId`
* Serviços: eventos de todos os serviços em seus clusters
* Serviço: eventos de um serviço específico identificado por `serviceId`
* Partições: eventos de todas as partições
* Partição: eventos de uma partição específica identificada por `partitionId`
* Réplicas de partição: eventos de todas as réplicas / instâncias em uma partição específica identificada por `partitionId`
* Réplica de partição: eventos de uma réplica específica / instância identificada por `replicaId` e `partitionId`

Para saber mais sobre a API, confira a [referência de API do EventStore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore).

O serviço EventStore também tem a capacidade de correlacionar eventos em seu cluster. Examinando os eventos que foram gravados ao mesmo tempo de diferentes entidades que podem ter se afetado entre si, o serviço EventStore é capaz de vincular esses eventos para ajudar a identificar as causas de atividades em seu cluster. Por exemplo, caso um de seus aplicativos se tornar não íntegro sem quaisquer alterações induzidas, o EventStore também examinará outros eventos expostos pela plataforma e poderá correlacionar isso a um evento `Error` ou `Warning`. Isso ajuda com detecção de falha e análise de causas raiz mais rápidas.

## <a name="enable-eventstore-on-your-cluster"></a>Habilitar EventStore em seu cluster

### <a name="local-cluster"></a>Cluster Local

No [fabricSettings.json em seu cluster](service-fabric-cluster-fabric-settings.md), adicione EventStoreService como um recurso de complemento e execute uma atualização de cluster.

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster-version-65"></a>Cluster do Azure versão 6.5 +
Se seu cluster do Azure é atualizado para a versão 6.5 ou superior, EventStore será habilitada automaticamente em seu cluster. Para recusar, você precisará atualizar o modelo de cluster com o seguinte:

* Usar uma versão de API do `2019-03-01` ou mais recente 
* Adicione o seguinte código à seção de propriedades em seu cluster
  ```json  
    "fabricSettings": [
      …
    ],
    "eventStoreServiceEnabled": false
  ```

### <a name="azure-cluster-version-64"></a>Cluster do Azure versão 6.4

Se você estiver usando a versão 6.4, você pode editar seu modelo do Azure Resource Manager para ativar o serviço de EventStore. Isso é feito pela execução de um [atualização de configuração de cluster](service-fabric-cluster-config-upgrade-azure.md) e adicionar o código a seguir, você pode usar PlacementConstraints para colocar as réplicas do serviço de EventStore em um NodeType específico, por exemplo, um NodeType dedicado para os serviços do sistema . A `upgradeDescription` seção configura a atualização de configuração para disparar uma reinicialização em nós. Você pode remover a seção em outra atualização.

```json
    "fabricSettings": [
          …
          …
          …,
         {
            "name": "EventStoreService",
            "parameters": [
              {
                "name": "TargetReplicaSetSize",
                "value": "3"
              },
              {
                "name": "MinReplicaSetSize",
                "value": "1"
              },
              {
                "name": "PlacementConstraints",
                "value": "(NodeType==<node_type_name_here>)"
              }
            ]
          }
        ],
        "upgradeDescription": {
          "forceRestart": true,
          "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807",
          "healthCheckWaitDuration": "00:01:00",
          "healthCheckStableDuration": "00:01:00",
          "healthCheckRetryTimeout": "00:5:00",
          "upgradeTimeout": "1:00:00",
          "upgradeDomainTimeout": "00:10:00",
          "healthPolicy": {
            "maxPercentUnhealthyNodes": 100,
            "maxPercentUnhealthyApplications": 100
          },
          "deltaHealthPolicy": {
            "maxPercentDeltaUnhealthyNodes": 0,
            "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0,
            "maxPercentDeltaUnhealthyApplications": 0
          }
        }
```


## <a name="next-steps"></a>Próximas etapas
* Introdução à API de EventStore - [Usando as APIs de EventStore em clusters do Service Fabric](service-fabric-diagnostics-eventstore-query.md)
* Saiba mais sobre a lista de eventos oferecidos pelo EventStore - [Eventos do Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* Visão geral do monitoramento e diagnóstico no Service Fabric - [Monitoramento e Diagnóstico para o Service Fabric](service-fabric-diagnostics-overview.md)
* Exibir a lista completa das chamadas à API - [Referência de API REST do EventStore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore)
* Saiba mais sobre o monitoramento de seu cluster - [Monitoramento do cluster e da plataforma](service-fabric-diagnostics-event-generation-infra.md).
