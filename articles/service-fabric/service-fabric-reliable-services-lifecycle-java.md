---
title: Azure Service Fabric Reliable Services | Microsoft Docs
description: Saiba mais sobre os eventos de ciclo de vida no Service Fabric Reliable Services.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 36c1ff2ace944d84120bf456060c7504170a814c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772934"
---
# <a name="reliable-services-lifecycle"></a>Ciclo de vida de Reliable Services
> [!div class="op_single_selector"]
> * [C# em Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java no Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Os Reliable Services são um dos modelos de programação disponíveis no Microsoft Azure Service Fabric. Ao aprender sobre o ciclo de vida dos Reliable Services, o mais importante é entender os eventos de ciclo de vida básicos. A ordem exata de eventos depende dos detalhes de configuração. 

Em geral, o ciclo de vida dos Reliable Services inclui os seguintes eventos:

* Durante a inicialização:
  * Os serviços são construídos.
  * Os serviços têm a oportunidade de construir e retornar zero ou mais ouvintes.
  * Todos os ouvintes retornados são abertos, permitindo a comunicação com o serviço.
  * O método `runAsync` do serviço é chamado, possibilitando um serviço de longa execução ou trabalho em segundo plano.
* Durante o desligamento:
  * O token de cancelamento passado para `runAsync` é cancelado e os ouvintes são fechados.
  * O objeto do serviço será destruído.

A ordem de eventos em Reliable Services pode ser ligeiramente alterada, dependendo se o Reliable Service é com ou sem estado. 

Além disso, para serviços com estado, você deve solucionar o cenário de troca primária. Durante esta sequência, a função da Primária é transferida para outra réplica (ou retorna) sem o desligamento do serviço. 

Por fim, você tem que pensar sobre as condições de erro ou falha.

## <a name="stateless-service-startup"></a>Inicialização de serviço sem estado
O ciclo de vida de um serviço sem estado é bastante simples. Aqui está a ordem de eventos:

1. O Serviço foi construído.
2. Esses eventos ocorrem em paralelo:
    - `StatelessService.createServiceInstanceListeners()` é invocado e todos os ouvintes retornados são abertos. `CommunicationListener.openAsync()` é chamado em cada ouvinte.
    - O método de serviço `runAsync` (`StatelessService.runAsync()`) é chamado.
3. Se estiver presente, o método `onOpenAsync` do próprio serviço será chamado. Especificamente, `StatelessService.onOpenAsync()` é chamado. Essa não é uma substituição comum, mas está disponível.

É importante observar que não há nenhuma ordem entre as chamadas para criar e abrir os ouvintes e a chamada para `runAsync`. Os ouvintes podem abrir antes de `runAsync` ser iniciado. Da mesma forma, `runAsync` pode ser chamado antes de os ouvintes de comunicação serem abertos, ou antes mesmo de serem construídos. Se alguma sincronização for necessária, ela deve ser realizada pelo implementador. Aqui estão algumas soluções comuns:

* Algumas vezes, os ouvintes não funcionam até que outras informações sejam criadas ou que algum outro trabalho seja realizado. Para serviços sem estado, esse trabalho normalmente pode ser feito no construtor do serviço. Isso pode ser feito durante o chamado `createServiceInstanceListeners()`, ou como parte da construção do ouvinte em si.
* Às vezes, o código em `runAsync` não será iniciado até que os ouvintes sejam abertos. Nesse caso, coordenação adicional será necessária. Uma solução comum é adicionar um sinalizador nos ouvintes. O sinalizador indica quando os ouvintes tiverem concluído. O método `runAsync` verifica isso antes de continuar o trabalho atual.

## <a name="stateless-service-shutdown"></a>Desligamento de serviço sem estado
Ao desligar um serviço sem estado, o mesmo padrão é seguido, porém na ordem inversa:

1. Esses eventos ocorrem em paralelo:
    - Todos os ouvintes abertos são fechados. `CommunicationListener.closeAsync()` é chamado em cada ouvinte.
    - O token de cancelamento passado para `runAsync()` é cancelado. A verificação da propriedade `isCancelled` do token de cancelamento retorna `true` e, se chamado, o método `throwIfCancellationRequested` do token vai lançar `CancellationException`.
2. Depois que `closeAsync()` termina em cada ouvinte e `runAsync()` também é concluído, o método `StatelessService.onCloseAsync()` do serviço é chamado, se estiver presente. Novamente, não é uma substituição comum, mas pode ser usada para fechar todos os recursos com segurança, interromper todos os processamentos em segundo plano, terminar de salvar o estado externo ou fechar conexões existentes.
3. Depois que `StatelessService.onCloseAsync()` for concluído, o objeto de serviço será destruído.

## <a name="stateful-service-startup"></a>Inicialização de serviço com estado
Os serviços com estado têm um padrão semelhante aos serviços sem estado, com algumas alterações.  Veja a ordem de eventos para iniciar um serviço com estado:

1. O Serviço foi construído.
2. `StatefulServiceBase.onOpenAsync()` é chamado. Essa chamada não costuma ser substituída no serviço.
3. Esses eventos ocorrem em paralelo:
    - `StatefulServiceBase.createServiceReplicaListeners()` é invocado. 
      - Se o serviço for um serviço primário, todos os ouvintes retornados serão abertos. `CommunicationListener.openAsync()` é chamado em cada ouvinte.
      - Se o serviço for um serviço secundário, somente ouvintes marcados como `listenOnSecondary = true` serão abertos. Ter ouvintes abertos em secundários é menos comum.
    - Se o serviço for primário no momento, o método `StatefulServiceBase.runAsync()` do serviço será chamado.
4. Depois que todas as chamadas `openAsync()` do ouvinte da réplica forem concluídas e `runAsync()` for chamado, `StatefulServiceBase.onChangeRoleAsync()` será chamado. Essa chamada não costuma ser substituída no serviço.

Similarmente aos serviços sem estado, nos serviços com estado, não há nenhuma coordenação entre a ordem em que os ouvintes são criados e abertos, e quando `runAsync` é chamado. Se você precisar de coordenação, as soluções são muito parecidas. Mas há um caso adicional para o serviço com estado. Digamos que as chamadas que chegam aos ouvintes de comunicação precisem de informações mantidas dentro de algumas [Coleções Confiáveis](service-fabric-reliable-services-reliable-collections.md). Como os ouvintes de comunicação podem abrir antes das coleções confiáveis estarem legíveis ou graváveis, e antes de `runAsync` iniciar, uma coordenação adicional é necessária. A solução mais simples e mais comum é os ouvintes de comunicação retornarem um código de erro. O cliente usa o código de erro para repetir a solicitação.

## <a name="stateful-service-shutdown"></a>Desligamento de serviço com estado
Da mesma forma que os serviços sem monitoração de estado, os eventos de ciclo de vida durante o desligamento são os mesmos que durante a inicialização, porém invertidos. Quando um serviço com estado está sendo desligado, ocorrem os seguintes eventos:

1. Esses eventos ocorrem em paralelo:
    - Todos os ouvintes abertos são fechados. `CommunicationListener.closeAsync()` é chamado em cada ouvinte.
    - O token de cancelamento passado para `runAsync()` é cancelado. Uma chamada para o método `isCancelled()` do token de cancelamento retorna `true` e, se chamado, o método `throwIfCancellationRequested()` do token lança um `OperationCanceledException`.
2. Depois que `closeAsync()` terminar em cada ouvinte e `runAsync()` também terminar, o serviço `StatefulServiceBase.onChangeRoleAsync()` será chamado. Essa chamada não costuma ser substituída no serviço.

   > [!NOTE]  
   > Esperar pela conclusão de `runAsync` só é necessário se essa réplica for uma réplica primária.

3. Após o método `StatefulServiceBase.onChangeRoleAsync()` terminar, o método `StatefulServiceBase.onCloseAsync()` é chamado. Essa chamada não é uma substituição comum, mas está disponível.
3. Depois que `StatefulServiceBase.onCloseAsync()` for concluído, o objeto de serviço será destruído.

## <a name="stateful-service-primary-swaps"></a>Trocas de primária do serviço com estado
Durante a execução de um serviço com estado, os ouvintes de comunicação são abertos e o método `runAsync` só é chamado para as réplicas primárias daqueles serviços com estado. Réplicas secundárias são construídas, mas não veem mais chamadas. Durante a execução de um serviço com estado, a réplica atualmente primária pode alterar. Os eventos de ciclo de vida que uma réplica com monitoração de estado pode ver depende se ela é a réplica que está sendo rebaixada ou promovida durante a troca.

### <a name="for-the-demoted-primary"></a>Para a réplica primária rebaixada
O Service Fabric precisa da réplica primária que é rebaixada para parar o processamento de mensagens e parar qualquer trabalho em segundo plano. Esta etapa é semelhante a quando o serviço for desligado. Uma diferença é que o Serviço não é destruído nem fechado, pois ele permanece como Secundário. Os seguintes eventos ocorrem:

1. Esses eventos ocorrem em paralelo:
    - Todos os ouvintes abertos são fechados. `CommunicationListener.closeAsync()` é chamado em cada ouvinte.
    - O token de cancelamento passado para `runAsync()` é cancelado. Uma verificação de que o método `isCancelled()` do token de cancelamento retorna `true`. Se chamado, método `throwIfCancellationRequested()` do token lança um `OperationCanceledException`.
2. Depois que `closeAsync()` terminar em cada ouvinte e `runAsync()` também terminar, o serviço `StatefulServiceBase.onChangeRoleAsync()` será chamado. Essa chamada não costuma ser substituída no serviço.

### <a name="for-the-promoted-secondary"></a>Para a réplica secundária promovida
Da mesma forma, o Service Fabric precisa que a réplica secundária promovida escute mensagens na transmissão e inicie quaisquer tarefas em segundo plano que precise concluir. Esta etapa é semelhante a quando o serviço é criado. A diferença é que a réplica em si já existe. Os seguintes eventos ocorrem:

1. Esses eventos ocorrem em paralelo:
    - `StatefulServiceBase.createServiceReplicaListeners()` é invocado e todos os ouvintes retornados são abertos. `CommunicationListener.openAsync()` é chamado em cada ouvinte.
    - O método `StatefulServiceBase.runAsync()` do serviço é chamado.
2. Depois que todas as chamadas `openAsync()` do ouvinte da réplica forem concluídas e `runAsync()` for chamado, `StatefulServiceBase.onChangeRoleAsync()` será chamado. Essa chamada não costuma ser substituída no serviço.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Problemas comuns durante o desligamento do serviço com estado e rebaixamento do primário
O Service Fabric altera a réplica primária de um serviço com estado por vários motivos. Os mais comuns são [rebalanceamento do cluster](service-fabric-cluster-resource-manager-balancing.md) e [upgrade de aplicativo](service-fabric-application-upgrade.md). Durante essas operações, é importante que o serviço respeite o `cancellationToken`. Isso também se aplica durante o desligamento normal do serviço, como se o serviço fosse excluído.

Os serviços que não tratam o cancelamento corretamente podem enfrentar vários problemas. Essas operações são lentas porque o Service Fabric aguarda até que os serviços parem normalmente. Em última instância, isso leva a falhas de upgrade que atingem um tempo limite e são revertidas. Falha em cumprir o token de cancelamento também pode causar clusters desequilibrados. Os clusters ficam desequilibrados porque os nós ficam quentes. No entanto, os serviços não podem ser reequilibrados porque demora muito para movê-los para outro lugar. 

Uma vez que os serviços têm estado, também é provável que eles usem [Coleções Confiáveis](service-fabric-reliable-services-reliable-collections.md). No Service Fabric, quando uma réplica primária é rebaixada, uma das primeiras coisas que acontece é que o acesso de gravação ao estado subjacente é revogado. Isso leva a um segundo conjunto de problemas que pode afetar o ciclo de vida do serviço. As coleções retornam exceções que se baseiam no tempo e em se a réplica está sendo movida ou desligada. É importante tratar essas exceções corretamente. 

Exceções geradas pelo Service Fabric são permanentes [(`FabricException`)](https://docs.microsoft.com/java/api/system.fabric.exception) ou transitórias [(`FabricTransientException`)](https://docs.microsoft.com/java/api/system.fabric.exception.fabrictransientexception). As exceções permanentes devem ser registradas e lançadas. As exceções transitórias podem ser repetidas com base na lógica de repetição.

Uma parte importante de testar e validar os Reliable Services é manipular as exceções que vêm do uso de `ReliableCollections` em conjunto com eventos de ciclo de vida do serviço. É recomendável que você sempre execute seu serviço sob carga. Você também deve executar atualizações e [teste de caos](service-fabric-controlled-chaos.md) antes de implantar para produção. Essas etapas básicas ajudam a garantir que o serviço seja implementado corretamente,e que ele trate os eventos do ciclo de vida corretamente.

## <a name="notes-on-service-lifecycle"></a>Observações sobre o ciclo de vida do serviço
* Tanto o método `runAsync()` quanto as chamadas `createServiceInstanceListeners/createServiceReplicaListeners` são opcionais. Um serviço pode ter um, ambos ou nenhum. Por exemplo, se o serviço fizer todo seu trabalho em resposta a chamadas do usuário, não será necessário implementar `runAsync()`. Apenas os ouvintes de comunicação e seu código associado são necessários.  Da mesma forma, criar e retornar ouvintes de comunicação é opcional. O serviço pode ter somente trabalho em segundo plano para fazer, e portanto precisa implementar `runAsync()`.
* Isso é válido para um serviço concluir `runAsync()` com êxito e retornar dele. Isso não é considerado uma condição de falha. Representa o trabalho em segundo plano da finalização do serviço. Para serviço confiável com estado, `runAsync()` seria chamado novamente se o serviço tiver sido rebaixado de primário e promovido novamente para primário.
* Se um serviço sair de `runAsync()`, lançando uma exceção inesperada, isso constituirá uma falha. O objeto de serviço é desligado e um erro de integridade é reportado.
* Embora não haja um limite de tempo para o retorno desses métodos, você perde imediatamente a capacidade de gravar. Portanto, não pode concluir nenhum trabalho real. Recomendamos que você retorne o mais rápido possível após o recebimento da solicitação de cancelamento. Se o serviço não responder a essas chamadas à API dentro de um período razoável, o Service Fabric poderá forçar o encerramento do serviço. Geralmente isso ocorre apenas durante atualizações de aplicativo ou quando um serviço está sendo excluído. Esse tempo limite é de 15 minutos por padrão.
* Falhas no caminho `onCloseAsync()` faz com que `onAbort()` seja chamado. Essa chamada é uma oportunidade de melhor esforço de última chance para o serviço ser limpo e liberar quaisquer recursos ocupados. Ele geralmente é chamado quando é detectada uma falha permanente no nó ou quando a malha de serviço não pode gerenciar confiavelmente o ciclo de vida da instância do serviço devido a falhas internas.
* `OnChangeRoleAsync()` é chamado quando a réplica de serviço com estado está alterando funções, por exemplo para primário ou secundário. Réplicas primárias recebem status de gravação (têm permissão para criar as Coleções Confiáveis e gravar nelas). Réplicas secundárias recebem status de leitura (podem somente ler das coleções confiáveis existentes). A maior parte do trabalho em um serviço com estado é executado na réplica primária. Réplicas secundárias podem realizar validação somente leitura, geração de relatórios, mineração de dados ou outros trabalhos somente leitura.

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Reliable Services](service-fabric-reliable-services-introduction.md)
* [Início Rápido dos Reliable Services](service-fabric-reliable-services-quick-start-java.md)

