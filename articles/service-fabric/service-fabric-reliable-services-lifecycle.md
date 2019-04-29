---
title: Visão geral do ciclo de vida do Azure Service Fabric Reliable Services | Microsoft Docs
description: Saiba mais sobre os diferentes eventos de ciclo de vida no Service Fabric Reliable Services
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: vturecek;
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: ebc7aec63b34630b606178aa17e2ae7fdd0fc87f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723532"
---
# <a name="reliable-services-lifecycle-overview"></a>Visão geral do ciclo de vida do Reliable Services
> [!div class="op_single_selector"]
> * [C# em Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java no Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Quando você está pensando sobre os ciclos de vida do Azure Service Fabric Reliable Services, os conceitos básicos do ciclo de vida são os mais importantes. Em geral, o ciclo de vida inclui o seguinte:

- Durante a inicialização:
  - Os serviços são construídos.
  - Os serviços têm a oportunidade de construir e retornar zero ou mais ouvintes.
  - Todos os ouvintes retornados são abertos, permitindo a comunicação com o serviço.
  - O método **RunAsync** do serviço é chamado, possibilitando ao serviço realizar tarefas de longa execução ou trabalho em segundo plano.
- Durante o desligamento:
  - O token de cancelamento passado para o **RunAsync** é cancelado e os ouvintes são fechados.
  - Depois de os ouvintes fecharem, o próprio objeto de serviço é destruído.

Há detalhes sobre a ordem exata desses eventos. A ordem de eventos pode mudar um pouco dependendo de se o Reliable Service tem estado ou não. Além disso, para serviços com estado, temos de lidar com o cenário de troca primário. Durante esta sequência, a função da Primária é transferida para outra réplica (ou retorna) sem o desligamento do serviço. Por fim, precisamos pensar nas condições de erro ou falha.

## <a name="stateless-service-startup"></a>Inicialização de serviço sem estado
O ciclo de vida de um serviço sem estado é simples. Aqui está a ordem de eventos:

1. O Serviço foi construído.
2. Em seguida, ocorrem duas ações em paralelo:
    - `StatelessService.CreateServiceInstanceListeners()` é invocado e todos os ouvintes retornados são abertos. `ICommunicationListener.OpenAsync()` é chamado em cada ouvinte.
    - O método `StatelessService.RunAsync()` do serviço é chamado.
3. Se estiver presente, o método `StatelessService.OnOpenAsync()` do serviço será chamado. Essa chamada não é uma substituição comum, mas está disponível. As tarefas de inicialização do serviço estendido podem ser iniciadas nesse momento.

Lembre-se de que não há nenhuma ordenação entre as chamadas para criar e abrir os ouvintes e o **RunAsync**. Os ouvintes podem ser abertos antes que o **RunAsync** seja iniciado. Da mesma forma, você pode invocar **RunAsync** antes que os ouvintes de comunicação sejam abertos ou até mesmo construídos. Se nenhuma sincronização for necessária, isso será deixado como um exercício para o implementador. Aqui estão algumas soluções comuns:

  - Algumas vezes, os ouvintes não funcionam até que outras informações sejam criadas ou algum trabalho seja realizado. Para serviços sem monitoração de estado, esse trabalho geralmente pode ser feito em outros locais, como o seguinte: 
    - No construtor do serviço.
    - Durante a chamada de `CreateServiceInstanceListeners()`.
    - Como parte da construção do ouvinte em si.
  - Às vezes, o código em **RunAsync** não é iniciado até que os ouvintes sejam abertos. Nesse caso, coordenação adicional será necessária. Uma solução comum é que há um sinalizador dentro os ouvintes que indica quando eles terminam. Esse sinalizador então é verificado no **RunAsync** antes que o trabalho real continue.

## <a name="stateless-service-shutdown"></a>Desligamento de serviço sem estado
Para desligar um serviço sem estado, o mesmo padrão é seguido, porém na ordem inversa:

1. Em paralelo:
    - Todos os ouvintes abertos são fechados. `ICommunicationListener.CloseAsync()` é chamado em cada ouvinte.
    - O token de cancelamento passado para `RunAsync()` é cancelado. Uma verificação da propriedade `IsCancellationRequested` do token de cancelamento retorna true e, se chamado, o método `ThrowIfCancellationRequested` do token lança um `OperationCanceledException`.
2. Depois que `CloseAsync()` termina em cada ouvinte e `RunAsync()` também é concluído, o método `StatelessService.OnCloseAsync()` do serviço é chamado, se presente.  OnCloseAsync é chamado quando a instância do serviço sem estado está prestes a ser desligada de modo normal. Isso pode ocorrer quando o código de serviço estiver sendo atualizado, quando a instância do serviço estiver sendo movida devido ao balanceamento de carga ou quando for detectada uma falha temporária. Não é uma substituição comum `StatelessService.OnCloseAsync()`, mas pode ser usada para fechar todos os recursos com segurança, interromper todos os processamentos em segundo plano, terminar de salvar o estado externo ou fechar conexões existentes.
3. Depois que `StatelessService.OnCloseAsync()` for concluído, o objeto de serviço será destruído.

## <a name="stateful-service-startup"></a>Inicialização de serviço com estado
Serviços com estado têm um padrão semelhante aos serviços sem monitoração de estado, com algumas alterações. Para inicializar um serviço com estado, a ordem dos eventos é a seguinte:

1. O Serviço foi construído.
2. `StatefulServiceBase.OnOpenAsync()` é chamado. Essa chamada não costuma ser substituída no serviço.
3. As seguintes ações ocorrem paralelamente:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` é invocado. 
      - Se o serviço for Primário, todos os ouvintes retornados serão abertos. `ICommunicationListener.OpenAsync()` é chamado em cada ouvinte.
      - Se o serviço for um serviço secundário, somente ouvintes marcados como `ListenOnSecondary = true` serão abertos. Ter ouvintes abertos em secundários é menos comum.
    - Se o serviço for Primário no momento, o método `StatefulServiceBase.RunAsync()` do serviço será chamado.
4. Depois que todas as chamadas `OpenAsync()` do ouvinte da réplica forem concluídas e `RunAsync()` for chamado, `StatefulServiceBase.OnChangeRoleAsync()` será chamado. Essa chamada não costuma ser substituída no serviço.

Semelhante aos serviços sem monitoração de estado, não há coordenação entre a ordem em que os ouvintes são criados e abertos e quando **RunAsync** é chamado. Se você precisar de coordenação, as soluções são muito parecidas. Há um caso adicional para o serviço com estado. Digamos que as chamadas que chegam aos ouvintes de comunicação precisem de informações mantidas dentro de algumas [Coleções Confiáveis](service-fabric-reliable-services-reliable-collections.md).

   > [!NOTE]  
   > Como os ouvintes de comunicação podem ser abertos antes que as coleções confiáveis fiquem legíveis ou graváveis e antes de **RunAsync** ser iniciado, é necessário realizar a coordenação. A solução mais simples e mais comum é os ouvintes de comunicação retornarem um código de erro que o cliente usa para repetir a solicitação.

## <a name="stateful-service-shutdown"></a>Desligamento de serviço com estado
Da mesma forma que os serviços sem monitoração de estado, os eventos de ciclo de vida durante o desligamento são os mesmos que durante a inicialização, porém invertidos. Quando um serviço com estado está sendo desligado, ocorrem os seguintes eventos:

1. Em paralelo:
    - Todos os ouvintes abertos são fechados. `ICommunicationListener.CloseAsync()` é chamado em cada ouvinte.
    - O token de cancelamento passado para `RunAsync()` é cancelado. Uma verificação da propriedade `IsCancellationRequested` do token de cancelamento retorna true e, se chamado, o método `ThrowIfCancellationRequested` do token lança um `OperationCanceledException`.
2. Depois que `CloseAsync()` terminar em cada ouvinte e `RunAsync()` também terminar, o serviço `StatefulServiceBase.OnChangeRoleAsync()` será chamado. Essa chamada não costuma ser substituída no serviço.

   > [!NOTE]  
   > A necessidade de aguardar **RunAsync** terminar só existe se esta réplica for primária.

3. Após o método `StatefulServiceBase.OnChangeRoleAsync()` terminar, o método `StatefulServiceBase.OnCloseAsync()` é chamado. Essa chamada não é uma substituição comum, mas está disponível.
3. Depois que `StatefulServiceBase.OnCloseAsync()` for concluído, o objeto de serviço será destruído.

## <a name="stateful-service-primary-swaps"></a>Trocas Primárias de serviço com estado
Enquanto um serviço com estado está em execução, somente as réplicas Primárias dos serviços com estado terão seus ouvintes de comunicação abertos e seu método **RunAsync** chamado. Réplicas secundárias são construídas, mas não veem mais chamadas. Durante a execução de um serviço com estado, a réplica que atualmente é a Primária poderá ser alterada como resultado de uma falha ou da otimização do balanceamento do cluster. O que isso significa em termos dos eventos do ciclo de vida que uma réplica pode ver? O comportamento que uma réplica com estado vê depende se ela é a réplica que está sendo rebaixada ou promovida durante a troca.

### <a name="for-the-primary-thats-demoted"></a>Para a primária que é rebaixada
Para a réplica primária é rebaixada, o Service Fabric precisa que essa réplica interrompa o processamento de mensagens e feche qualquer trabalho em segundo plano que esteja executando. Como resultado, esta etapa tem uma aparência igual à de quando o serviço está desligado. Uma diferença é que o Serviço não é destruído nem fechado, pois ele permanece como Secundário. As seguintes APIs são chamadas:

1. Em paralelo:
    - Todos os ouvintes abertos são fechados. `ICommunicationListener.CloseAsync()` é chamado em cada ouvinte.
    - O token de cancelamento passado para `RunAsync()` é cancelado. Uma verificação da propriedade `IsCancellationRequested` do token de cancelamento retorna true e, se chamado, o método `ThrowIfCancellationRequested` do token lança um `OperationCanceledException`.
2. Depois que `CloseAsync()` terminar em cada ouvinte e `RunAsync()` também terminar, o serviço `StatefulServiceBase.OnChangeRoleAsync()` será chamado. Essa chamada não costuma ser substituída no serviço.

### <a name="for-the-secondary-thats-promoted"></a>Para a secundária que é promovida
Da mesma forma, o Service Fabric precisa que a réplica Secundária que é promovida escute mensagens na transmissão e inicie quaisquer tarefas em segundo plano que precise concluir. Assim, esse processo tem aparência similar à de quando o serviço é criado, exceto que a réplica em si já existe. As seguintes APIs são chamadas:

1. Em paralelo:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` é invocado e todos os ouvintes retornados são abertos. `ICommunicationListener.OpenAsync()` é chamado em cada ouvinte.
    - O método `StatefulServiceBase.RunAsync()` do serviço é chamado.
2. Depois que todas as chamadas `OpenAsync()` do ouvinte da réplica forem concluídas e `RunAsync()` for chamado, `StatefulServiceBase.OnChangeRoleAsync()` será chamado. Essa chamada não costuma ser substituída no serviço.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Problemas comuns durante o desligamento do serviço com estado e o rebaixamento da Primária
O Service Fabric altera o Primário de um serviço com estado por vários motivos. Os mais comuns são [rebalanceamento do cluster](service-fabric-cluster-resource-manager-balancing.md) e [upgrade de aplicativo](service-fabric-application-upgrade.md). Durante essas operações (bem como durante o desligamento normal do serviço, como você veria se o serviço fosse excluído), é importante que o serviço respeite o `CancellationToken`. 

Os serviços que não tratam o cancelamento corretamente podem enfrentar vários problemas. Essas operações são lentas porque o Service Fabric aguarda até que os serviços parem normalmente. Em última instância, isso leva a falhas de upgrade que atingem um tempo limite e são revertidas. Falha em cumprir o token de cancelamento também pode causar clusters desequilibrados. Os clusters tornam-se desequilibrados porque os nós ficam ativos, mas os serviços não podem ser reequilibrados porque demora muito para movê-los para outro lugar. 

Uma vez que os serviços têm estado, também é provável que eles usem [Coleções Confiáveis](service-fabric-reliable-services-reliable-collections.md). No Service Fabric, quando um Primário é rebaixado, uma das primeiras coisas que acontece é que o acesso de gravação ao estado subjacente é revogado. Isso leva a um segundo conjunto de problemas que pode afetar o ciclo de vida do serviço. As coleções retornam exceções que se baseiam no tempo e em se a réplica está sendo movida ou desligada. Essas exceções devem ser tratadas corretamente. As exceções geradas pelo Service Fabric se classificam nas categorias permanentes [(`FabricException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) e transitórias [(`FabricTransientException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet). As exceções permanentes devem ser registradas e geradas, enquanto as exceções transitórias podem ser recuperadas com base em alguma lógica de repetição.

O tratamento das exceções que resultam do uso de `ReliableCollections` em conjunto com eventos de ciclo de vida do serviço é uma importante etapa do teste e da validação de um Serviço Confiável. A recomendação é sempre executar seu serviço sob carga durante a execução de upgrades e [fazer o teste de caos](service-fabric-controlled-chaos.md) antes de implantar em produção. Essas etapas básicas ajudam a garantir que o serviço seja implementado corretamente, além de tratar os eventos do ciclo de vida corretamente.


## <a name="notes-on-the-service-lifecycle"></a>Observações sobre o ciclo de vida do serviço
  - Tanto o método `RunAsync()` quanto as chamadas `CreateServiceReplicaListeners/CreateServiceInstanceListeners` são opcionais. Um serviço pode ter um deles, ambos ou nenhum. Por exemplo, se o serviço fizer todo seu trabalho em resposta a chamadas do usuário, não será necessário implementar `RunAsync()`. Apenas os ouvintes de comunicação e seu código associado são necessários. Da mesma forma, criar e retornar ouvintes de comunicação é opcional, pois o serviço pode ter apenas trabalho em segundo plano e só precisar implementar `RunAsync()`.
  - Isso é válido para um serviço concluir `RunAsync()` com êxito e retornar dele. A conclusão não é uma condição de falha. A conclusão de `RunAsync()` indica que o trabalho em segundo plano do serviço foi concluído. Para Reliable Services com estado, `RunAsync()` será chamado novamente se a réplica tiver sido rebaixada de Primária para Secundária e promovida de volta para Primária.
  - Se um serviço sair de `RunAsync()` lançando uma exceção inesperada, isso constituirá uma falha. O objeto de serviço é desligado e um erro de integridade é reportado.
  - Embora não haja um limite de tempo para o retorno desses métodos, você perde imediatamente a capacidade de gravar em Reliable Collections e, portanto, não pode concluir nenhum trabalho real. Recomendamos que você retorne o mais rápido possível após o recebimento da solicitação de cancelamento. Se o serviço não responder a essas chamadas à API dentro de um período razoável, o Service Fabric poderá forçar o encerramento do serviço. Geralmente isso ocorre apenas durante atualizações de aplicativo ou quando um serviço está sendo excluído. Esse tempo limite é de 15 minutos por padrão.
  - Falhas no caminho de `OnCloseAsync()` resultam em uma chamada de `OnAbort()`, que é uma oportunidade de melhor esforço de última chance para o serviço ser limpo e liberar quaisquer recursos ocupados. Ele geralmente é chamado quando é detectada uma falha permanente no nó ou quando a malha de serviço não pode gerenciar confiavelmente o ciclo de vida da instância do serviço devido a falhas internas.
  - `OnChangeRoleAsync()` é chamado quando a réplica de serviço com estado está alterando funções, por exemplo para primário ou secundário. Réplicas primárias recebem status de gravação (têm permissão para criar as Coleções Confiáveis e gravar nelas). Réplicas secundárias recebem status de leitura (podem somente ler das coleções confiáveis existentes). A maior parte do trabalho em um serviço com estado é executado na réplica primária. Réplicas secundárias podem realizar validação somente leitura, geração de relatórios, mineração de dados ou outros trabalhos somente leitura.

## <a name="next-steps"></a>Próximas etapas
- [Introdução ao Reliable Services](service-fabric-reliable-services-introduction.md)
- [Início Rápido dos Serviços Confiáveis](service-fabric-reliable-services-quick-start.md)
- [Instâncias e réplicas](service-fabric-concepts-replica-lifecycle.md)
