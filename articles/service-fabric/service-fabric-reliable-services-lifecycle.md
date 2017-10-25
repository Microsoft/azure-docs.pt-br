---
title: "Visão geral do ciclo de vida do Azure Service Fabric Reliable Services | Microsoft Docs"
description: Saiba mais sobre os diferentes eventos de ciclo de vida no Service Fabric Reliable Services
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek;
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: d5977a79cfe4016d6bd943cecb22edadc0eaec6b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="reliable-services-lifecycle-overview"></a>Visão geral do ciclo de vida do Reliable Services
> [!div class="op_single_selector"]
> * [C# em Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java no Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Ao pensar sobre os ciclos de vida do Reliable Services, os conceitos básicos são os mais importantes. Em geral:

- Durante a inicialização
  - Serviços são construídos
  - Eles têm a oportunidade de construir e retornar zero ou mais ouvintes
  - Todos os ouvintes retornados são abertos, permitindo a comunicação com o serviço
  - O método RunAsync do serviço é chamado, possibilitando um serviço de longa execução ou trabalho em segundo plano
- Durante o desligamento
  - O token de cancelamento passado para RunAsync é cancelado e os ouvintes são fechados
  - Assim que estiver concluído, o objeto do serviço será destruído

Há detalhes sobre a ordem exata desses eventos. Em especial, a ordem de eventos pode mudar um pouco dependendo se o Reliable Service tem estado ou não. Além disso, para serviços com estado, temos de lidar com o cenário de troca primário. Durante esta sequência, a função da Primária é transferida para outra réplica (ou retorna) sem o desligamento do serviço. Por fim, temos de pensar sobre as condições de erro ou falha.

## <a name="stateless-service-startup"></a>Inicialização de serviço sem estado
O ciclo de vida de um serviço sem estado é bastante simples. Aqui está a ordem de eventos:

1. O Serviço foi construído
2. Em seguida, ocorrem duas coisas em paralelo:
    - `StatelessService.CreateServiceInstanceListeners()` é invocado e todos os ouvintes retornados são Abertos. `ICommunicationListener.OpenAsync()` é chamado em cada ouvinte
    - O método `StatelessService.RunAsync()` do serviço é chamado
3. Se estiver presente, o método `StatelessService.OnOpenAsync()` do serviço será chamado. Essa não é uma substituição comum, mas está disponível.

É importante observar que não há nenhuma ordem entre as chamadas para criar e abrir os ouvintes e o RunAsync. Os ouvintes podem ser abertos antes de RunAsync ser iniciado. Da mesma forma, RunAsync pode acabar sendo invocado antes que os ouvintes de comunicação sejam abertos ou até mesmo antes de serem construídos. Se nenhuma sincronização for necessária, isso será deixado como um exercício para o implementador. Soluções comuns:

  - Algumas vezes os ouvintes não funcionam até que outras informações sejam criadas ou algum trabalho seja realizado. Para serviços sem estado, esse trabalho geralmente pode ser feito em outros locais, como: 
    - no construtor do serviço
    - durante a chamada de `CreateServiceInstanceListeners()`
    - como parte da construção do ouvinte em si
  - Às vezes, o código em RunAsync não é iniciado até que os ouvintes estejam abertos. Nesse caso, coordenação adicional será necessária. Uma solução comum é uma sinalização nos ouvintes, indicando quando eles foram concluídos. Esse sinalizador é verificado no RunAsync antes da continuação do trabalho real.

## <a name="stateless-service-shutdown"></a>Desligamento de serviço sem estado
Ao desligar um serviço sem estado, o mesmo padrão é seguido, porém na ordem inversa:

1. Em paralelo
    - Todos os ouvintes abertos são Fechados. `ICommunicationListener.CloseAsync()` é chamado em cada ouvinte.
    - O token de cancelamento passado para `RunAsync()` é cancelado. A verificação da propriedade `IsCancellationRequested` do token de cancelamento retorna true e, se chamado, o método `ThrowIfCancellationRequested` do token vai lançar `OperationCanceledException`.
2. Uma vez que `CloseAsync()` é concluído em cada ouvinte e `RunAsync()` também é finalizado, o método `StatelessService.OnCloseAsync()` do serviço será chamado, se estiver presente. Não é comum substituir `StatelessService.OnCloseAsync()`.
3. Depois da conclusão de `StatelessService.OnCloseAsync()`, o objeto de serviço é destruído

## <a name="stateful-service-startup"></a>Inicialização de serviço com estado
Serviços com estado têm um padrão semelhante aos serviços sem monitoração de estado, com algumas alterações. Ao iniciar um serviço com estado, a ordem de eventos é a seguinte:

1. O Serviço foi construído
2. `StatefulServiceBase.OnOpenAsync()` é chamado. Isso raramente é substituído no serviço.
3. As seguintes ações ocorrem paralelamente
    - `StatefulServiceBase.CreateServiceReplicaListeners()` é invocado 
      - Se o serviço for Primário, todos os ouvintes retornados serão Abertos. `ICommunicationListener.OpenAsync()` é chamado em cada ouvinte.
      - Se o serviço for um Secundário, somente esses ouvintes marcados como `ListenOnSecondary = true` serão abertos. Ter ouvintes que estão abertos em Secundários é menos comum.
    - Então, se o serviço for um Primário no momento, o método `StatefulServiceBase.RunAsync()` do serviço será chamado
4. Depois que todas as chamadas a `OpenAsync()` do ouvinte da réplica forem concluídas e `RunAsync()` for chamado, `StatefulServiceBase.OnChangeRoleAsync()` será chamado. Isso raramente é substituído no serviço.

Semelhante aos serviços sem estado, não há coordenação entre a ordem em que os ouvintes são criados e abertos e quando RunAsync é chamado. Se você precisar de coordenação, as soluções são muito parecidas. Há um caso adicional: digamos que as chamadas que chegam aos ouvintes de comunicação exijam que as informações sejam mantidas em algumas [Coleções Confiáveis](service-fabric-reliable-services-reliable-collections.md). Como os ouvintes de comunicação podem ser abertos antes que as coleções confiáveis fiquem legíveis ou graváveis e antes de RunAsync ser iniciado, é necessário realizar a coordenação. A solução mais simples e mais comum é os ouvintes de comunicação retornarem um código de erro que o cliente usa para repetir a solicitação.

## <a name="stateful-service-shutdown"></a>Desligamento de serviço com estado
Da mesma forma que os serviços sem monitoração de estado, os eventos de ciclo de vida durante o desligamento são os mesmos que durante a inicialização, porém invertidos. Quando um serviço com estado está sendo desligado, ocorrem os seguintes eventos:

1. Em paralelo
    - Todos os ouvintes abertos são Fechados. `ICommunicationListener.CloseAsync()` é chamado em cada ouvinte.
    - O token de cancelamento passado para `RunAsync()` é cancelado. A verificação da propriedade `IsCancellationRequested` do token de cancelamento retorna true e, se chamado, o método `ThrowIfCancellationRequested` do token vai lançar `OperationCanceledException`.
2. Uma vez que `CloseAsync()` é concluído em cada ouvinte e `RunAsync()` também é finalizado, o `StatefulServiceBase.OnChangeRoleAsync()` do serviço é chamado. (Isso raramente é substituído no serviço.)
    - Aguardar a conclusão de RunAsync só será necessário se essa réplica de serviço for um Primário.
3. Depois que o método `StatefulServiceBase.OnChangeRoleAsync()` for concluído, o método `StatefulServiceBase.OnCloseAsync()` será chamado. Essa não é uma substituição comum, mas está disponível.
3. Depois da conclusão de `StatefulServiceBase.OnCloseAsync()`, o objeto de serviço é destruído.

## <a name="stateful-service-primary-swaps"></a>Trocas de primária do serviço com estado
Enquanto um serviço com estado é executado, somente as réplicas Primárias destes serviços com estado terão seus ouvintes de comunicação abertos e seu método RunAsync chamado. Secundárias são construídas, mas não recebem chamadas. Contudo, enquanto um serviço com estado é executado, qual réplica é a Primária no momento pode mudar. O que isso significa em termos dos eventos do ciclo de vida que uma réplica pode ver? O comportamento que uma réplica com estado vê depende se ela é a réplica que está sendo rebaixada ou promovida durante a troca.

### <a name="for-the-primary-being-demoted"></a>Para a primária sendo rebaixada
O Service Fabric precisa que esta réplica interrompa o processamento de mensagens e feche todo o trabalho em segundo plano que estiver sendo realizado. Por isso, essa etapa é semelhante a quando o serviço está sendo desligado. Uma diferença é que o Serviço não é destruído ou fechado, pois ele permanece como uma Secundária. As seguintes APIs são chamadas:

1. Em paralelo
    - Todos os ouvintes abertos são Fechados. `ICommunicationListener.CloseAsync()` é chamado em cada ouvinte.
    - O token de cancelamento passado para `RunAsync()` é cancelado. A verificação da propriedade `IsCancellationRequested` do token de cancelamento retorna true e, se chamado, o método `ThrowIfCancellationRequested` do token vai lançar `OperationCanceledException`.
2. Uma vez que `CloseAsync()` é concluído em cada ouvinte e `RunAsync()` também é finalizado, o `StatefulServiceBase.OnChangeRoleAsync()` do serviço é chamado. Isso raramente é substituído no serviço.

### <a name="for-the-secondary-being-promoted"></a>Para a secundária que está sendo promovida
Da mesma forma, o Service Fabric precisa que essa réplica comece a escutar as mensagens durante a transmissão e inicie as tarefas em segundo plano que são importantes. Por isso, esse processo é similar à criação do serviço, exceto que a própria réplica já existe. As seguintes APIs são chamadas:

1. Em paralelo
    - `StatefulServiceBase.CreateServiceReplicaListeners()` é invocado e todos os ouvintes retornados são Abertos. `ICommunicationListener.OpenAsync()` é chamado em cada ouvinte.
    - O método `StatefulServiceBase.RunAsync()` do serviço é chamado
2. Depois que todas as chamadas a `OpenAsync()` do ouvinte da réplica forem concluídas e `RunAsync()` tiver sido chamado, `StatefulServiceBase.OnChangeRoleAsync()` será chamado. Isso raramente é substituído no serviço.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Problemas comuns durante o desligamento do serviço com estado e rebaixamento do primário
O Service Fabric altera o Primário de um serviço com estado por vários motivos. Os mais comuns são [rebalanceamento do cluster](service-fabric-cluster-resource-manager-balancing.md) e [upgrade de aplicativo](service-fabric-application-upgrade.md). Durante essas operações (bem como durante o desligamento normal do serviço, como você veria se o serviço fosse excluído), é importante que o serviço respeite o `CancellationToken`. Os serviços que não tratarem o cancelamento corretamente enfrentarão vários problemas. Particularmente, essas operações ficarão lentas, uma vez que o Service Fabric espera que os serviços parem normalmente. Em última instância, isso leva a falhas de upgrade que atingem um tempo limite e são revertidas. Não respeitar o token de cancelamento também pode resultar em clusters desequilibrados, pois os nós são ativados, mas os serviços não podem ser rebalanceados, uma vez que é muito demorado movê-los para qualquer outro lugar. 

Como os serviços têm estado, também é provável que eles estejam usando as [Coleções Confiáveis](service-fabric-reliable-services-reliable-collections.md). No Service Fabric, quando um Primário é rebaixado, uma das primeiras coisas que acontece é que o acesso de gravação ao estado subjacente é revogado. Isso leva a um segundo conjunto de problemas que pode afetar o ciclo de vida do serviço. As coleções retornam Exceções que se baseiam no tempo e se a réplica está sendo movida ou desligada. Essas exceções devem ser tratadas corretamente. As exceções geradas pelo Service Fabric se classificam nas categorias permanentes [(`FabricException`)](https://docs.microsoft.com/en-us/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) e transitórias [(`FabricTransientException`)](https://docs.microsoft.com/en-us/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet). As exceções permanentes devem ser registradas e lançadas, enquanto as exceções transitórias podem ser recuperadas com base em alguma lógica de repetição.

O tratamento das exceções que resultam do uso de `ReliableCollections` em conjunto com eventos de ciclo de vida do serviço é uma importante etapa do teste e da validação de um Serviço Confiável. A recomendação é sempre executar seu serviço sob carga durante a execução de upgrades e sempre [fazer o teste de caos](service-fabric-controlled-chaos.md) antes de implantar no ambiente de produção. Essas etapas básicas ajudam a garantir que o serviço seja implementado corretamente, além de tratar os eventos do ciclo de vida corretamente.


## <a name="notes-on-service-lifecycle"></a>Observações sobre o ciclo de vida do serviço
  - Tanto o método `RunAsync()` quanto as chamadas `CreateServiceReplicaListeners/CreateServiceInstanceListeners` são opcionais. Um serviço pode ter um deles, ambos ou nenhum. Por exemplo, se o serviço fizer todo seu trabalho em resposta a chamadas do usuário, não será necessário implementar `RunAsync()`. Apenas os ouvintes de comunicação e seu código associado são necessários. Da mesma forma, criar e retornar ouvintes de comunicação é opcional, pois o serviço pode ter apenas trabalho em segundo plano e só precisar implementar `RunAsync()`
  - Isso é válido para um serviço concluir `RunAsync()` com êxito e retornar dele. A conclusão não é uma condição de falha. A conclusão de `RunAsync()` indica que o trabalho em segundo plano do serviço foi concluído. Para serviços confiáveis com estado, `RunAsync()` será chamado novamente se a réplica tiver sido rebaixada de Primária para Secundária e promovida de volta para Primária.
  - Se um serviço sair de `RunAsync()` lançando uma exceção inesperada, isso constituirá uma falha. O objeto de serviço será desligado e um erro de integridade reportado.
  - Embora não haja um limite de tempo para o retorno desses métodos, você perde imediatamente a capacidade de gravar em Reliable Collections e, portanto, não pode concluir qualquer trabalho real. Recomendamos que você retorne o mais rápido possível após o recebimento da solicitação de cancelamento. Se o serviço não responder a essas chamadas à API dentro de um período razoável, o Service Fabric poderá forçar o encerramento do serviço. Geralmente isso ocorre apenas durante atualizações de aplicativo ou quando um serviço está sendo excluído. Esse tempo limite é de 15 minutos por padrão.
  - Falhas no caminho de `OnCloseAsync()` resultam em uma chamada de `OnAbort()`, que é uma oportunidade de melhor esforço de última chance para o serviço ser limpo e liberar quaisquer recursos ocupados.

## <a name="next-steps"></a>Próximas etapas
- [Introdução ao Reliable Services](service-fabric-reliable-services-introduction.md)
- [Início Rápido dos Serviços Confiáveis](service-fabric-reliable-services-quick-start.md)
- [Uso avançado de Reliable Services](service-fabric-reliable-services-advanced-usage.md)
- [Réplicas e Instâncias](service-fabric-concepts-replica-lifecycle.md)