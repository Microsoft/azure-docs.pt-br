---
title: "Reconfiguração no Azure Service Fabric | Microsoft Docs"
description: "Entender a reconfiguração de partições no Service Fabric"
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: 
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2017
ms.author: aprameyr
ms.openlocfilehash: ee2808286012720210070817d7ee6c2349247858
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Reconfiguração no Azure Service Fabric
Uma *configuração* é definida como as réplicas e suas funções para uma partição de um serviço com estado.

Uma *reconfiguração* é o processo de mover uma configuração para outra configuração. Ela faz uma alteração no conjunto de réplicas para uma partição de um serviço com estado. A configuração antiga é chamada de *PC (configuração anterior)* e a nova configuração é chamada de *CC (configuração atual)*. O protocolo de reconfiguração no Azure Service Fabric preserva a consistência e mantém a disponibilidade durante alterações no conjunto de réplicas.

O Gerenciador de Failover inicia as reconfigurações em resposta a diferentes eventos no sistema. Por exemplo, se a principal falhar, uma reconfiguração será iniciada para promover uma secundária ativa para primária. Outro exemplo é em resposta às atualizações de aplicativo quando talvez seja necessário mover a primária para outro nó a fim de atualizá-lo.

## <a name="reconfiguration-types"></a>Tipos de reconfiguração
As reconfigurações podem ser classificadas em dois tipos:

- Reconfigurações em que a primária está sendo alterada:
    - **Failover**: failovers são reconfigurações em resposta à falha de uma primária em execução.
    - **SwapPrimary**: permutas são reconfigurações em que o Service Fabric precisa mover uma primária em execução de um nó para outro, geralmente em resposta ao balanceamento de carga ou uma atualização.

- Reconfigurações em que a primária não está sendo alterada.

## <a name="reconfiguration-phases"></a>Fases da reconfiguração
Uma reconfiguração ocorre em várias fases:

- **Phase0**: esta fase ocorre em reconfigurações de permuta primária em que a primária atual transfere seu estado para a nova primária e faz a transição para a secundária ativa.

- **Phase1**: esta fase ocorre durante as reconfigurações em que a primária está sendo alterada. Durante esta fase, o Service Fabric identifica a primária correta entre as réplicas atuais. Essa fase não é necessária durante reconfigurações de permuta primária, porque a nova primária já foi escolhida. 

- **Phase2**: durante esta fase, o Service Fabric garante que todos os dados estejam disponíveis na maioria das réplicas da configuração atual.

Há várias outras fases que são somente para uso interno.

## <a name="stuck-reconfigurations"></a>Reconfigurações paralisadas
As reconfigurações podem ficar *paralisadas* por diversos motivos. Alguns dos principais motivos incluem:

- **Réplicas inoperantes**: algumas fases de reconfiguração exigem que a maioria das réplicas na configuração esteja operante.
- **Problemas de rede ou de comunicação**: as reconfigurações exigem conectividade de rede entre diferentes nós.
- **Falhas de API**: o protocolo de reconfiguração requer que as implementações de serviço concluam determinadas APIs. Por exemplo, não respeitar o token de cancelamento em um serviço confiável faz as reconfigurações SwapPrimary ficarem paralisadas.

Use relatórios de integridade de componentes do sistema, como System.FM, System.RA e System.RAP, para diagnosticar onde uma reconfiguração está paralisada. A [página do relatório de integridade do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) descreve esses relatórios de integridade.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os conceitos do Service Fabric, confira os seguintes artigos:

- [Ciclo de vida dos Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)
- [Relatórios de integridade do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Instâncias e réplicas](service-fabric-concepts-replica-lifecycle.md)
