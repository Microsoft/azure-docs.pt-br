---
title: Reconfiguração no Azure Service Fabric
description: Saiba mais sobre as configurações de réplicas de serviço com estado e o processo de reconfiguração Service Fabric usa para manter a consistência e a disponibilidade durante a alteração.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: bd46a7776495624affef77a44fcf68334750ba17
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "75609988"
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Reconfiguração no Azure Service Fabric
Uma *configuração* é definida como as réplicas e suas funções para uma partição de um serviço com estado.

Uma *reconfiguração* é o processo de mover uma configuração para outra configuração. Ela faz uma alteração no conjunto de réplicas para uma partição de um serviço com estado. A configuração antiga é chamada de *PC (configuração anterior)*, e a nova configuração é chamada de *configuração atual (CC)*. O protocolo de reconfiguração no Azure Service Fabric preserva a consistência e mantém a disponibilidade durante alterações no conjunto de réplicas.

O Gerenciador de Failover inicia as reconfigurações em resposta a diferentes eventos no sistema. Por exemplo, se a principal falhar, uma reconfiguração será iniciada para promover uma secundária ativa para primária. Outro exemplo é em resposta às atualizações de aplicativo quando talvez seja necessário mover a primária para outro nó a fim de atualizá-lo.

## <a name="reconfiguration-types"></a>Tipos de reconfiguração
As reconfigurações podem ser classificadas em dois tipos:

- Reconfigurações em que a primária está sendo alterada:
    - **Failover**: failovers são reconfigurações em resposta à falha de um primário em execução.
    - **SwapPrimary**: permutas são reconfigurações em que o Service Fabric precisa mover uma primária em execução de um nó para outro, geralmente em resposta ao balanceamento de carga ou uma atualização.

- Reconfigurações em que a primária não está sendo alterada.

## <a name="reconfiguration-phases"></a>Fases da reconfiguração
Uma reconfiguração ocorre em várias fases:

- **Phase0**: esta fase ocorre em reconfigurações de permuta primária em que a primária atual transfere seu estado para a nova primária e faz a transição para a secundária ativa.

- **Phase1**: esta fase ocorre durante as reconfigurações em que a primária está sendo alterada. Durante esta fase, o Service Fabric identifica a primária correta entre as réplicas atuais. Essa fase não é necessária durante reconfigurações de permuta primária, porque a nova primária já foi escolhida. 

- A **Fase2**: durante essa fase, Service Fabric garante que todos os dados estejam disponíveis na maioria das réplicas da configuração atual.

Há várias outras fases que são somente para uso interno.

## <a name="stuck-reconfigurations"></a>Reconfigurações paralisadas
As reconfigurações podem ficar *paralisadas* por diversos motivos. Alguns dos principais motivos incluem:

- **Réplicas inativas**: algumas fases de reconfiguração exigem a maioria das réplicas na configuração a ser ativada.
- **Problemas de rede ou de comunicação**: as reconfigurações exigem conectividade de rede entre diferentes nós.
- **Falhas de API**: o protocolo de reconfiguração requer que as implementações de serviço concluam determinadas APIs. Por exemplo, não respeitar o token de cancelamento em um serviço confiável faz as reconfigurações SwapPrimary ficarem paralisadas.

Use relatórios de integridade de componentes do sistema, como System.FM, System.RA e System.RAP, para diagnosticar onde uma reconfiguração está paralisada. A [página do relatório de integridade do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) descreve esses relatórios de integridade.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os conceitos do Service Fabric, confira os seguintes artigos:

- [Ciclo de vida dos Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)
- [Relatórios de integridade do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Réplicas e instâncias](service-fabric-concepts-replica-lifecycle.md)
