---
title: Perguntas frequentes sobre o cache do Azure para gerenciamento Redis
description: Conheça as respostas a perguntas comuns que ajudam a gerenciar o cache do Azure para Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: 15c7ed4ca9d04e4bb314eea8b92bef749d2369b1
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537653"
---
# <a name="azure-cache-for-redis-management-faqs"></a>Perguntas frequentes sobre o cache do Azure para gerenciamento Redis
Este artigo fornece respostas a perguntas comuns sobre como gerenciar o cache do Azure para Redis.

## <a name="common-questions-and-answers"></a>Perguntas comuns e respostas
Esta seção aborda as seguintes perguntas frequentes:

* [Quando devo habilitar a porta não TLS/SSL para conexão ao Redis?](#when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis)
* [Quais são algumas práticas recomendadas de produção?](#what-are-some-production-best-practices)
* [Quais são algumas das considerações ao usar os comandos comuns do Redis?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Como medir e testar o desempenho do meu cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Detalhes importantes sobre o crescimento de ThreadPool](#important-details-about-threadpool-growth)
* [Habilitar a GC (coleta de lixo) do servidor para obter mais produtividade no cliente ao usar o StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Considerações sobre desempenho em torno de conexões](#performance-considerations-around-connections)

### <a name="when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis"></a>Quando devo habilitar a porta não TLS/SSL para conexão ao Redis?
O servidor Redis não oferece suporte nativo a TLS, mas o Cache do Azure para Redis sim. Se você estiver se conectando ao Cache do Azure para Redis, e seu cliente oferecer suporte a TLS, como StackExchange.Redis, você deverá usar TLS.

>[!NOTE]
>A porta não TLS está desabilitada por padrão para novas instâncias do Cache do Azure para Redis. Se o cliente não oferecer suporte a TLS, você deverá habilitar a porta não TLS seguindo as instruções na seção [Portas de acesso](cache-configure.md#access-ports) do artigo [Configurar um cache no Cache do Azure para Redis](cache-configure.md).
>
>

Ferramentas do Redis como o `redis-cli` não funcionam com a porta TLS, mas você pode usar um utilitário como `stunnel` para conectar as ferramentas com segurança à porta TLS seguindo as instruções na postagem de blog [Anunciando o provedor de estado de sessão ASP.NET para versão de visualização do Redis](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/).

Para obter instruções sobre como baixar as ferramentas do Redis, consulte a seção [Como posso executar comandos do Redis?](cache-development-faq.md#how-can-i-run-redis-commands)

### <a name="what-are-some-production-best-practices"></a>Quais são algumas práticas recomendadas de produção?
* [Práticas recomendadas do StackExchange.Redis](#stackexchangeredis-best-practices)
* [Configuração e conceitos](#configuration-and-concepts)
* [Testes de desempenho](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Práticas recomendadas do StackExchange.Redis
* Defina `AbortConnect` como false e deixe o ConnectionMultiplexer se reconectar automaticamente. [Consulte aqui para obter detalhes](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Reutilize o ConnectionMultiplexer – não crie um novo para cada solicitação. O padrão `Lazy<ConnectionMultiplexer>`[mostrado aqui](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) é recomendável.
* O Redis funciona melhor com valores menores, portanto, considere talhar dados maiores em várias chaves. Nesta [discussão do Redis](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 KB é considerado grande. Leia [este artigo](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) para obter um problema de exemplo que pode ser causado por valores grandes.
* Configure suas [configurações de ThreadPool](#important-details-about-threadpool-growth) para evitar tempos limites.
* Use pelo menos o connectTimeout padrão de 5 segundos. Esse intervalo dá ao StackExchange.Redis tempo suficiente para restabelecer a conexão, no caso de um blip de rede.
* Lembre-se dos custos de desempenho associados a operações diferentes que você está executando. Por exemplo, o `KEYS` comando é uma operação O(n) e deve ser evitado. O [site redis.io](https://redis.io/commands/) apresenta detalhes sobre a complexidade de tempo para cada operação a qual ele dá suporte. Clique em cada comando para ver a complexidade para cada operação.

#### <a name="configuration-and-concepts"></a>Configuração e conceitos
* Use as camadas Standard ou Premium para Sistemas de Produção. A camada Básica é um sistema de nó único sem replicação de dados e sem SLA. Além disso, use pelo menos um cache C1. Caches C0 normalmente são usados para cenários de desenvolvimento/teste simples.
* Lembre-se de que o Redis é um armazenamento de dados **na memória** . Leia [este artigo](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) para que você fique ciente de situações em que pode haver perda de dados.
* Desenvolva seu sistema, de modo que possa lidar com blips de conexão [devido à aplicação de patch e ao failover](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Testes de desempenho
* Comece usando `redis-benchmark.exe` para ter uma ideia de taxa de transferência possível antes de escrever seus próprios testes de desempenho. Como `redis-benchmark` não dá suporte a TLS, você deve [habilitar a porta não TLS no portal do Azure](cache-configure.md#access-ports) antes de executar o teste. Por exemplo, consulte [Como medir e testar o desempenho do meu cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* A VM do cliente usada para testes deve estar na mesma região que a instância do Cache do Azure para Redis.
* É recomendável usar a Série de VM Dv2 série para o cliente, pois ela tem hardware melhor e deve oferecer melhores resultados.
* Verifique se a VM do cliente escolhida tem ao menos a quantidade de computação e funcionalidade de largura de banda de cache que você está testando.
* Habilite o VRSS no computador cliente, se você estiver usando o Windows. [Consulte aqui para obter detalhes](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383582(v=ws.11)).
* As instâncias do Redis nível Premium terão melhor latência de rede e taxa de transferência porque estão em execução tanto para a CPU quanto para a Rede.

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Quais são algumas das considerações ao usar os comandos comuns do Redis?

* Evite usar determinados comandos do Redis que demoram muito para serem concluídos, a menos que você entenda bem o impacto desses comandos. Por exemplo, não execute o comando [KEYS](https://redis.io/commands/keys) em produção. Dependendo do número de chaves, o retorno pode levar muito tempo. O Redis é um servidor de thread único e processa um comando por vez. Se você emitir outros comandos após CHAVES, eles não serão processados até que o Redis processe o comando CHAVES. O [site redis.io](https://redis.io/commands/) apresenta detalhes sobre a complexidade de tempo para cada operação a qual ele dá suporte. Clique em cada comando para ver a complexidade para cada operação.
* Tamanhos de chave - devo usar valores pequenos ou grandes de chave? Depende do cenário. Se seu cenário exigir chaves maiores, você poderá ajustar o ConnectionTimeout e, depois, repetir os valores e ajustar sua lógica de repetição de tentativas. Da perspectiva do servidor Redis, valores menores apresentam um desempenho melhor.
* Essas considerações não significam que você não pode armazenar valores maiores em Redis, mas que deve estar ciente das considerações a seguir. As latências serão maiores. Se você tiver um conjunto de dados maior e um menor, você pode usar várias instâncias do ConnectionMultiplexer, cada uma configurada com um conjunto diferente de valores de tempo limite e de repetição de tentativa, conforme descrito anteriormente na seção [O que as opções de configuração de StackExchange.Redis fazem](cache-development-faq.md#what-do-the-stackexchangeredis-configuration-options-do) .

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Como medir e testar o desempenho do meu cache?
* [Habilite o diagnóstico de cache](cache-how-to-monitor.md#enable-cache-diagnostics) para que você possa [monitorar](cache-how-to-monitor.md) a integridade do cache. Você pode exibir as métricas no portal do Azure e também pode [baixá-las e analisá-las](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) usando as ferramentas de sua escolha.
* Você pode usar o redis-benchmark.exe para testar a carga em seu servidor do Redis.
* Verifique se o cliente de teste de carga e o Cache do Azure para Redis estão na mesma região.
* Use o redis cli.exe e monitore o cache usando o comando INFO.
* Se sua carga está causando um nível elevado de fragmentação de memória, você deve escalar verticalmente para um tamanho de cache maior.
* Para obter instruções sobre como baixar as ferramentas do Redis, consulte a seção [Como posso executar comandos do Redis?](cache-development-faq.md#how-can-i-run-redis-commands)

Os comandos a seguir fornecem um exemplo de uso benchmark.exe redis. Para obter resultados precisos, execute esses comandos de uma VM na mesma região que o cache.

* Teste solicitações SET de Pipeline usando um conteúdo de 1k

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Solicitações GET de Pipelined usando uma conteúdo de 1k.

>[!NOTE]
> primeiro execute o teste SET abaixo para popular o cache
>

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

### <a name="important-details-about-threadpool-growth"></a>Detalhes importantes sobre o crescimento de ThreadPool
O ThreadPool do CLR tem dois tipos de threads: "Trabalho" e "Porta de conclusão E/S" (IOCP).

* Os threads de trabalho são usados em situações como o processamento dos métodos `Task.Run(…)` ou `ThreadPool.QueueUserWorkItem(…)`. Esses threads também são usados por vários componentes no CLR quando o trabalho precisa ser executado em um thread em segundo plano.
* Os threads IOCP são usados quando há E/S assíncrona, como durante a leitura da rede.

O pool de threads fornece novos threads de trabalho ou de conclusão de E/S sob demanda (sem qualquer limitação) até atingir a configuração de "Mínimo" para cada tipo de thread. Por padrão, o número mínimo de threads é definido como o número de processadores em um sistema.

Depois que o número de threads existentes (ocupados) atinge o número "mínimo" de threads, o ThreadPool limitará a taxa à qual injeta novos threads em um thread por 500 milissegundos. Geralmente, se seu sistema obtiver um pico de trabalho que precisa de um thread IOCP, ele processará esse trabalho rapidamente. No entanto, se o pico de trabalho for maior que a configuração "Mínimo", haverá algum atraso no processamento do trabalho, já que o ThreadPool aguarda um dentre dois acontecimentos.

* Um thread existente ficar livre para processar o trabalho.
* Nenhum thread existente ficar livre por 500 ms, o que leva à criação de outro thread.

Basicamente, isso significa que quando o número de threads ocupados é maior que o mínimo de threads, você provavelmente está pagando um atraso de 500 ms antes que o tráfego de rede seja processado pelo aplicativo. Além disso, é importante observar que, quando um thread existente permanece ocioso por mais de 15 segundos (se não me falha a memória), ele é limpo e esse ciclo de crescimento e redução pode ser repetido.

Se examinarmos um exemplo de mensagem de erro do StackExchange.Redis (versão 1.0.450 ou posterior), você verá que agora ela imprime estatísticas de ThreadPool (confira os detalhes de TRABALHO e IOCP abaixo).

```
System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
IOCP: (Busy=6,Free=994,Min=4,Max=1000),
WORKER: (Busy=3,Free=997,Min=4,Max=1000)
```

No exemplo anterior, você pode ver que, para o thread IOCP, há seis threads ocupados, e o sistema está configurado para permitir o mínimo de quatro threads. Nesse caso, o cliente provavelmente veria dois atrasos de 500 ms porque 6 > 4.

Observe que o StackExchange.Redis poderá atingir tempos limite se o crescimento de threads de TRABALHO ou IOCP for limitado.

#### <a name="recommendation"></a>Recomendação

Dadas essas informações, recomendamos fortemente que os clientes definam o valor de configuração mínima para threads de TRABALHO e IOCP com um valor maior que o valor padrão. Não podemos dar uma orientação que sirva para todos sobre esse valor porque o valor correto para um aplicativo provavelmente será muito alto ou baixo para outro aplicativo. Essa configuração também pode afetar o desempenho de outras partes do aplicativos complicados; portanto, cada cliente precisa ajustar essa configuração para as suas necessidades específicas. Um bom ponto de partida é 200 ou 300, para testar e ajustar conforme necessário.

Como definir essa configuração:

* É recomendável alterar essa configuração programaticamente usando o método [ThreadPool.SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) no `global.asax.cs`. Por exemplo:

    ```csharp
    private readonly int minThreads = 200;
    void Application_Start(object sender, EventArgs e)
    {
        // Code that runs on application startup
        AreaRegistration.RegisterAllAreas();
        RouteConfig.RegisterRoutes(RouteTable.Routes);
        BundleConfig.RegisterBundles(BundleTable.Bundles);
        ThreadPool.SetMinThreads(minThreads, minThreads);
    }
    ```

    > [!NOTE]
    > O valor especificado por esse método é uma configuração global que afeta todo o AppDomain. Por exemplo, se você tiver uma máquina com quatro núcleos e desejar definir as configurações *minWorkerThreads* e *minIoThreads* como 50 por CPU durante o tempo de execução, use **ThreadPool.SetMinThreads (200, 200)** .

* Também é possível especificar a configuração de threads mínimos usando a configuração [*minIoThreads* ou *minWorkerThreads*](/previous-versions/dotnet/netframework-4.0/7w2sway1(v=vs.100)) no elemento de configuração `<processModel>` no `Machine.config`, geralmente localizado em `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\`. **Definir o número de threads mínimos assim geralmente não é recomendado, pois é uma configuração de todo o sistema.**

  > [!NOTE]
  > O valor especificado nesse elemento de configuração é uma *configuração por núcleo* . Por exemplo, se você tivesse um computador com quatro núcleos e desejar que sua configuração *minIoThreads* seja de 200 no runtime, usaria `<processModel minIoThreads="50"/>`.
  >

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Habilitar a GC (coleta de lixo) do servidor para obter mais produtividade no cliente ao usar o StackExchange.Redis
Habilitar a GC do servidor pode otimizar o cliente e proporcionar melhor desempenho e produtividade ao usar o Stackexchange.Redis. Para saber mais sobre a GC do servidor e como habilitá-la, confira os artigos a seguir:

* [Para habilitar a GC (coleta de lixo) do servidor](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Noções básicas sobre a coleta de lixo](/dotnet/standard/garbage-collection/fundamentals)
* [Coleta de lixo e desempenho](/dotnet/standard/garbage-collection/performance)

### <a name="performance-considerations-around-connections"></a>Considerações sobre desempenho em torno de conexões

Cada tipo de preço tem limites diferentes para conexões de cliente, memória e largura de banda. Embora cada tamanho de cache permita *até* um determinado número de conexões, cada conexão com o Redis tem sobrecarga associadas. Um exemplo de tal sobrecarga seria o uso da CPU e de memória como resultado de criptografia TLS/SSL. O limite máximo de conexão para um tamanho de cache determinado pressupõe um cache com pouca carga. Se a carga da conexão de sobrecarga *mais* a carga de operações do cliente excederem a capacidade do sistema, o cache poderá enfrentar problemas de capacidade mesmo se não exceder o limite de conexão para o tamanho atual do cache.

Para obter mais informações sobre os limites de conexões diferentes para cada camada, consulte [preços do Cache do Azure para Redis](https://azure.microsoft.com/pricing/details/cache/). Para obter mais informações sobre as conexões e outras configurações padrão, consulte [configuração do servidor padrão Redis](cache-configure.md#default-redis-server-configuration).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre outros [cache do Azure para perguntas frequentes](cache-faq.md)sobre o Redis.