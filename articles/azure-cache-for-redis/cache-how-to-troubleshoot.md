---
title: Como solucionar problemas do Cache do Azure para Redis | Microsoft Docs
description: Saiba como solucionar problemas comuns com Cache do Azure para Redis.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/15/2019
ms.author: yegu
ms.openlocfilehash: 66361871d365068a90a2eeab70d92adb6b246a83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60830000"
---
# <a name="how-to-troubleshoot-azure-cache-for-redis"></a>Como solucionar problemas do Cache do Azure para Redis

Este artigo ajuda a solucionar problemas de diferentes categorias de problemas que você pode encontrar durante a conexão com o Cache do Azure para instâncias do Redis.

- [Solução de problemas do lado do cliente](#client-side-troubleshooting) ajuda a identificar e resolver problemas no aplicativo que se conecta ao seu cache.
- [Solução de problemas do lado do servidor](#server-side-troubleshooting) ajuda a identificar e resolver problemas que ocorrem no Cache do lado do Redis do Azure.
- [Solução de problemas de perda de dados](#data-loss-troubleshooting) ajuda a identificar e resolver incidentes em que as chaves são esperadas, mas não encontradas no seu cache.
- [Exceções de tempo limite do stackexchange. Redis](#stackexchangeredis-timeout-exceptions) fornece diretrizes específicas sobre como solucionar problemas com a biblioteca do stackexchange. Redis.

> [!NOTE]
> Várias das etapas de solução de problemas neste guia incluem instruções para executar comandos do Redis e monitorar diversas métricas de desempenho. Para obter mais informações, consulte os artigos na seção [Informações adicionais](#additional-information) .
>
>

## <a name="client-side-troubleshooting"></a>Solução de problemas do lado do cliente

Esta seção aborda a solução de problemas que ocorrem devido a alguma condição do aplicativo cliente.

- [Demanda de memória do cliente](#memory-pressure-on-the-client)
- [Intermitência de tráfego](#burst-of-traffic)
- [Alto nível de uso da CPU do cliente](#high-client-cpu-usage)
- [Largura de Banda Excedida do Lado do Cliente](#client-side-bandwidth-exceeded)
- [Solicitação/resposta grande](#large-requestresponse-size)

### <a name="memory-pressure-on-the-client"></a>Demanda de memória do cliente

Pressão de memória no computador cliente leva a todos os tipos de problemas de desempenho que podem atrasar o processamento de respostas do cache. Quando há grande demanda de memória, o sistema pode dados da página para o disco. Esse tipo de _falha de página_ faz com que o sistema fique significativamente mais lento.

Para detectar a pressão de memória no cliente:

- Monitorar o uso de memória no computador para certificar-se de que ele não exceda a memória disponível.
- Monitorar o cliente `Page Faults/Sec` contador de desempenho. Durante a operação normal, a maioria dos sistemas têm algumas falhas de página. Picos de falhas de página correspondente aos tempos limite de solicitação podem indicar pressão de memória.

Alta pressão de memória no cliente pode ser reduzido de várias maneiras:

- Aprofunde-se em seus padrões de uso de memória para reduzir o consumo de memória no cliente.
- Atualize a VM do cliente para um tamanho maior com mais memória.

### <a name="burst-of-traffic"></a>Intermitência de tráfego

A intermitência de tráfego, combinada com configurações de `ThreadPool` ruins, podem resultar em atrasos no processamento de dados que já foram enviados pelo servidor Redis, mas ainda não foram consumidos no lado do cliente.

Monitorar como seu `ThreadPool` estatísticas mudam ao longo do tempo usando [um exemplo `ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Você pode usar `TimeoutException` mensagens do stackexchange. Redis como abaixo para investigar em detalhes:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

A exceção anterior, há várias questões interessantes:

- Observe que, na seção `IOCP` e na seção `WORKER`, você tem um valor `Busy` que é maior que o valor `Min`. Essa diferença significa que seu `ThreadPool` configurações precisam de ajuste.
- Você também pode ver `in: 64221`. Esse valor indica que 64.211 bytes foram recebidos na camada de soquete de kernel do cliente, mas ainda não foram lidos pelo aplicativo. Essa diferença geralmente significa que seu aplicativo (por exemplo, stackexchange. Redis) não está lendo dados da rede rapidamente o servidor está enviando para você.

Você pode [configurar sua `ThreadPool` configurações](https://gist.github.com/JonCole/e65411214030f0d823cb) certificar-se de que seu pool de threads é dimensionado rapidamente em cenários de intermitência.

### <a name="high-client-cpu-usage"></a>Alto nível de uso da CPU do cliente

Cliente de alto uso da CPU indica que o sistema não consegue acompanhar o trabalho que ela já foi feita para fazer. Mesmo que o cache enviado a resposta rapidamente, o cliente pode falhar ao processar a resposta de forma oportuna.

Monitore o uso de CPU de todo o sistema do cliente usando as métricas disponíveis no portal do Azure ou por meio de contadores de desempenho no computador. Tenha cuidado para não monitorar *processo* CPU porque um único processo pode ter baixa utilização da CPU, mas a CPU de todo o sistema pode ser alta. Fique atento a picos de uso de CPU que correspondem aos tempos limite. Alta utilização da CPU também pode causar alto `in: XXX` os valores em `TimeoutException` mensagens de erro, conforme descrito na [intermitência de tráfego](#burst-of-traffic) seção.

> [!NOTE]
> O StackExchange 1.1.603 e versões posteriores incluem a métrica `local-cpu` em mensagens de erro `TimeoutException`. Certifique-se de estar usando a versão mais recente do [Pacote NuGet do StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Constantemente são corrigidos bugs no código para torná-lo mais robusto com relação a tempos limite, de modo que ter a versão mais recente é importante.
>
>

Para atenuar o alto uso de CPU do cliente:

- Investigar o que está causando os picos de CPU.
- Atualize o cliente para um tamanho de VM maior com mais capacidade de CPU.

### <a name="client-side-bandwidth-exceeded"></a>Largura de banda excedida do lado do cliente

Dependendo da arquitetura dos computadores cliente, eles poderão apresentar limitações na largura de banda de rede disponível. Se o cliente exceder a largura de banda disponível, sobrecarregando a capacidade de rede, os dados não não processados no lado do cliente tão rapidamente quanto o servidor está enviando. Essa situação pode resultar em tempos limite excedidos.

Monitorar como o seu uso de largura de banda mudar ao longo do tempo usando [um exemplo `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Esse código pode não ser executado corretamente em alguns ambientes com permissões restritas (como sites do Azure).

Para atenuar, reduzir o consumo de largura de banda de rede ou aumente o tamanho da VM para outro com mais capacidade de rede do cliente.

### <a name="large-requestresponse-size"></a>Solicitação/resposta grande

Uma solicitação/resposta grande pode causar tempos limite. Por exemplo, suponha que seu valor de tempo limite configurado no seu cliente é de 1 segundo. Seu aplicativo solicita duas chaves (por exemplo, "A" e "B") ao mesmo tempo (usando a mesma conexão de rede física). A maioria dos clientes oferecem suporte a solicitação "pipelining", onde ambas as solicitações 'A' e 'B' são enviadas uma após a outra sem esperar que suas respostas. O servidor devolve as respostas na mesma ordem. Se a resposta 'A' é grande, ela poderá consumir a maior parte do tempo limite para solicitações posteriores.

No exemplo a seguir, a solicitação 'A' e 'B' são enviadas rapidamente para o servidor. O servidor começa a enviar respostas 'A' e 'B' rapidamente. Por causa de tempos de transferência de dados, 'B' deve aguardar por trás de resposta 'A' tempos de resposta-out, mesmo que o servidor respondeu rapidamente.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

Essa é uma solicitação/resposta difícil de se medir. Você pode instrumentar seu código de cliente para acompanhar grandes solicitações e respostas.

Resoluções para tamanhos grandes de resposta variam, mas incluem:

1. Otimize seu aplicativo para um grande número de valores pequenos, em vez de alguns valores grandes.
    - A solução preferida é dividir seus dados em valores menores relacionados.
    - Consulte a postagem [o que é o intervalo de tamanho do valor ideal para redis? 100 KB é muito grande? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) para obter detalhes sobre por que valores menores são recomendados.
1. Aumentar o tamanho da sua VM para obter recursos de largura de banda superiores
    - Mais largura de banda em seu cliente ou a VM do servidor pode reduzir o tempo de transferência de dados para respostas maiores.
    - Compare o uso atual da rede em ambos os computadores para os limites do tamanho atual da sua VM. Mais largura de banda somente no servidor ou apenas no cliente pode não ser suficiente.
1. Aumente o número de objetos de conexão que seu aplicativo usa.
    - Use uma abordagem de round-robin para fazer solicitações sobre objetos de conexão diferente.

## <a name="server-side-troubleshooting"></a>Solução de problemas do lado do servidor

Esta seção aborda a solução de problemas que ocorrem devido a alguma condição do servidor de cache.

- [Demanda de memória do servidor](#memory-pressure-on-the-server)
- [Alto nível de uso da CPU/carga de servidor](#high-cpu-usage--server-load)
- [Largura de banda do servidor ultrapassada](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Demanda de memória do servidor

Uma grande demanda da memória do servidor acarreta todo tipo de problemas de desempenho, que podem atrasar o processamento de solicitações. Quando há grande demanda de memória, o sistema pode dados da página para o disco. Esse tipo de _falha de página_ faz com que o sistema fique significativamente mais lento. Há diversas causas possíveis para essa demanda de memória:

- O cache é preenchido com dados perto de sua capacidade máxima.
- Redis está passando a fragmentação de memória alta. Essa fragmentação geralmente é causada pelo armazenamento de objetos grandes, pois o Redis é otimizado para objetos pequenos.

O redis expõe duas estatísticas por meio de [INFO](https://redis.io/commands/info) comando que pode ajudá-lo a identificar esse problema: "used_memory" e "used_memory_rss". Você pode [exibir essas métricas](cache-how-to-monitor.md#view-metrics-with-azure-monitor) usando o portal.

Há várias alterações possíveis que você pode fazer para ajudar a manter o uso de memória Íntegro:

- [Configurar uma política de memória](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) e definir tempos de expiração em suas chaves. Essa política pode não ser suficiente se você tiver fragmentação.
- [Configurar um valor para maxmemory-reserved](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) que seja grande o suficiente para compensar pela fragmentação da memória. Para obter mais informações, consulte adicional [considerações para reservas de memória](#considerations-for-memory-reservations) abaixo.
- Dividir os objetos grandes armazenados em cache em objetos menores relacionados.
- [Criar alertas](cache-how-to-monitor.md#alerts) em métricas como memória usada para ser notificado no início sobre impactos em potencial.
- [Escala](cache-how-to-scale.md) para um tamanho de cache maior com mais capacidade de memória.

#### <a name="considerations-for-memory-reservations"></a>Considerações para reservas de memória

Atualizar valores de reserva de memória, como a maxmemory-reserved, podem afetar o desempenho do cache. Suponha que você tenha um cache de 53 GB que é preenchido com 49 GB de dados. Alterando o valor de reserva para 8 GB descarta a memória máxima disponível do sistema para 45 GB. Se _used_memory_ ou _used_memory_rss_ valores são maiores do que 45 GB, o sistema pode remover os dados até que ambas _used_memory_ e _used_memory_rss_ estão abaixo de 45 GB. A remoção pode aumentar a fragmentação de memória e carregamento do servidor.

### <a name="high-cpu-usage--server-load"></a>Alto nível de uso da CPU/carga de servidor

Uma carga alta do servidor ou o uso da CPU significa que o servidor não pode processar solicitações de forma oportuna. O servidor pode ser impossível de acompanhar com taxas de solicitação e responde lentamente.

[Monitorar as métricas](cache-how-to-monitor.md#view-metrics-with-azure-monitor) , como carga de CPU ou de servidor. Fique atento a picos de uso de CPU que correspondem aos tempos limite.

Há várias alterações que você pode fazer para reduzir a carga do servidor de alta:

- Investigar o que está causando os picos de CPU como executar [comandos caros](#expensive-commands) ou página com falha devido à pressão de memória alta.
- [Criar alertas](cache-how-to-monitor.md#alerts) em métricas como carga de CPU ou de servidor sejam notificados antecipadamente impactos em potencial.
- [Escala](cache-how-to-scale.md) para um tamanho de cache maior com mais capacidade de CPU.

#### <a name="expensive-commands"></a>Comandos caros

Nem todos os comandos do Redis são criados igualmente – algumas são mais caras de serem executados que outros. O [documentação de comandos do Redis](https://redis.io/commands) mostra a complexidade de tempo de cada comando. É recomendável que você revise os comandos que você estiver executando em seu cache para entender o impacto no desempenho desses comandos. Por exemplo, o [chaves](https://redis.io/commands/keys) comando geralmente é usado sem saber o que é uma operação de (n). Você pode evitar chaves usando [SCAN](https://redis.io/commands/scan) reduzir a CPU picos.

Usando o [SLOWLOG](https://redis.io/commands/slowlog) de comando, você pode medir comandos caros que está sendo executados em relação ao servidor.

### <a name="server-side-bandwidth-exceeded"></a>Largura de banda do lado do servidor ultrapassada

Tamanhos de cache diferentes têm capacidades de largura de banda de rede diferente. Se o servidor excede a largura de banda disponível, os dados não serão enviados ao cliente tão rapidamente. Solicitações de clientes podiam atingir o tempo limite porque o servidor não pode enviar dados por push para o cliente rápido o suficiente.

As métricas de "Cache de leitura" e "Cache de gravação" podem ser usadas para ver quanta largura de banda do lado do servidor está sendo usada. Você pode [exibir essas métricas](cache-how-to-monitor.md#view-metrics-with-azure-monitor) no portal.

Para atenuar as situações em que o uso de largura de banda de rede é próximo à capacidade máxima:

- Alterar o comportamento de chamada do cliente para reduzir a demanda de rede.
- [Criar alertas](cache-how-to-monitor.md#alerts) em métricas como cache de leitura ou gravação no cache para ser notificado no início sobre impactos em potencial.
- [Escala](cache-how-to-scale.md) para um tamanho de cache maior com mais capacidade de largura de banda de rede.

## <a name="data-loss-troubleshooting"></a>Solução de problemas de perda de dados

Eu esperava que certos dados estivessem em minha instância do Cache do Azure para Redis, mas parece que não estão lá.

Consulte [What happened to my data in Redis? (O que aconteceu com meu dados no Redis?)](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) para ver as possíveis causas e resoluções.

## <a name="stackexchangeredis-timeout-exceptions"></a>Exceções de tempo limite do StackExchange.Redis

Stackexchange. Redis usa uma configuração chamada `synctimeout` para operações síncronas com um valor padrão de 1000 ms. Se uma chamada síncrona não for concluída nesse período, o cliente stackexchange. Redis gerará um erro de tempo limite semelhante ao exemplo a seguir:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

Essa mensagem de erro contém métricas que podem ajudar a indicar a causa e a possível resolução do problema. A tabela a seguir contém detalhes sobre as métricas da mensagem de erro.

| Métrica da mensagem de erro | Detalhes |
| --- | --- |
| inst |Na última fração de tempo: 0 comandos foram emitidos |
| mgr |O Gerenciador do soquete está fazendo `socket.select`, que significa que ele está solicitando que o sistema operacional indique um soquete que tem algo a fazer. O leitor não está lendo ativamente da rede porque ele acha que não há nada a fazer |
| fila |Existem 73 operações em andamento no total |
| qu |6 das operações em andamento estão na fila não enviada e ainda ainda não foram escritos para a rede de saída |
| qs |67 das operações em andamento foram enviadas ao servidor, mas uma resposta ainda não está disponível. A resposta pode ser `Not yet sent by the server` ou `sent by the server but not yet processed by the client.` |
| qc |0 das operações em andamento viram respostas, mas ainda não foram marcados como concluídas porque está aguardando do loop de conclusão |
| wr |Há um gravador ativo (ou seja, que as 6 solicitações não enviadas não estão sendo ignoradas) bytes/activewriters |
| mergulhar |Não há nenhum leitor ativo e zero bytes estão disponíveis para serem lidos no NIC bytes/activereaders |

### <a name="steps-to-investigate"></a>Etapas para investigar

1. Como prática recomendada, verifique se que você estiver usando o seguinte padrão para se conectar ao usar o cliente stackexchange. Redis.

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

    Para saber mais, confira [Conectar-se ao cache usando StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Verifique se seu servidor e o aplicativo cliente na mesma região no Azure. Por exemplo, você pode receber tempos limite quando o cache está no Leste dos EUA, mas o cliente está no Oeste dos EUA e a solicitação não for concluída dentro do `synctimeout` intervalo ou você pode receber tempos limite quando você estiver depurando em seu computador de desenvolvimento local. 

    É altamente recomendável ter o cache e o cliente na mesma região do Azure. Se você tem um cenário que inclui chamadas entre regiões, é necessário definir o intervalo de `synctimeout` como um valor maior que o intervalo padrão de 1.000 ms, incluindo uma propriedade `synctimeout` na cadeia de conexão. O exemplo a seguir mostra um trecho de uma cadeia de caracteres de conexão para o stackexchange. Redis fornecidos pelo Cache do Azure para Redis com um `synctimeout` de 2000 ms.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. Certifique-se de estar usando a versão mais recente do [Pacote NuGet do StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Constantemente são corrigidos bugs no código para torná-lo mais robusto com relação a tempos limite, de modo que ter a versão mais recente é importante.
1. Se suas solicitações são associadas por limitações de largura de banda no servidor ou cliente, levará mais tempo para que eles possam concluir e pode causar tempos limite. Para ver se o tempo limite devido à largura de banda de rede no servidor, consulte [largura de banda do lado do servidor ultrapassada](#server-side-bandwidth-exceeded). Para ver se o tempo limite devido à largura de banda de rede de cliente, consulte [largura de banda do lado do cliente ultrapassada](#client-side-bandwidth-exceeded).
1. Você está sendo limitado à CPU no servidor ou no cliente?

   - Verifique se você está sendo limitado pela CPU no cliente. Alta utilização da CPU pode fazer com que a solicitação não seja processada dentro do `synctimeout` intervalo e causa uma solicitação atinja o tempo limite. Mover para um tamanho de cliente maior ou distribuir a carga pode ajudar a controlar esse problema.
   - Verifique se você estiver recebendo CPU associada ao servidor monitorando a `CPU` [métrica de desempenho de cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Solicitações recebidas enquanto o Redis está vinculado à CPU podem causar essas solicitações para o tempo limite. Para resolver essa condição, você pode distribuir a carga entre vários fragmentos em um cache Premium ou atualizar para um tipo de preço ou tamanho maior. Para obter mais informações, consulte [Largura de banda do servidor ultrapassada](#server-side-bandwidth-exceeded).
1. Há comandos que levam muito tempo para serem processados no servidor? Comandos de execução longa que levam muito tempo para serem processados no servidor do redis podem resultar em tempos limite. Para obter mais informações sobre os comandos de longa execução, consulte [comandos caros](#expensive-commands). Você pode se conectar ao seu Cache do Azure para instância do Redis usando o cliente redis-cli ou o [Console do Redis](cache-configure.md#redis-console). Em seguida, execute as [SLOWLOG](https://redis.io/commands/slowlog) comando para ver se há solicitações mais lentamente do que o esperado. O Servidor do Redis e o StackExchange.Redis são otimizados para várias solicitações pequenas, em vez de menos solicitações grandes. Dividir os dados em partes menores pode melhorar as coisa.

    Para obter informações sobre como se conectar ao ponto de extremidade SSL do seu cache usando o redis-cli e stunnel, consulte o postagem no blog [anunciando o ASP.NET Session State Provider para versão de visualização do Redis](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).
1. Uma carga alta no servidor Redis pode resultar em tempos limite. Você pode monitorar a carga do servidor monitorando a [métrica de desempenho de cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Redis Server Load`. Uma carga de servidor de 100 (valor máximo) significa que o servidor Redis está ocupado, sem tempo ocioso, processando de solicitações. Para ver se determinadas solicitações estão consumindo toda a capacidade do servidor, execute o comando SlowLog, conforme descrito no parágrafo anterior. Para obter mais informações, confira Alto nível de uso da CPU/carga de servidor.
1. Houve qualquer outro evento no lado do cliente que possa ter causado um problema na rede? Eventos comuns incluem: dimensionamento do número de instâncias de cliente para cima ou para baixo, implantando uma nova versão do cliente ou do dimensionamento automático habilitado. Em nossos testes, descobrimos que a AutoEscala ou escalar/reduzir verticalmente pode causar conectividade de rede de saída seja perdida por alguns segundos. O código do StackExchange.Redis é resiliente a tais eventos e se reconectará. Durante a reconexão, quaisquer solicitações na fila podem atingir o tempo limite.
1. Houve uma grande solicitação anterior várias pequenas solicitações para o cache que atingiu o tempo limite? O parâmetro `qs` no erro mensagem informa quantas solicitações foram enviadas do cliente ao servidor, mas ainda não processaram uma resposta. Esse valor pode continuar crescendo porque o StackExchange.Redis usa uma única conexão TCP e só pode ler uma resposta por vez. Mesmo que a primeira operação atingiu o tempo limite, ele não para mais dados sejam enviados para ou do servidor. Outras solicitações serão bloqueadas até que a grande solicitação seja concluída e pode causar tempos limite. Uma solução é minimizar a chance de tempos limite, garantindo que o cache seja grande o suficiente para sua carga de trabalho e dividindo valores grandes em partes menores. Outra solução possível é usar um pool de objetos `ConnectionMultiplexer` no seu cliente e escolher o `ConnectionMultiplexer` menos carregado ao enviar uma nova solicitação. Carregando a vários objetos de conexão deve impedir que um único tempo limite fazendo com que outras solicitações também o tempo limite.
1. Se você estiver usando `RedisSessionStateProvider`, verifique se você configurou corretamente o tempo limite de repetição. `retryTimeoutInMilliseconds` deve ser maior do que `operationTimeoutInMilliseconds`; caso contrário, não há novas tentativas. No exemplo a seguir, `retryTimeoutInMilliseconds` é definido como 3000. Para obter mais informações, consulte [provedor de estado de sessão ASP.NET para Cache do Azure para Redis](cache-aspnet-session-state-provider.md) e [Como usar os parâmetros de configuração do provedor de estado de sessão e do provedor de cache de saída](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

    ```xml
    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />
    ```

1. Verifique o uso de memória no servidor do Cache do Azure para Redis [monitorando](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` e `Used Memory`. Se uma política de remoção estiver em vigor, o Redis começará a remover chaves quando `Used_Memory` atingir o tamanho do cache. Idealmente, `Used Memory RSS` deve ser apenas um pouco maior do que `Used memory`. Uma grande diferença significa que há fragmentação de memória (internos ou externos). Quando `Used Memory RSS` é menor que `Used Memory`, significa que parte da memória cache foi trocada pelo sistema operacional. Se a troca ocorrer, você poderá esperar que haja algumas latências significativas. Porque o Redis não tem controle sobre como suas alocações são mapeadas para páginas de memória alta `Used Memory RSS` costuma ser o resultado de um aumento no uso de memória. Quando o servidor Redis libera memória, o alocador leva a memória, mas ele pode ou não pode fornecer a memória novamente ao sistema. Pode haver uma discrepância entre o valor de `Used Memory` e o consumo de memória, conforme relatado pelo sistema operacional. Memória pode ter foi usada e liberada pelo Redis, mas não fornecida de volta para o sistema. Para ajudar a atenuar problemas de memória, você pode fazer as seguintes etapas:

   - Atualize o cache para um tamanho maior para que não estão em execução em relação a limitações de memória no sistema.
   - Definir tempos de expiração das chaves para que valores mais antigos sejam removidos de forma proativa.
   - Monitorar a métrica de cache `used_memory_rss`. Quando esse valor se aproximar do tamanho de seu cache, você provavelmente começar a ver problemas de desempenho. Distribua os dados entre vários fragmentos, se você estiver usando um cache premium ou atualizar para um tamanho de cache maior.

   Para obter mais informações, confira [Demanda de memória do servidor](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Informações adicionais

- [Qual oferta de Cache do Azure para Redis e tamanho eu devo usar?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Como medir e testar o desempenho do meu cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Como posso executar comandos do Redis?](cache-faq.md#how-can-i-run-redis-commands)
- [Como monitorar o Cache do Azure para Redis](cache-how-to-monitor.md)
