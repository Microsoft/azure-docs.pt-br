---
title: Perguntas frequentes de desenvolvimento do cache do Azure para Redis
description: Conheça as respostas a perguntas comuns que o ajudarão a desenvolver para o cache do Azure para Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: bafd8a9752d2587ec52fe586e442e3bfc86d7537
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585761"
---
# <a name="azure-cache-for-redis-development-faqs"></a>Perguntas frequentes de desenvolvimento do cache do Azure para Redis

Este artigo fornece respostas a perguntas comuns sobre como desenvolver para o cache do Azure para Redis.

## <a name="common-questions-and-answers"></a>Perguntas e respostas comuns
Esta seção aborda as seguintes perguntas frequentes:

* [Como começo a usar o Cache do Azure para Redis?](#how-can-i-get-started-with-azure-cache-for-redis)
* [O que as opções de configuração do StackExchange.Redis fazem?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Quais clientes do Cache do Azure para Redis posso usar?](#what-azure-cache-for-redis-clients-can-i-use)
* [Há um emulador local para o Cache do Azure para Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Como posso executar comandos do Redis?](#how-can-i-run-redis-commands)
* [Por que o cache do Azure para Redis tem uma referência da biblioteca de classes do MSDN?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference)
* [Posso usar o Cache do Azure para Redis como um cache de sessão em PHP?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Quais são os bancos de dados do Redis?](#what-are-redis-databases)

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Como começo a usar o Cache do Azure para Redis?
Há várias maneiras de começar a usar o Cache do Azure para Redis.

* Você pode conferir um dos nossos tutoriais disponíveis para [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md) e [Python](cache-python-get-started.md).
* Você pode assistir ao vídeo sobre [Como criar aplicativos de alto desempenho usando o Cache do Microsoft Azure para Redis](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Você pode conferir a documentação do cliente para os clientes que correspondem à linguagem de desenvolvimento do seu projeto a fim de saber como usar o Redis. Há muitos clientes Redis que podem ser usados com o Cache do Azure para Redis. Lista de clientes Redis, consulte [https://redis.io/clients](https://redis.io/clients).

Se ainda não tiver uma conta do Azure, você poderá:

* [Abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Obtenha créditos que possam ser usados para experimentar os serviços pagos do Azure. Mesmo depois que os créditos são usados, você pode manter a conta e usar os serviços e recursos gratuitos do Azure.
* [Ativar benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Todos os meses, sua assinatura do MSDN lhe oferece créditos que podem ser usados para serviços pagos do Azure.

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>O que as opções de configuração do StackExchange.Redis fazem?
O StackExchange.Redis tem muitas opções. Esta seção fala sobre algumas das configurações comuns. Para obter mais informações sobre opções do StackExchange.Redis, consulte [configuração do StackExchange.Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Descrição | Recomendação |
| --- | --- | --- |
| AbortOnConnectFail |Quando definido como true, a conexão não reconectará após uma falha de rede. |Defina como false e deixe o StackExchange.Redis reconectar-se automaticamente. |
| ConnectRetry |O número de vezes para repetir tentativas de conexão durante a conexão inicial. |Consulte as observações a seguir para se orientar. |
| ConnectTimeout |Tempo limite em ms para operações de conexão. |Consulte as observações a seguir para se orientar. |

Normalmente, os valores padrão do cliente são suficientes. Você pode realizar o ajuste fino das opções com base na sua carga de trabalho.

* **Novas tentativas**
  * Para ConnectRetry e ConnectTimeout, a orientação geral é falhar rapidamente e tentar novamente. Essa diretriz se baseia na sua carga de trabalho e em quanto tempo em média leva para o cliente emitir um comando Redis e receber uma resposta.
  * Deixe o StackExchange.Redis reconectar-se automaticamente, em vez de você verificar o status de conexão e realizar a reconexão. **Evitar usar a propriedade ConnectionMultiplexer.IsConnected**.
  * Bola de neve - às vezes, você poderá ter um problema em que você esteja realizando novas tentativas e isso se torne uma bola de neve, sem nunca se recuperar. Caso ocorra essa bola de neve, você deve considerar usar um algoritmo de nova tentativa de retirada exponencial, conforme descrito em [Orientação geral sobre novas tentativas](/azure/architecture/best-practices/transient-faults) publicado pelo grupo de Padrões e práticas da Microsoft.
  
* **Valores de tempo limite**
  * Considere sua carga de trabalho e defina os valores adequadamente. Se você estiver armazenando valores grandes, defina o tempo limite como um valor mais alto.
  * Defina `AbortOnConnectFail` como false e deixe StackExchange.Redis reconectar-se para você.
  * Use uma única instância de ConnectionMultiplexer para o aplicativo. Você pode usar uma LazyConnection para criar uma única instância que é retornada por uma propriedade Connection, conforme mostrado em [Conectar-se ao cache usando a classe ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Defina a propriedade `ConnectionMultiplexer.ClientName` como um nome exclusivo de instância de aplicativo para fins de diagnóstico.
  * Usar várias instâncias de `ConnectionMultiplexer` para cargas de trabalho personalizadas.
    * Você pode seguir este modelo se tem variação de carga em seu aplicativo. Por exemplo:
    * Você pode ter um multiplexador para lidar com chaves grandes.
    * Você pode ter um multiplexador para lidar com chaves pequenas.
    * Você pode definir valores diferentes para o tempo limite da conexão e a lógica de repetição para cada ConnectionMultiplexer que você usa.
    * Definir a propriedade `ClientName` em cada multiplexador para ajudar no diagnóstico.
    * Essas diretrizes podem levar mais simplificada latência por `ConnectionMultiplexer`.

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Quais clientes do Cache do Azure para Redis posso usar?
Uma das coisas legais sobre o Redis é que há muitos clientes que dão suporte a várias linguagens de programação diferentes. Para obter uma lista atual de clientes, confira [Clientes Redis](https://redis.io/clients). Para obter tutoriais que abrangem várias linguagens e clientes diferentes, confira [Como usar o Cache Redis do Azure](cache-dotnet-how-to-use-azure-redis-cache.md) e seus artigos relacionados no sumário.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Há um emulador local para o Cache do Azure para Redis?
Não há emulador local para o Cache do Azure para Redis, mas é possível executar a versão MSOpenTech do redis-server.exe nas [ferramentas de linha de comando do Redis](https://github.com/MSOpenTech/redis/releases/) no computador local e conectá-lo para obter uma experiência semelhante ao emulador de cache local, conforme mostrado no exemplo a seguir:

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        // Connect to a locally running instance of Redis to simulate
        // a local cache emulator experience.
        return ConnectionMultiplexer.Connect("127.0.0.1:6379");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

Se desejar, é possível configurar um arquivo [redis.conf](https://redis.io/topics/config) para corresponder melhor às [configurações de cache padrão](cache-configure.md#default-redis-server-configuration) do Cache do Azure para Redis online.

### <a name="how-can-i-run-redis-commands"></a>Como posso executar comandos do Redis?
Você pode usar qualquer comando listado em [Comandos do Redis](https://redis.io/commands#), exceto os listados em [Comandos do Redis sem suporte no Cache do Azure para Redis](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis). Você tem várias opções para executar comandos do Redis.

* Se você tiver um cache Standard ou Premium, poderá executar comandos do Redis usando o [Console do Redis](cache-configure.md#redis-console). O console Redis fornece uma maneira segura para executar comandos do Redis no portal do Azure.
* Também é possível usar as ferramentas de linha de comando do Redis. Para usá-las, você executará as seguintes etapas:
* Baixe as [Ferramentas de linha de comando do Redis](https://github.com/MSOpenTech/redis/releases/).
* Conecte-se ao cache usando `redis-cli.exe`. Passe no ponto de extremidade de cache usando que o comutador -h e a chave usando - a, como mostrado no exemplo a seguir:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> As ferramentas de linha de comando do Redis não funcionam com a porta TLS, mas você pode usar um utilitário como o `stunnel` para conectar as ferramentas com segurança à porta TLS seguindo as instruções no artigo [Como usar a ferramenta de linha de comando Redis com o Cache do Azure para Redis](./cache-how-to-redis-cli-tool.md).
>
>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference"></a>Por que o cache do Azure para Redis tem uma referência da biblioteca de classes do MSDN?
O cache Microsoft Azure para Redis é baseado no conhecido armazenamento de dados na memória de software livre, Redis. Ele pode ser acessado por uma ampla variedade de [clientes Redis](https://redis.io/clients) para muitas linguagens de programação. Cada cliente tem sua própria API, que faz chamadas para a instância de Cache do Azure para Redis usando [comandos do Redis](https://redis.io/commands).

Como cada cliente é diferente, não há não uma referência de classe centralizada no MSDN, e cada cliente mantém sua própria documentação de referência. Além da documentação de referência, há vários tutoriais mostrando como começar a usar o Cache do Azure para Redis usando diferentes linguagens e clientes de cache. Para acessar esses tutoriais, confira [Como usar o Cache Redis do Azure](cache-dotnet-how-to-use-azure-redis-cache.md) e seus artigos relacionados no sumário.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Posso usar o Cache do Azure para Redis como um cache de sessão em PHP?
Sim, para usar o Cache do Azure para Redis como um cache de sessão de PHP, especifique a cadeia de conexão para sua instância de Cache do Azure para Redis no `session.save_path`.

> [!IMPORTANT]
> Ao usar o Cache do Azure para Redis como um cache de sessão em PHP, você deve aplicar codificação URL à chave de segurança usada para se conectar ao cache, conforme mostrado no exemplo a seguir:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Se a chave não for codificada em URL, você receberá uma exceção com uma mensagem como: `Failed to parse session.save_path`
>

Para obter mais informações sobre como usar o Cache do Azure para Redis como um cache de sessão de PHP com o cliente PhpRedis, confira [Manipulador de sessão do PHP](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Quais são os bancos de dados do Redis?

Os Bancos de Dados Redis são apenas uma separação lógica dos dados dentro da mesma instância do Redis. A memória do cache é compartilhada entre todos os bancos de dados, e o consumo real da memória de um determinado banco de dados depende de chaves/valores armazenados no banco de dados. Por exemplo, um cache C6 tem 53 GB de memória, e um P5 tem 120 GB. Você pode optar por colocar todos os 53 GB/120 GB em um banco de dados, ou você pode dividi-los entre vários bancos de dados. 

> [!NOTE]
> Ao usar um Cache do Azure para Redis Premium com cluster habilitado, somente o banco de dados 0 estará disponível. Essa limitação é uma limitação do Redis intrínseca e não é específica ao Cache do Azure para Redis. Para obter mais informações, consulte [preciso fazer qualquer alteração no meu aplicativo cliente para usar o clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering).
> 
> 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre outros [cache do Azure para perguntas frequentes](cache-faq.md)sobre o Redis.