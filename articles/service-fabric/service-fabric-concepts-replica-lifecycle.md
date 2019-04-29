---
title: Réplicas e instâncias no Azure Service Fabric | Microsoft Docs
description: Entender réplicas e instâncias – sua função e ciclos de vida
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: ''
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: 7f8638365b40395a5dd82457c40e5c15209ba1a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60882365"
---
# <a name="replicas-and-instances"></a>Réplicas e instâncias 
Este artigo fornece uma visão geral do ciclo de vida das réplicas de serviços com estado e instâncias de serviços sem monitoração de estado.

## <a name="instances-of-stateless-services"></a>Instâncias de serviços sem monitoração de estado
Uma instância de um serviço sem estado é uma cópia da lógica de serviço em execução em um dos nós do cluster. Uma instância dentro de uma partição é identificada exclusivamente pelo seu **InstanceId**. O ciclo de vida de uma instância é modelado no diagrama a seguir:

![Ciclo de vida da instância](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Depois que o Gerenciador de Recursos de Cluster determinar um posicionamento para a instância, ele entrará nesse estado de ciclo de vida. A instância é iniciada no nó. O host de aplicativo é iniciado, a instância é criada e, em seguida, aberta. Depois que a inicialização for concluída, a instância fará a transição para o estado pronto. 

Se o nó ou o host de aplicativo para essa instância falhar, ele fará a transição para o estado removido.

### <a name="ready-rd"></a>Pronta (RD)
No estado pronto, a instância entra em funcionamento no nó. Se essa instância for um serviço confiável, o **RunAsync** terá sido chamado. 

Se o nó ou o host de aplicativo para essa instância falhar, ele fará a transição para o estado removido.

### <a name="closing-cl"></a>Fechamento (CL)
No estado de fechamento, o Azure Service Fabric está no processo de desligamento da instância neste nó. Esse desligamento pode acontecer devido a muitos motivos – por exemplo, uma atualização de aplicativo, balanceamento de carga ou o serviço que está sendo excluído. Após a conclusão do desligamento, ele fará a transição para o estado removido.

### <a name="dropped-dd"></a>Removido (DD)
No estado removido, a instância não está mais em execução no nó. Neste ponto, o Service Fabric mantém os metadados sobre essa instância, que é excluída definitivamente também.

> [!NOTE]
> É possível fazer a transição de qualquer estado para o estado removido usando a opção **ForceRemove** em `Remove-ServiceFabricReplica`.
>

## <a name="replicas-of-stateful-services"></a>Réplicas de serviços com estado
Uma réplica de um serviço com estado é uma cópia da lógica de serviço em execução em um dos nós do cluster. Além disso, a réplica mantém uma cópia do estado desse serviço. Dois conceitos relacionados descrevem o ciclo de vida e o comportamento de réplicas com estado:
- Ciclo de vida da réplica
- Função da réplica

A discussão a seguir descreve os serviços com estado persistentes. Para serviços com estado voláteis (ou na memória), os estados inoperantes e removidos são equivalentes.

![Ciclo de vida da réplica](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Uma réplica InBuild é uma réplica criada ou preparada para unir o conjunto de réplicas. Dependendo da função da réplica, o IB tem semânticas diferentes. 

Se o host do aplicativo ou o nó de uma réplica InBuild falhar, ela fará a transição para o estado inoperante.

   - **Réplicas do InBuild primárias**: As réplicas do InBuild primárias são as primeiras réplicas em uma partição. Geralmente, essa réplica ocorre quando a partição está sendo criada. As réplicas do InBuild primárias também surgem quando todas as réplicas de uma partição são reiniciadas ou removidas.

   - **Réplicas IdleSecondary InBuild**: Essas são novas réplicas criadas pelo Gerenciador de recursos de Cluster ou réplicas existentes que estavam inoperantes e precisam ser adicionadas de volta ao conjunto. Essas réplicas são propagadas ou criadas pela primária antes de poderem ingressar no conjunto de réplicas como ActiveSecondary e participam da confirmação de quorum de operações.

   - **Réplicas ActiveSecondary InBuild**: Esse estado é observado em algumas consultas. É uma otimização em que o conjunto de réplicas não está sendo alterado, mas uma réplica precisa ser criada. A própria réplica segue as transições de computador de estado normal (conforme descrito na seção sobre funções de réplica).

### <a name="ready-rd"></a>Pronta (RD)
Uma réplica Pronta é uma réplica que está participando da replicação e da confirmação de quorum de operações. O estado pronto é aplicável a réplicas primárias e secundárias ativas.

Se o host do aplicativo ou o nó de uma réplica pronta falhar, ela fará a transição para o estado inoperante.

### <a name="closing-cl"></a>Fechamento (CL)
Uma réplica entra no estado de fechamento nos seguintes cenários:

- **Desligando o código para a réplica**: Talvez seja necessário desligar o código em execução para uma réplica do Service Fabric. Esse desligamento pode ocorrer por vários motivos. Por exemplo, isso pode ocorrer devido a uma atualização de aplicativo, de malha ou de infraestrutura ou devido a uma falha relatada pela réplica. Quando o fechamento da réplica for concluído, a réplica fará a transição para o estado inoperante. O estado persistente associado a esta réplica armazenada em disco não é limpo.

- **Removendo a réplica do cluster**: O Service Fabric talvez seja necessário remover o estado persistente e desligar o código em execução para uma réplica. Esse desligamento pode ocorrer por vários motivos, por exemplo, balanceamento de carga.

### <a name="dropped-dd"></a>Removido (DD)
No estado removido, a instância não está mais em execução no nó. Também não há mais nenhum estado no nó. Neste ponto, o Service Fabric mantém os metadados sobre essa instância, que é excluída definitivamente também.

### <a name="down-d"></a>Inoperante (D)
No estado inoperante, o código de réplica não está em execução, mas o estado persistente para essa réplica existe nesse nó. Uma réplica pode ficar inoperante por muitos motivos – por exemplo, o nó está inoperante, uma falha no código de réplica, uma atualização do aplicativo ou falhas de réplica.

Uma réplica inoperante é aberta pelo Service Fabric conforme necessário, por exemplo, quando a atualização é concluída no nó.

A função de réplica não é relevante no estado inoperante.

### <a name="opening-op"></a>Abertura (OP)
Uma réplica inoperante entra no estado de abertura quando o Service Fabric precisa colocar a réplica em operação novamente. Por exemplo, esse estado poderia ocorrer após a atualização de um código para o aplicativo ser concluído em um nó. 

Se o host do aplicativo ou o nó de uma réplica de abertura falhar, ela fará a transição para o estado inoperante.

A função da réplica não é relevante no estado de abertura.

### <a name="standby-sb"></a>StandBy (SB)
Uma réplica StandBy é uma réplica de um serviço persistente que ficou inoperante e foi aberto. Essa réplica poderá ser usada pelo Service Fabric se ele precisar adicionar outra réplica ao conjunto de réplicas (porque ele já tem alguma parte do estado e o processo de build é mais rápido). Depois que o StandByReplicaKeepDuration expirar, a réplica em espera será descartada.

Se o host do aplicativo ou o nó de uma réplica em espera falhar, ela fará a transição para o estado inoperante.

A função da réplica não é relevante no estado em espera.

> [!NOTE]
> Qualquer réplica que não esteja inoperante nem removida é considerada *operante*.
>

> [!NOTE]
> É possível fazer a transição de qualquer estado para o estado removido usando a opção **ForceRemove** em `Remove-ServiceFabricReplica`.
>

## <a name="replica-role"></a>Função da réplica 
A função da réplica determina sua função no conjunto de réplicas:

- **Primary (P)**: Há uma primária no conjunto de réplicas é responsável por executar operações leitura e gravação. 
- **ActiveSecondary (S)**: Essas são réplicas que recebem atualizações de estado da primária, aplicação-las e, em seguida, enviam confirmações de volta. Há várias secundárias ativas no conjunto de réplicas. O número dessas secundárias ativas determina o número de falhas que o serviço pode manipular.
- **IdleSecondary (I)**: Essas réplicas estão sendo criadas pela primária. Elas estão recebendo o estado da primária antes de poderem ser promovidas para a secundária ativa. 
- **None (N)**: Essas réplicas não têm uma responsabilidade no conjunto de réplicas.
- **Unknown (U)**: Essa é a função inicial de uma réplica antes de receber qualquer **ChangeRole** chamada à API do Service Fabric.

O diagrama a seguir ilustra as transições da função de réplica e alguns cenários de exemplo no qual elas podem ocorrer:

![Função da réplica](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U -> P: Criação de uma nova réplica primária.
- U -> I: Criação de uma nova réplica ociosa.
- U -> N: Exclusão de uma réplica em espera.
- I -> S: A promoção de ociosidade secundário para o secundário ativo, de modo que suas confirmações contribuam para o quorum.
- I -> P: Promoção de secundária ociosa para primária. Isso pode acontecer em reconfigurações especiais quando a secundária ociosa é a candidata correta a ser primária.
- I -> N: Exclusão da réplica secundária ociosa.
- S -> P: Promoção de secundária ativa para primária. Isso pode ocorrer devido ao failover da primária ou a uma movimentação primária iniciada pelo Gerenciador de Recursos de Cluster. Por exemplo, pode ser em resposta a uma atualização do aplicativo ou o balanceamento de carga.
- S -> N: Exclusão da réplica secundária ativa.
- P -> S: Rebaixamento da réplica primária. Isso pode ocorrer devido a uma movimentação primária iniciada pelo Gerenciador de Recursos de Cluster. Por exemplo, pode ser em resposta a uma atualização do aplicativo ou o balanceamento de carga.
- P -> N: Exclusão da réplica primária.

> [!NOTE]
> Os modelos de programação de nível mais alto, como [Reliable Actors](service-fabric-reliable-actors-introduction.md) e [Reliable Services](service-fabric-reliable-services-introduction.md), ocultam o conceito de funções de réplica do desenvolvedor. Em Actors, a noção de uma função é desnecessária. Em Services, ela é amplamente simplificada para a maioria dos cenários.
>

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre os conceitos do Service Fabric, consulte o artigo a seguir:

[Ciclo de vida dos Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)

