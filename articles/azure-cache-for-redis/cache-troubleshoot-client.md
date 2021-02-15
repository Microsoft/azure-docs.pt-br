---
title: Solucionar problemas no lado do cliente do Cache do Azure para Redis
description: Saiba como resolver problemas comuns do lado do cliente com o cache do Azure para Redis, como pressão de memória do cliente Redis, intermitência de tráfego, alta CPU, largura de banda limitada, solicitações grandes ou tamanho de resposta grande.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: 122c96c95aea794fbba9cab8a9a5b867f9f34b48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008960"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Solucionar problemas no lado do cliente do Cache do Azure para Redis

Esta seção aborda a solução de problemas que ocorrem devido a uma condição no cliente Redis que seu aplicativo usa.

- [Pressão de memória no cliente Redis](#memory-pressure-on-redis-client)
- [Intermitência de tráfego](#traffic-burst)
- [Alto nível de uso da CPU do cliente](#high-client-cpu-usage)
- [Limitação de largura de banda do lado do cliente](#client-side-bandwidth-limitation)
- [Tamanho grande de solicitação ou resposta](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Pressão de memória no cliente Redis

A pressão de memória na máquina cliente leva a todos os tipos de problemas de desempenho que podem atrasar o processamento de respostas do cache. Quando a pressão de memória é alcançada, o sistema pode paginar dados em disco. Esse tipo de _falha de página_ faz com que o sistema fique significativamente mais lento.

Para detectar a pressão de memória no cliente:

- Monitore o uso de memória no computador para garantir que ele não exceda a memória disponível.
- Monitore o contador de desempenho do cliente `Page Faults/Sec` . Durante a operação normal, a maioria dos sistemas tem algumas falhas de página. Picos em falhas de página correspondentes com tempos limite de solicitação podem indicar pressão de memória.

A alta pressão de memória no cliente pode ser atenuada de várias maneiras:

- Aprofunde-se nos padrões de uso de memória para reduzir o consumo de memória no cliente.
- Atualize a VM do cliente para um tamanho maior com mais memória.

## <a name="traffic-burst"></a>Intermitência de tráfego

A intermitência de tráfego, combinada com configurações de `ThreadPool` ruins, podem resultar em atrasos no processamento de dados que já foram enviados pelo servidor Redis, mas ainda não foram consumidos no lado do cliente.

Monitore como suas `ThreadPool` estatísticas são alteradas ao longo do tempo usando [um exemplo `ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Você pode usar  `TimeoutException` mensagens de stackexchange. Redis como mostrado abaixo para investigar ainda mais:

```output
    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)
```

Na exceção anterior, há vários problemas que são interessantes:

- Observe que, na seção `IOCP` e na seção `WORKER`, você tem um valor `Busy` que é maior que o valor `Min`. Essa diferença significa que suas `ThreadPool` configurações precisam ser ajustadas.
- Você também pode ver `in: 64221`. Esse valor indica que 64.211 bytes foram recebidos na camada de soquete do kernel do cliente, mas que não foram lidos pelo aplicativo. Essa diferença normalmente significa que seu aplicativo (por exemplo, StackExchange. Redis) não está lendo dados da rede tão rapidamente quanto o servidor o está enviando para você.

Você pode [definir suas `ThreadPool` configurações](cache-management-faq.md#important-details-about-threadpool-growth) para garantir que seu pool de threads seja dimensionado rapidamente em cenários de disparo contínuo.

## <a name="high-client-cpu-usage"></a>Alto nível de uso da CPU do cliente

Alto uso de CPU do cliente indica que o sistema não pode acompanhar o trabalho que ele foi solicitado a fazer. Embora o cache tenha enviado a resposta rapidamente, o cliente pode falhar ao processar a resposta em tempo hábil.

Monitore o uso de CPU de todo o sistema do cliente usando métricas disponíveis no portal do Azure ou por meio de contadores de desempenho no computador. Tenha cuidado para não monitorar a CPU do *processo* , pois um único processo pode ter baixa utilização da CPU, mas a CPU de todo o sistema pode ser alta. Fique atento a picos de uso de CPU que correspondem aos tempos limite. A alta CPU também pode causar `in: XXX` valores altos em `TimeoutException` mensagens de erro, conforme descrito na seção de [intermitência de tráfego](#traffic-burst) .

> [!NOTE]
> O StackExchange 1.1.603 e versões posteriores incluem a métrica `local-cpu` em mensagens de erro `TimeoutException`. Certifique-se de estar usando a versão mais recente do [Pacote NuGet do StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Constantemente são corrigidos bugs no código para torná-lo mais robusto com relação a tempos limite, de modo que ter a versão mais recente é importante.
>

Para atenuar o alto uso da CPU de um cliente:

- Investigue o que está causando picos de CPU.
- Atualize seu cliente para um tamanho maior de VM com mais capacidade de CPU.

## <a name="client-side-bandwidth-limitation"></a>Limitação de largura de banda do lado do cliente

Dependendo da arquitetura dos computadores cliente, eles poderão apresentar limitações na largura de banda de rede disponível. Se o cliente exceder a largura de banda disponível sobrecarregando a capacidade da rede, os dados não serão processados no lado do cliente tão rapidamente quanto o servidor o estiver enviando. Essa situação pode resultar em tempos limite excedidos.

Monitore como o uso de largura de banda muda ao longo do tempo usando [um exemplo `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Esse código pode não ser executado corretamente em alguns ambientes com permissões restritas (como sites do Azure).

Para reduzir, reduza o consumo de largura de banda da rede ou aumente o tamanho da VM do cliente para uma com mais capacidade de rede.

## <a name="large-request-or-response-size"></a>Tamanho grande de solicitação ou resposta

Uma solicitação/resposta grande pode causar tempos limite. Por exemplo, suponha que o valor de tempo limite configurado em seu cliente seja de 1 segundo. Seu aplicativo solicita duas chaves (por exemplo, "A" e "B") ao mesmo tempo (usando a mesma conexão de rede física). A maioria dos clientes dá suporte à solicitação de "pipeline", em que as solicitações "A" e "B" são enviadas uma após a outra sem aguardar suas respostas. O servidor devolve as respostas na mesma ordem. Se a resposta ' A ' for grande, ela poderá comer a maior parte do tempo limite para solicitações posteriores.

No exemplo a seguir, a solicitação ' A ' e o ' B ' são enviados rapidamente para o servidor. O servidor começa a enviar respostas ' A ' e ' B ' rapidamente. Devido a tempos de transferência de dados, a resposta "B" deve aguardar por trás da resposta "A" expira mesmo que o servidor tenha respondido rapidamente.

```console
|-------- 1 Second Timeout (A)----------|
|-Request A-|
     |-------- 1 Second Timeout (B) ----------|
     |-Request B-|
            |- Read Response A --------|
                                       |- Read Response B-| (**TIMEOUT**)
```

Essa é uma solicitação/resposta difícil de se medir. Você pode instrumentar seu código de cliente para rastrear solicitações e respostas grandes.

As resoluções para tamanhos de resposta grandes são variadas, mas incluem:

1. Otimize seu aplicativo para um grande número de valores pequenos, em vez de alguns valores grandes.
    - A solução preferida é dividir seus dados em valores menores relacionados.
    - Confira o post [qual é o intervalo de tamanho de valor ideal para Redis? 100 KB é muito grande?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) para obter detalhes sobre por que valores menores são recomendados.
1. Aumente o tamanho de sua VM para obter mais recursos de largura de banda
    - Mais largura de banda na VM do cliente ou do servidor pode reduzir os tempos de transferência de dados para respostas maiores.
    - Compare seu uso de rede atual em ambos os computadores com os limites do tamanho atual da sua VM. Mais largura de banda somente no servidor ou somente no cliente pode não ser suficiente.
1. Aumente o número de objetos de conexão que seu aplicativo usa.
    - Use uma abordagem Round Robin para fazer solicitações em diferentes objetos de conexão.

## <a name="additional-information"></a>Informações adicionais

- [Solucionar problemas no lado do servidor do Cache do Azure para Redis](cache-troubleshoot-server.md)
- [Como medir e testar o desempenho do meu cache?](cache-management-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
