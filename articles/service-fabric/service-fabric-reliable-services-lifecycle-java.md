---
title: "Visão geral do ciclo de vida do Azure Service Fabric Reliable Services | Microsoft Docs"
description: Saiba mais sobre os diferentes eventos de ciclo de vida no Service Fabric Reliable Services
services: Service-Fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa;
ms.openlocfilehash: 80eb68346dd05c256c60725eb082aa0651fe7cbd
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

* Durante a inicialização
  * Serviços são construídos
  * Eles têm a oportunidade de construir e retornar zero ou mais ouvintes
  * Todos os ouvintes retornados são abertos, permitindo a comunicação com o serviço
  * O método runAsync do serviço é chamado, possibilitando um serviço de longa execução ou trabalho em segundo plano
* Durante o desligamento
  * O token de cancelamento passado para o runAsync é cancelado e os ouvintes são fechados
  * Assim que estiver concluído, o objeto do serviço será destruído

Há detalhes sobre a ordem exata desses eventos. Em especial, a ordem de eventos pode mudar um pouco dependendo se o Reliable Service tem estado ou não. Além disso, para serviços com estado, temos de lidar com o cenário de troca primário. Durante esta sequência, a função da Primária é transferida para outra réplica (ou retorna) sem o desligamento do serviço. Por fim, temos de pensar sobre as condições de erro ou falha.

## <a name="stateless-service-startup"></a>Inicialização de serviço sem estado
O ciclo de vida de um serviço sem estado é bastante simples. Aqui está a ordem de eventos:

1. O Serviço foi construído
2. Em seguida, ocorrem duas coisas em paralelo:
    - `StatelessService.createServiceInstanceListeners()` é invocado e todos os ouvintes retornados serão Abertos (`CommunicationListener.openAsync()` é chamado em cada ouvinte)
    - O método runAsync (`StatelessService.runAsync()`) do serviço é chamado
3. Se existir, o próprio método onOpenAsync do serviço será chamado (especificamente, `StatelessService.onOpenAsync()` será chamado. Esta é uma substituição incomum, mas está disponível).

É importante observar que não há nenhuma ordem entre as chamadas para criar e abrir os ouvintes e o runAsync. Os ouvintes podem ser abertos antes de o runAsync ser iniciado. Da mesma forma, o runAsync pode acabar sendo invocado antes que os ouvintes de comunicação sejam abertos ou até mesmo antes de serem construídos. Se nenhuma sincronização for necessária, isso será deixado como um exercício para o implementador. Soluções comuns:

* Algumas vezes os ouvintes não funcionam até que outras informações sejam criadas ou algum trabalho seja realizado. Para serviços sem monitoração de estado, esse trabalho geralmente pode ser realizado no construtor do serviço, durante a chamada `createServiceInstanceListeners()` ou como parte da construção do ouvinte propriamente dito.
* Às vezes, o código em runAsync não é iniciado até que os ouvintes estejam abertos. Nesse caso, coordenação adicional será necessária. Uma solução comum é um sinalizador dentro dos ouvintes indicando quando eles foram concluídos, que é verificado no runAsync antes de passar para o trabalho real.

## <a name="stateless-service-shutdown"></a>Desligamento de serviço sem estado
Ao desligar um serviço sem estado, o mesmo padrão é seguido, porém na ordem inversa:

1. Em paralelo
    - Todos os ouvintes abertos são Fechados (`CommunicationListener.closeAsync()` é chamado em cada ouvinte)
    - O token de cancelamento passado para `runAsync()` é cancelado (verificar a propriedade `isCancelled` do token de cancelamento retornará true e, se chamado, o método `throwIfCancellationRequested` do token retornará um `CancellationException`)
2. Uma vez que `closeAsync()` é concluído em cada ouvinte e `runAsync()` também é finalizado, o método `StatelessService.onCloseAsync()` do serviço é chamado, se existir (novamente, essa é uma substituição incomum).
3. Depois da conclusão de `StatelessService.onCloseAsync()`, o objeto de serviço é destruído

## <a name="notes-on-service-lifecycle"></a>Observações sobre o ciclo de vida do serviço
* Tanto o método `runAsync()` quanto as chamadas `createServiceInstanceListeners` são opcionais. Um serviço pode ter um deles, ambos ou nenhum. Por exemplo, se o serviço fizer todo seu trabalho em resposta a chamadas do usuário, não será necessário implementar `runAsync()`. Apenas os ouvintes de comunicação e seu código associado são necessários. Da mesma forma, criar e retornar ouvintes de comunicação é opcional, pois o serviço pode ter apenas trabalho em segundo plano e só precisar implementar `runAsync()`
* Isso é válido para um serviço concluir `runAsync()` com êxito e retornar dele. Isso não é considerado uma condição de falha e representa o trabalho em segundo plano após o término do serviço. Para serviços confiáveis com estado, `runAsync()` seria chamado novamente se o serviço tiver sido rebaixado de primário e promovido novamente para primário.
* Se um serviço sai de `runAsync()` gerando uma exceção inesperada, há uma falha e o objeto de serviço é desligado e relatado um erro de integridade.
* Embora não haja um limite de tempo para o retorno desses métodos, você perde imediatamente a capacidade de gravar e, portanto, não pode concluir qualquer trabalho real. Recomendamos que você retorne o mais rápido possível após o recebimento da solicitação de cancelamento. Se o serviço não responder a essas chamadas à API dentro de um período razoável, o Service Fabric poderá forçar o encerramento do serviço. Geralmente isso ocorre apenas durante atualizações de aplicativo ou quando um serviço está sendo excluído. Esse tempo limite é de 15 minutos por padrão.
* Falhas no caminho de `onCloseAsync()` resultam em uma chamada de `onAbort()`, que é uma oportunidade de melhor esforço de última chance para o serviço ser limpo e liberar quaisquer recursos ocupados.

> [!NOTE]
> Ainda não há suporte para Reliable Services com monitoração de estado em java.
>
>

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Reliable Services](service-fabric-reliable-services-introduction.md)
* [Início Rápido dos Serviços Confiáveis](service-fabric-reliable-services-quick-start.md)
* [Uso avançado de Reliable Services](service-fabric-reliable-services-advanced-usage.md)
