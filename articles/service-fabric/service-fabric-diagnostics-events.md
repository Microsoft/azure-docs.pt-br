---
title: Eventos de Service Fabric do Azure
description: Saiba mais sobre os eventos do Service Fabric fornecidos para ajudá-lo a monitorar o cluster do Azure Service Fabric.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 581bb3f16941fce660e0f464ed8ea9389b034b12
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628794"
---
# <a name="service-fabric-events"></a>Eventos do Service Fabric 

A plataforma do Service Fabric grava vários eventos estruturados para atividades operacionais chave acontecendo dentro do seu cluster. Esses variam de atualizações de cluster a decisões de posicionamento de réplica. Cada evento do Service Fabric expõe mapas para uma das seguintes entidades no cluster:
* Cluster
* Aplicativo
* Serviço
* Partition
* Réplica 
* Contêiner

Para ver uma lista completa dos eventos expostos por plataforma - [Listar eventos do Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

Aqui estão alguns exemplos de cenários para os quais você deve ver eventos em seu cluster. 
* Eventos de ciclo de vida de nó: conforme os nós sobem, descem, dimensionam, reiniciam e são ativados/desativados, esses eventos serão expostos mostrando o que aconteceu e ajuda você a identificar se há algo errado com o computador em si ou se havia uma API que foi chamada por meio do SF para modificar o status de um nó.
* Atualização de cluster: conforme o cluster for atualizado (versão SF ou alteração de configuração), você verá a atualização iniciar, reverter cada um dos seus Upgrade Domains e completo (ou reverter). 
* Atualizações de aplicativo: da mesma forma que atualizações de cluster, há um conjunto abrangente de eventos, conforme a atualização é passada. Esses eventos podem ser úteis para entender quando uma atualização foi agendada, o estado atual de uma atualização e a sequência geral de eventos. Isso é útil para pesquisar novamente para ver quais atualizações foram revertidas com êxito ou se a reversão foi acionada.
* Exclusão do Aplicativo/Implantação de serviço: não há eventos para cada aplicativo, serviço e contêiner, sendo criado ou excluído e útil ao escalar dentro ou fora, por exemplo, aumentar o número de réplicas
* Movimentos de partição (reconfiguração): sempre que uma partição com estado passa por uma reconfiguração (uma alteração no conjunto de réplicas), um evento é registrado. Isso é útil se você estiver tentando entender com que frequência seu conjunto de réplicas de partição está sendo alterado ou fazer failover, ou rastrear qual nó estava executando sua réplica primária a qualquer ponto no tempo.
* Eventos de caos: ao usar o serviço [Caos](service-fabric-controlled-chaos.md) do Service Fabric, você verá eventos toda vez que o serviço for iniciado ou interrompido, ou quando ele injetar uma falha no sistema.
* Eventos de integridade: o Service Fabric expõe eventos de integridade sempre que um aviso ou um relatório de integridade de erro é criado, ou uma entidade volta para um estado de integridade OK ou um relatório de integridade expira. Esses eventos são muito úteis para rastrear as estatísticas de integridade históricas para uma entidade. 

## <a name="how-to-access-events"></a>Como acessar os eventos

Há algumas maneiras diferentes pelas quais os eventos do Service Fabric podem ser acessados:
* Os eventos são registrados por meio de canais padrão, como logs de eventos ETW/Windows e podem ser visualizados por qualquer ferramenta de monitoramento que ofereça suporte a esses logs de Azure Monitor. Por padrão, os clusters criados no portal têm o diagnóstico ativado e têm o agente de diagnóstico do Windows Azure enviando os eventos para o armazenamento de tabelas do Azure, mas você ainda precisa integrá-lo com o recurso do log Analytics. Leia mais sobre como configurar o [agente de diagnóstico do Azure](service-fabric-diagnostics-event-aggregation-wad.md) para modificar a configuração de diagnóstico do cluster para selecionar mais logs ou contadores de desempenho e a integração de logs de [Azure monitor](service-fabric-diagnostics-event-analysis-oms.md)
* APIs de Rest do serviço EventStore que permitem que você consulte o cluster diretamente ou por meio da biblioteca de clientes do Service Fabric. Consulte [Consultar APIs do EventStore para eventos de cluster](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Próximas etapas
* Obtenha mais informações sobre o monitoramento de seu cluster - [Monitoramento do cluster e da plataforma](service-fabric-diagnostics-event-generation-infra.md).
* Saiba mais sobre o serviço EventStore - [Visão geral do serviço EventStore](service-fabric-diagnostics-eventstore.md)
