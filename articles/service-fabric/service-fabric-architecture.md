---
title: Arquitetura do Service Fabric do Azure | Microsoft Docs
description: "A Malha do Serviço é uma plataforma de sistemas distribuídos usada para criar aplicativos escalonáveis, confiáveis e facilmente gerenciados para a nuvem. Este artigo mostra a arquitetura da malha de serviço."
services: service-fabric
documentationcenter: .net
author: rishirsinha
manager: timlt
editor: rishirsinha
ms.assetid: 6b554243-70cb-4c22-9b28-1a8b4703f45e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2017
ms.author: rsinha
ms.openlocfilehash: 3cf019bf9a08574c984e92985452df438f3eeb77
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-architecture"></a>Arquitetura do Service Fabric
O Service Fabric é criado com subsistemas em camadas. Esses subsistemas permitem escrever aplicativos:

* Altamente disponível
* Escalonável
* Gerenciáveis
* Testável

A figura a seguir mostra os subsistemas principais do Service Fabric.

![Diagrama da arquitetura do Service Fabric](media/service-fabric-architecture/service-fabric-architecture.png)

Em um sistema distribuído, a capacidade de estabelecer uma comunicação segura entre os nós em um cluster é crucial. Na base da pilha está o subsistema de transporte, que fornece comunicação segura entre os nós. Acima do subsistema de transporte, encontra-se o subsistema de federação, que agrupa os diferentes nós em uma única entidade (clusters nomeados) para que o Service Fabric possa detectar falhas, executar eleição de líder e permitir o roteamento de forma consistente. O subsistema de confiabilidade, na camada mais alta do subsistema de federação, é responsável pela confiabilidade dos serviços do Service Fabric por meio de mecanismos como replicação, gerenciamento de recursos e failover. O subsistema de federação também serve como base para o subsistema de hospedagem e de ativação, que gerencia o ciclo de vida de um aplicativo em um único nó. O subsistema de gerenciamento gerencia o ciclo de vida de aplicativos e de serviços. O subsistema de capacidade de teste ajuda os desenvolvedores de aplicativos a testar seus serviços por meio de falhas simuladas antes e depois da implantação de aplicativos e de serviços em ambientes de produção. O Service Fabric oferece a capacidade de resolver locais de serviço por meio de seu subsistema de comunicação. Os modelos de programação de aplicativo apresentados aos desenvolvedores estão em camadas sobre esses subsistemas, junto com o modelo de aplicativo para habilitar as ferramentas.

## <a name="transport-subsystem"></a>Subsistema de transporte
O subsistema de transporte implementa um canal de comunicação de datagrama ponto a ponto. Esse canal é usado para a comunicação dentro de clusters do Service Fabric e para a comunicação entre clientes e o cluster do Service Fabric. Ele dá suporte a padrão de comunicação unidirecional e de solicitação-resposta, que oferece a base para a implementação de difusão e de multicast na camada de Federação. O subsistema de transporte protege a comunicação usando certificados X509 ou segurança do Windows. Esse subsistema é usado internamente pelo Service Fabric e não pode ser acessado diretamente pelos desenvolvedores para a programação de aplicativo.

## <a name="federation-subsystem"></a>Subsistema de federação
Para justificar um conjunto de nós em um sistema distribuído, você precisa ter uma visão consistente do sistema. O subsistema de federação usa os primitivos da comunicação fornecidos pelo subsistema de transporte e junta vários nós em um único cluster unificado que ele pode justificar. Ele fornece os primitivos de sistemas distribuídos necessários para outros subsistemas - detecção de falhas, eleição de líder e roteamento consistente. O subsistema de federação é criado sobre tabelas de hash distribuídas com um espaço de token de 128 bits. O subsistema cria uma topologia de anel sobre os nós, com cada nó no anel sendo alocado a um subconjunto do espaço de token para a propriedade. Para detecção de falha, a camada usa um mecanismo de concessão baseado em heartbeat e arbitragem. O subsistema de federação também garante através de uma junção complexa e protocolos de saída que somente um único proprietário de um token exista a qualquer momento. Isso oferece garantias de eleição de líder e de roteamento consistente.

## <a name="reliability-subsystem"></a>Subsistema de confiabilidade
O subsistema de confiabilidade fornece o mecanismo para manter o estado de um serviço do Service Fabric altamente disponível por meio do uso do *Replicator*, do *Gerenciador de Failover* e do *Balanceador de Recurso*.

* O Replicador garante que as alterações de estado da réplica principal serviço automaticamente serão replicadas para réplicas secundárias, mantendo a consistência entre as réplicas primárias e secundárias em um conjunto de réplicas do serviço. O replicador é responsável pelo gerenciamento de quorum entre as réplicas no conjunto de réplicas. Ele interage com a unidade de failover para obter a lista de operações a replicar, e o agente de reconfiguração fornece-a com a configuração do conjunto de réplicas. Essa configuração indica em quais réplicas as operações devem ser replicadas. O Service Fabric fornece um replicador padrão chamado Fabric Replicator, que pode ser usado pela API de modelo de programação para tornar o estado do serviço altamente disponível e confiável.
* O Gerenciador de Failover garante que quando os nós forem adicionados ou removidos do cluster, a carga será automaticamente redistribuída entre os nós disponíveis. Se um nó do cluster falhar, o cluster automaticamente reconfigurará as réplicas de serviço para manter a disponibilidade.
* O Gerenciador de Recursos coloca o domínio de falha entre réplicas de serviço no cluster e garante que todas as unidades de failover estejam operacionais. O Gerenciador de Recursos também faz o balanceamento de recursos de serviço no pool compartilhado subjacente de nós de cluster para atingir a distribuição de carga uniforme ideal.

## <a name="management-subsystem"></a>Subsistema de gerenciamento
O subsistema de gerenciamento oferece serviços de ponta a ponta e gerenciamento de ciclo de vida do aplicativo. Os cmdlets do PowerShell e as APIs administrativas permitem provisionar, implantar, corrigir, atualizar e desprovisionar aplicativos sem perda de disponibilidade. O subsistema de gerenciamento faz isso por meio dos serviços a seguir.

* **Gerenciador de Cluster**: esse é o principal serviço que interage com o Gerenciador de Failover de confiabilidade para posicionar os aplicativos nos nós com base nas restrições de posicionamento de serviço. O Gerenciador de Recursos no subsistema de failover faz com que as restrições nunca sejam desfeitas. O gerenciador de cluster gerencia o ciclo de vida dos aplicativos de provisionamento para desprovisionamento. Ele se integra ao gerenciador de integridade para garantir que a disponibilidade de aplicativos não seja perdida de uma perspectiva de semântica de integridade durante as atualizações.
* **Gerenciador de Integridade**: esse serviço permite o monitoramento de integridade de aplicativos, serviços e entidades de cluster. As entidades de cluster (como os nós, as partições de serviço e as réplicas) podem relatar informações de integridade, que são então agregadas a um repositório centralizado de integridade. Essas informações de integridade fornecem um instantâneo da integridade geral de um ponto no tempo dos serviços e nós distribuídas em vários nós no cluster, permitindo que você tome as ações corretivas necessárias. As APIs de consulta de integridade permitem consultar os eventos de integridade relatados ao subsistema de integridade. As APIs de consulta de integridade retornam os dados brutos de integridade armazenados no repositório de integridade ou os dados de integridade agregados e interpretados de uma entidade de cluster específica.
* **Repositório de Imagens**: esse serviço permite o armazenamento e a distribuição dos binários do aplicativo. O serviço fornece armazenamento de arquivos distribuídos simples, onde os aplicativos são carregados e de onde são baixados.

## <a name="hosting-subsystem"></a>Subsistema de hospedagem
O gerenciador de cluster informa o subsistema de hospedagem (em execução em cada nó), os serviços que precisa para gerenciar um nó específico. O subsistema de hospedagem, em seguida, gerencia o ciclo de vida do aplicativo nesse nó. Ele interage com os componentes de confiabilidade e integridade para garantir que as réplicas estão posicionadas corretamente e estão íntegras.

## <a name="communication-subsystem"></a>Subsistema de comunicação
Esse subsistema fornece um sistema de mensagens confiável dentro do cluster e descoberta de serviço por meio do serviço de nomenclatura. O serviço de nomenclatura resolve nomes de serviço para um local no cluster e permite aos usuários gerenciar propriedades e nomes de serviço. Usando o serviço de nomenclatura, os clientes podem comunicar-se com segurança com qualquer nó no cluster para resolver um nome de serviço e recuperar metadados de serviço. Usando uma API de cliente de nomenclatura simples de nomenclatura, os usuários do Service Fabric podem desenvolver serviços e clientes capazes de resolver o local de rede atual, independentemente do dinamismo do nó ou do redimensionamento do cluster.

## <a name="testability-subsystem"></a>Subsistema de capacidade de teste
Capacidade de teste é um conjunto de ferramentas desenvolvidas especificamente para testar serviços criados com base na malha de serviço. As ferramentas permitem que um desenvolvedor induza com facilidade falhas significativas e execute cenários de teste para praticar e validar os vários estados e transições pelos quais o serviço passa durante o ciclo de vida, tudo de uma maneira segura e controlada. A capacidade de teste também fornece um mecanismo para executar testes mais longos que podem iterar por várias possíveis falhas sem perder a disponibilidade. Isso fornece a você um ambiente de teste em produção.

