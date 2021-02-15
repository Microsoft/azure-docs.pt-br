---
title: Solucionar problemas de tempo limite do Cache do Azure para Redis
description: Saiba como resolver problemas comuns de tempo limite com o cache do Azure para Redis, como a aplicação de patches do Redis Server e as exceções de tempo limite de StackExchange. Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 10/18/2019
ms.openlocfilehash: bf8b20dadd2fcd78657aa6877e796b645332dd94
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88213453"
---
# <a name="troubleshoot-azure-cache-for-redis-timeouts"></a>Solucionar problemas de tempo limite do Cache do Azure para Redis

Esta seção aborda a solução de problemas de tempo limite que ocorrem durante a conexão com o cache do Azure para Redis.

- [Aplicação de patch do servidor Redis](#redis-server-patching)
- [Exceções de tempo limite do StackExchange.Redis](#stackexchangeredis-timeout-exceptions)

> [!NOTE]
> Várias das etapas de solução de problemas neste guia incluem instruções para executar comandos do Redis e monitorar diversas métricas de desempenho. Para obter mais informações, consulte os artigos na seção [Informações adicionais](#additional-information) .
>

## <a name="redis-server-patching"></a>Aplicação de patch do servidor Redis

O cache do Azure para Redis atualiza regularmente seu software de servidor como parte da funcionalidade de serviço gerenciado que ele fornece. Essa atividade de [aplicação de patch](cache-failover.md) ocorre em grande parte por trás da cena. Durante os failovers quando os nós de servidor Redis estão sendo corrigidos, os clientes Redis conectados a esses nós podem experimentar tempos limite temporários conforme as conexões são alternadas entre esses nós. Veja [como um failover afeta meu aplicativo cliente](cache-failover.md#how-does-a-failover-affect-my-client-application) para obter mais informações sobre quais patches de efeitos colaterais podem ter em seu aplicativo e como você pode melhorar sua manipulação de eventos de aplicação de patches.

## <a name="stackexchangeredis-timeout-exceptions"></a>Exceções de tempo limite do StackExchange.Redis

StackExchange. Redis usa um parâmetro de configuração chamado `synctimeout` para operações síncronas com um valor padrão de 5000 MS. Se uma chamada síncrona não for concluída neste momento, o cliente StackExchange. Redis gera um erro de tempo limite semelhante ao exemplo a seguir:

```output
    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)
```

Essa mensagem de erro contém métricas que podem ajudar a indicar a causa e a possível resolução do problema. A tabela a seguir contém detalhes sobre as métricas da mensagem de erro.

| Métrica da mensagem de erro | Detalhes |
| --- | --- |
| inst |Na última fração de tempo: 0 comandos foram emitidos |
| mgr |O Gerenciador de soquete está fazendo `socket.select` , o que significa que ele está solicitando que o sistema operacional indique um soquete que tenha algo a fazer. O leitor não está lendo ativamente da rede porque não parece que há algo a fazer |
| fila |Existem 73 operações em andamento no total |
| qu |6 das operações em andamento estão na fila não enviada e ainda não foram gravadas na rede de saída |
| qs |67 das operações em andamento foram enviadas para o servidor, mas uma resposta ainda não está disponível. A resposta pode ser `Not yet sent by the server` ou `sent by the server but not yet processed by the client.` |
| qc |0 das operações em andamento viram respostas, mas ainda não foram marcadas como concluídas porque estão aguardando o loop de conclusão |
| wr |Há um gravador ativo (o que significa que as 6 solicitações não enviadas não estão sendo ignoradas) bytes/activewriters |
| in |Não há nenhum leitor ativo e zero bytes estão disponíveis para serem lidos no NIC bytes/activereaders |

Você pode usar as etapas a seguir para investigar possíveis causas raiz.

1. Como prática recomendada, verifique se você está usando o padrão a seguir para se conectar ao usar o cliente StackExchange. Redis.

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

1. Verifique se o servidor e o aplicativo cliente estão na mesma região no Azure. Por exemplo, você pode estar recebendo tempos limite quando o cache está no leste dos EUA, mas o cliente está no oeste dos EUA e a solicitação não é concluída dentro do `synctimeout` intervalo ou você pode estar recebendo tempos limite quando estiver Depurando de seu computador de desenvolvimento local. 

    É altamente recomendável ter o cache e o cliente na mesma região do Azure. Se você tiver um cenário que inclui chamadas entre regiões, defina o `synctimeout` intervalo como um valor maior que o intervalo 5000-MS padrão, incluindo uma `synctimeout` Propriedade na cadeia de conexão. O exemplo a seguir mostra um trecho de uma cadeia de conexão para StackExchange. Redis fornecido pelo cache do Azure para Redis com um `synctimeout` de 2000 MS.

    ```output
    synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
    ```

1. Certifique-se de estar usando a versão mais recente do [Pacote NuGet do StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Constantemente são corrigidos bugs no código para torná-lo mais robusto com relação a tempos limite, de modo que ter a versão mais recente é importante.
1. Se suas solicitações estiverem vinculadas por limitações de largura de banda no servidor ou cliente, levará mais tempo para que elas sejam concluídas e poderão causar tempos limite. Para ver se o tempo limite é devido à largura de banda de rede no servidor, consulte [limitação de largura de banda do servidor](cache-troubleshoot-server.md#server-side-bandwidth-limitation). Para ver se o tempo limite é devido à largura de banda da rede do cliente, consulte [limitação de largura de banda do cliente](cache-troubleshoot-client.md#client-side-bandwidth-limitation).
1. Você está sendo limitado à CPU no servidor ou no cliente?

   - Verifique se você está se vinculando à CPU em seu cliente. A alta CPU pode fazer com que a solicitação não seja processada dentro do `synctimeout` intervalo e fazer com que uma solicitação expire o tempo limite. Mover para um tamanho de cliente maior ou distribuir a carga pode ajudar a controlar esse problema.
   - Verifique se você está recebendo a CPU no servidor monitorando a métrica de [desempenho de cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)da CPU. As solicitações recebidas enquanto Redis está associada à CPU podem fazer com que essas solicitações expirem o tempo limite. Para resolver essa condição, você pode distribuir a carga entre vários fragmentos em um cache Premium ou atualizar para um tipo de preço ou tamanho maior. Para obter mais informações, consulte [limitação de largura de banda do lado do servidor](cache-troubleshoot-server.md#server-side-bandwidth-limitation).
1. Há comandos que levam muito tempo para serem processados no servidor? Comandos de longa execução que estão demorando muito tempo para serem processados no servidor Redis podem causar tempos limite. Para obter mais informações sobre comandos de longa execução, consulte [comandos de longa execução](cache-troubleshoot-server.md#long-running-commands). Você pode se conectar ao cache do Azure para a instância do Redis usando o cliente Redis-CLI ou o [console do Redis](cache-configure.md#redis-console). Em seguida, execute o comando [SLOWLOG](https://redis.io/commands/slowlog) para ver se há solicitações mais lentas do que o esperado. O Servidor do Redis e o StackExchange.Redis são otimizados para várias solicitações pequenas, em vez de menos solicitações grandes. Dividir os dados em partes menores pode melhorar as coisa.

    Para obter informações sobre como se conectar ao ponto de extremidade TLS/SSL do seu cache usando Redis-CLI e stunnel, consulte a postagem de blog [anunciando o provedor de estado de sessão ASP.net para a versão de visualização do Redis](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/).
1. Uma carga alta no servidor Redis pode resultar em tempos limite. Você pode monitorar a carga do servidor monitorando a  [métrica de desempenho de cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)`Redis Server Load`. Uma carga de servidor de 100 (valor máximo) significa que o servidor Redis está ocupado, sem tempo ocioso, processando de solicitações. Para ver se determinadas solicitações estão consumindo toda a capacidade do servidor, execute o comando SlowLog, conforme descrito no parágrafo anterior. Para obter mais informações, confira Alto nível de uso da CPU/carga de servidor.
1. Houve qualquer outro evento no lado do cliente que possa ter causado um problema na rede? Os eventos comuns incluem: dimensionamento do número de instâncias do cliente para cima ou para baixo, implantação de uma nova versão do cliente ou dimensionamento automático habilitado. Em nossos testes, descobrimos que o dimensionamento automático ou o dimensionamento/redução podem fazer com que a conectividade de rede de saída seja perdida por vários segundos. O código do StackExchange.Redis é resiliente a tais eventos e se reconectará. Durante a reconexão, qualquer solicitação na fila pode atingir o tempo limite.
1. Houve uma grande solicitação antes de várias pequenas solicitações ao cache que atingiram o tempo limite? O parâmetro `qs` na mensagem de erro informa quantas solicitações foram enviadas do cliente para o servidor, mas não processaram uma resposta. Esse valor pode continuar crescendo porque o StackExchange.Redis usa uma única conexão TCP e só pode ler uma resposta por vez. Embora a primeira operação tenha expirado, ela não impede que mais dados sejam enviados de ou para o servidor. Outras solicitações serão bloqueadas até que a solicitação grande seja concluída e possa causar tempos limite. Uma solução é minimizar a chance de tempos limite, garantindo que o cache seja grande o suficiente para sua carga de trabalho e dividindo valores grandes em partes menores. Outra solução possível é usar um pool de objetos `ConnectionMultiplexer` no seu cliente e escolher o `ConnectionMultiplexer` menos carregado ao enviar uma nova solicitação. O carregamento em vários objetos de conexão deve impedir que um tempo limite único faça com que outras solicitações também expirem o tempo limite.
1. Se estiver usando `RedisSessionStateProvider` o, verifique se você definiu o tempo limite de repetição corretamente. `retryTimeoutInMilliseconds` deve ser maior do que `operationTimeoutInMilliseconds`; caso contrário, não há novas tentativas. No exemplo a seguir, `retryTimeoutInMilliseconds` é definido como 3000. Para obter mais informações, consulte [provedor de estado de sessão ASP.NET para Cache do Azure para Redis](cache-aspnet-session-state-provider.md) e [Como usar os parâmetros de configuração do provedor de estado de sessão e do provedor de cache de saída](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

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

1. Verifique o uso de memória no servidor do Cache do Azure para Redis [monitorando](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` e `Used Memory`. Se uma política de remoção estiver em vigor, o Redis começará a remover chaves quando `Used_Memory` atingir o tamanho do cache. Idealmente, `Used Memory RSS` deve ser apenas um pouco maior do que `Used memory`. Uma grande diferença significa que há fragmentação de memória (interna ou externa). Quando `Used Memory RSS` é menor que `Used Memory`, significa que parte da memória cache foi trocada pelo sistema operacional. Se a troca ocorrer, você poderá esperar que haja algumas latências significativas. Como o Redis não tem controle sobre como suas alocações são mapeadas para páginas de memória, `Used Memory RSS` o alto geralmente é o resultado de um pico no uso de memória. Quando o Redis Server libera memória, o alocador pega a memória, mas pode ou não fornecer a memória de volta ao sistema. Pode haver uma discrepância entre o valor de `Used Memory` e o consumo de memória, conforme relatado pelo sistema operacional. A memória pode ter sido usada e liberada por Redis, mas não foi retornada ao sistema. Para ajudar a reduzir os problemas de memória, você pode executar as seguintes etapas:

   - Atualize o cache para um tamanho maior para que você não esteja executando em limitações de memória no sistema.
   - Definir tempos de expiração das chaves para que valores mais antigos sejam removidos de forma proativa.
   - Monitorar a métrica de cache `used_memory_rss`. Quando esse valor se aproximar do tamanho do cache, é provável que você comece a ver os problemas de desempenho. Distribua os dados entre vários fragmentos se você estiver usando um cache Premium ou atualizar para um tamanho de cache maior.

   Para obter mais informações, consulte [pressão de memória no servidor Redis](cache-troubleshoot-server.md#memory-pressure-on-redis-server).

## <a name="additional-information"></a>Informações adicionais

- [Solucionar problemas no lado do cliente do Cache do Azure para Redis](cache-troubleshoot-client.md)
- [Solucionar problemas no lado do servidor do Cache do Azure para Redis](cache-troubleshoot-server.md)
- [Como medir e testar o desempenho do meu cache?](cache-management-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Como monitorar o Cache do Azure para Redis](cache-how-to-monitor.md)
