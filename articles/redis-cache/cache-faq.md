---
title: Perguntas Frequentes sobre o Cache Redis do Azure | Microsoft Docs
description: "Conheça as respostas a perguntas, padrões e práticas recomendadas comuns do Cache Redis do Azure"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: c2c52b7d-b2d1-433a-b635-c20180e5cab2
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: sdanie
ms.openlocfilehash: dcabdb789489af1996276d8838afde410473738d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-redis-cache-faq"></a>Perguntas frequentes sobre Cache Redis do Azure
Conheça as respostas a perguntas, padrões e práticas recomendadas comuns do Cache Redis do Azure.

## <a name="what-if-my-question-isnt-answered-here"></a>E se dúvida não foi respondida aqui?
Se sua pergunta não estiver listada aqui, fale conosco e nós ajudaremos a encontrar uma resposta.

* Você pode postar uma pergunta nos comentários no final das perguntas frequentes e entrar em contato com a equipe do Cache do Azure e outros membros da comunidade sobre este artigo.
* Para atingir um público mais amplo, você pode postar uma pergunta sobre o [Fórum do MSDN do Cache do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) e entrar em contato com a equipe do Cache do Azure e outros membros da comunidade.
* Se você quiser fazer uma solicitação de recurso, envie suas solicitações e ideias para o [UserVoice do Cache Redis do Azure](https://feedback.azure.com/forums/169382-cache).
* Você também pode enviar um email para nós com [Comentários externos sobre o Cache do Azure](mailto:azurecache@microsoft.com).

## <a name="azure-redis-cache-basics"></a>Noções básicas sobre o Cache Redis do Azure
As perguntas frequentes nesta seção abordam alguns dos conceitos básicos do Cache Redis do Azure.

* [O que é o Cache Redis do Azure?](#what-is-azure-redis-cache)
* [Como posso começar a usar o Cache Redis do Azure?](#how-can-i-get-started-with-azure-redis-cache)

As perguntas frequentes a seguir abordam os conceitos básicos e as perguntas sobre o Cache Redis do Azure e são respondidas em uma das outras seções das perguntas frequentes.

* [Qual oferta e tamanho de Cache Redis devo usar?](#what-redis-cache-offering-and-size-should-i-use)
* [Quais clientes do cache Redis eu posso usar?](#what-redis-cache-clients-can-i-use)
* [Há um emulador local para o Cache Redis do Azure?](#is-there-a-local-emulator-for-azure-redis-cache)
* [Como monitorar a integridade e o desempenho do meu cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Perguntas frequentes sobre planejamento
* [Qual oferta e tamanho de Cache Redis devo usar?](#what-redis-cache-offering-and-size-should-i-use)
* [Desempenho do Cache Redis do Azure](#azure-redis-cache-performance)
* [Em que região posso localizar meu cache?](#in-what-region-should-i-locate-my-cache)
* [Como eu sou cobrado pelo Cache Redis do Azure?](#how-am-i-billed-for-azure-redis-cache)
* [Posso usar o Cache Redis do Azure com o Azure Governamental na Nuvem, o Azure China na Nuvem ou o Microsoft Azure Alemanha?](#can-i-use-azure-redis-cache-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Perguntas frequentes sobre desenvolvimento
* [O que as opções de configuração do StackExchange.Redis fazem?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Quais clientes do cache Redis eu posso usar?](#what-redis-cache-clients-can-i-use)
* [Há um emulador local para o Cache Redis do Azure?](#is-there-a-local-emulator-for-azure-redis-cache)
* [Como posso executar comandos do Redis?](#how-can-i-run-redis-commands)
* [Por que o Cache Redis do Azure não tem uma referência da biblioteca de classes MSDN, assim como alguns dos outros serviços do Azure?](#why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Posso usar o Cache Redis do Azure como um cache de sessão do PHP?](#can-i-use-azure-redis-cache-as-a-php-session-cache)
* [Quais são os bancos de dados do Redis?](#what-are-redis-databases)

## <a name="security-faqs"></a>Perguntas frequentes sobre segurança
* [Quando devo habilitar a porta não SSL para conexão ao Redis?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>Perguntas frequentes sobre produção
* [Quais são algumas práticas recomendadas de produção?](#what-are-some-production-best-practices)
* [Quais são algumas das considerações ao usar os comandos comuns do Redis?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Como medir e testar o desempenho do meu cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Detalhes importantes sobre o crescimento de ThreadPool](#important-details-about-threadpool-growth)
* [Habilitar a GC (coleta de lixo) do servidor para obter mais produtividade no cliente ao usar o StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Considerações sobre desempenho em torno de conexões](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Perguntas frequentes sobre monitoramento e solução de problemas
As Perguntas frequentes nesta seção tratam de dúvidas comuns sobre monitoramento e solução de problemas. Para saber mais sobre como monitorar e solucionar problemas de suas instâncias do Cache Redis do Azure, confira [Como monitorar o Cache Redis do Azure](cache-how-to-monitor.md) e [Como solucionar problemas do Cache Redis do Azure](cache-how-to-troubleshoot.md).

* [Como monitorar a integridade e o desempenho do meu cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Por que vejo tempos limite?](#why-am-i-seeing-timeouts)
* [Por que meu cliente foi desconectado do cache?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Perguntas frequentes sobre ofertas anteriores de cache
* [Qual oferta de Cache do Azure é a correta para mim?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-redis-cache"></a>O que é o Cache Redis do Azure?
O Cache Redis do Azure é baseado no popular [Cache Redis](http://redis.io)de software livre. Ele oferece acesso ao cache Redis seguro e dedicado, gerenciado pela Microsoft e acessível de qualquer aplicativo no Azure. Para obter uma visão mais detalhada, confira a página do produto [Cache Redis do Azure](https://azure.microsoft.com/services/cache/) em Azure.com.

### <a name="how-can-i-get-started-with-azure-redis-cache"></a>Como posso começar a usar o Cache Redis do Azure?
Há várias maneiras de começar a usar o Cache Redis do Azure.

* Você pode conferir um dos nossos tutoriais disponíveis para [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md) e [Python](cache-python-get-started.md).
* Você pode assistir ao vídeo sobre [Como criar aplicativos de alto desempenho usando o Cache Redis do Microsoft Azure](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Você pode conferir a documentação do cliente para os clientes que correspondem à linguagem de desenvolvimento do seu projeto a fim de saber como usar o Redis. Há muitos clientes Redis que podem ser usados com o Cache Redis do Azure. Para obter uma lista de clientes Redis, acesse [http://redis.io/clients](http://redis.io/clients).

Se ainda não tiver uma conta do Azure, você poderá:

* [Abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Obtenha créditos que possam ser usados para experimentar os serviços pagos do Azure. Mesmo depois que os créditos são usados, você pode manter a conta e usar os serviços e recursos gratuitos do Azure.
* [Ativar benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Todos os meses, sua assinatura do MSDN lhe oferece créditos que podem ser usados para serviços pagos do Azure.

<a name="cache-size"></a>

### <a name="what-redis-cache-offering-and-size-should-i-use"></a>Qual oferta e tamanho de Cache Redis devo usar?
Cada oferta de Cache Redis do Azure fornece diferentes níveis de opções **tamanho**, **largura de banda**, **alta disponibilidade** e **SLA**.

Veja abaixo as considerações para a escolha de uma oferta de Cache.

* **Memória**: as camadas Básica e Standard oferecem 250 MB – 53 GB. A camada Premium oferece até 530 GB. Para saber mais, confira [Preço do Cache Redis do Azure](https://azure.microsoft.com/pricing/details/cache/).
* **Desempenho de rede**: se você tiver uma carga de trabalho que requer uma alta taxa de transferência, a camada Premium oferece mais largura de banda quando comparada às camadas Standard ou Básica. Também dentro de cada camada, caches de tamanhos maiores têm mais largura de banda, devido à VM subjacente que hospeda o cache. Confira a [tabela a seguir](#cache-performance) para saber mais.
* **Taxa de transferência**: a camada Premium oferece a maior taxa de transferência disponível. Se o cliente ou o servidor de cache atingir os limites de largura de banda, você poderá receber tempos limite no lado do cliente. Para obter mais informações, confira a tabela a seguir.
* **Alta Disponibilidade/SLA**: o Cache Redis do Azure garante que um cache Standard/Premium esteja disponível durante, pelo menos, 99,9% do tempo. Para saber mais sobre nosso SLA, confira [Preço do Cache Redis do Azure](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). O SLA abrange apenas a conectividade com os pontos de extremidade do Cache. O SLA não abrange a proteção contra perda de dados. Recomendamos usar o recurso de persistência de dados do Redis na camada Premium para aumentar a resiliência contra a perda de dados.
* **Persistência de dados do Redis**: a camada Premium permite persistir os dados de cache em uma conta do Armazenamento do Azure. Em um cache Básico/Standard, todos os dados são armazenados apenas na memória. Se houver problemas de infraestrutura subjacente, pode haver uma possível perda de dados. Recomendamos usar o recurso de persistência de dados do Redis na camada Premium para aumentar a resiliência contra a perda de dados. O Cache Redis do Azure oferece opções de RDB e AOF (em breve) na persistência do Redis. Para saber mais, confira [Como configurar a persistência para um Cache Redis do Azure Premium](cache-how-to-premium-persistence.md).
* **Cluster Redis**: se desejar criar caches maiores que 53 GB ou fragmentar dados entre vários nós do Redis, você pode usar o clustering do Redis que está disponível na camada Premium. Cada nó consiste em um par de cache primário/de réplica para alta disponibilidade. Para saber mais, confira [Como configurar o clustering para um Cache Redis do Azure Premium](cache-how-to-premium-clustering.md).
* **Isolamento de rede e segurança avançados**: a implantação de VNET (Rede Virtual) do Azure fornece isolamento e segurança avançados para o Cache Redis do Azure, bem como sub-redes, políticas de controle de acesso e outros recursos para restringir ainda mais o acesso. Para saber mais,confira [Como configurar o suporte de Rede Virtual para um Cache Redis do Azure Premium](cache-how-to-premium-vnet.md).
* **Configurar o Redis**: nas camadas Standard e Premium, você pode configurar o Redis para notificações de Keyspace.
* **Número máximo de conexões de cliente**: a camada Premium oferece o número máximo de clientes que podem se conectar ao Redis, com um número maior de conexões para caches de tamanhos maiores. Para obter mais informações, confira [Preço do Cache Redis do Azure](https://azure.microsoft.com/pricing/details/cache/).
* **Núcleo dedicado para o servidor do Redis**: na camada Premium, todos os tamanhos de cache têm um núcleo dedicado para o Redis. Nas camadas Básica/Standard, o tamanho C1 e acima têm um núcleo dedicado para o servidor do Redis.
* **Como o Redis usa thread único** , ter mais de dois núcleos não oferece um benefício adicional em relação a ter apenas dois, mas tamanhos maiores de VM normalmente têm mais largura de banda que tamanhos menores. Se o cliente ou servidor de cache atingir os limites de largura de banda, você receberá tempos limite no lado do cliente.
* **Melhorias de desempenho**: os caches na camada Premium são implantados no hardware que tem processadores mais rápidos e que oferece um melhor desempenho quando comparado às camadas Básica ou Standard. Os Caches da camada Premium têm a taxa de transferência mais alta e as latências mais baixas.

<a name="cache-performance"></a>

### <a name="azure-redis-cache-performance"></a>Desempenho do Cache Redis do Azure
A tabela a seguir mostra os valores de largura de banda máxima observados durante o teste de vários tamanhos de caches Standard e Premium usando `redis-benchmark.exe` de uma VM de Iaas no ponto de extremidade do Cache Redis do Azure. 

>[!NOTE] 
>Esses valores não são garantidos e que não há nenhum SLA para esses números, mas eles devem ser típicos. Você deve realizar teste de carga em seu próprio aplicativo para determinar o tamanho de cache certo para ele.
>
>

Podemos tirar as seguintes conclusões desta tabela:

* A taxa de transferência para os caches são do mesmo tamanho é superior na camada Premium em comparação com a camada Standard. Por exemplo, com um Cache de 6 GB, a taxa de transferência de P1 será 180 mil RPS, em comparação com 49 mil para C3.
* Com o cluster Redis, a taxa de transferência aumenta linearmente à medida que o número de fragmentos (nós) no cluster aumenta. Por exemplo, se você criar um cluster P4 de 10 fragmentos, a taxa de transferência disponível será de 400.000 * 10 = 4 milhões de RPS.
* A taxa de transferência tamanhos de chave maiores é mais alta na camada Premium quando comparada à camada Standard.

| Camada de preços | Tamanho | Núcleos de CPU | Largura de banda disponível | Tamanho do valor de 1 KB |
| --- | --- | --- | --- | --- |
| **Tamanhos de cache padrão** | | |**Megabits por segundo (Mb/s) / Megabytes por segundo (MB/s)** |**RPS (solicitações por segundo)** |
| C0 |250 MB |Compartilhado |5 / 0,625 |600 |
| C1 |1 GB |1 |100 / 12,5 |12.200 |
| C2 |2,5 GB |2 |200 / 25 |24.000 |
| C3 |6 GB |4 |400 / 50 |49.000 |
| C4 |13 GB |2 |500 / 62,5 |61.000 |
| C5 |26 GB |4 |1.000 / 125 |115.000 |
| C6 |53 GB |8 |2.000 / 250 |150.000 |
| **Tamanhos de cache Premium** | |**Núcleos de CPU por fragmento** | **Megabits por segundo (Mb/s) / Megabytes por segundo (MB/s)** |**RPS (solicitações por segundo) por fragmento** |
| P1 |6 GB |2 |1.500 / 187.5 |180,000 |
| P2 |13 GB |4 |3.000 / 375 |360,000 |
| P3 |26 GB |4 |3.000 / 375 |360,000 |
| P4 |53 GB |8 |6.000 / 750 |400.000 |

Para obter instruções sobre como baixar as ferramentas do Redis como `redis-benchmark.exe`, consulte a seção [Como posso executar comandos do Redis?](#cache-commands)

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>Em que região posso localizar meu cache?
Para melhor desempenho e menor latência, localize o Cache Redis do Azure na mesma região que o aplicativo cliente de cache.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-redis-cache"></a>Como eu sou cobrado pelo Cache Redis do Azure?
O preço do Cache Redis do Azure pode ser encontrado [aqui](https://azure.microsoft.com/pricing/details/cache/). A página de preços lista os preços com uma taxa horária. Os caches são cobrados em uma base por minuto do momento em que o cache é criado até o momento em que um cache é excluído. Não há nenhuma opção para parar ou pausar a cobrança de um cache.

### <a name="can-i-use-azure-redis-cache-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Posso usar o Cache Redis do Azure com o Azure Governamental na Nuvem, o Azure China na Nuvem ou o Microsoft Azure Alemanha?
Sim, o Cache Redis do Azure está disponível no Azure Governamental na Nuvem, Azure China na Nuvem e Microsoft Azure Alemanha. As URLs para acesso e gerenciamento do Cache Redis do Azure são diferentes no Azure Governamental na Nuvem e no Azure China na Nuvem em comparação com a Nuvem Pública do Azure. 

| Nuvem   | Sufixo DNS para Redis            |
|---------|---------------------------------|
| Público  | *.redis.cache.windows.net       |
| Gov dos EUA  | *.redis.cache.usgovcloudapi.net |
| Alemanha | *.redis.cache.cloudapi.de       |
| China   | *.redis.cache.chinacloudapi.cn  |

Para obter mais informações sobre considerações ao usar o Cache Redis do Azure com outras nuvens, consulte os links a seguir.

- [Bancos de dados do Azure Governamental – Cache Redis do Azure](../azure-government/documentation-government-services-database.md#azure-redis-cache)
- [Azure China na Nuvem – Cache Redis do Azure](https://www.azure.cn/documentation/services/redis-cache/)
- [Microsoft Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)

Para obter mais informações sobre como usar o Cache Redis do Azure com o PowerShell no Azure Governamental na Nuvem, Azure China na Nuvem e no Microsoft Azure Alemanha, veja [Como se conectar a outras nuvens – PowerShell do Cache Redis do Azure](cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>O que as opções de configuração do StackExchange.Redis fazem?
O StackExchange.Redis tem muitas opções. Esta seção fala sobre algumas das configurações comuns. Para obter mais informações sobre opções do StackExchange.Redis, consulte [configuração do StackExchange.Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Descrição | Recomendações |
| --- | --- | --- |
| AbortOnConnectFail |Quando definido como true, a conexão não reconectará após uma falha de rede. |Defina como false e deixe o StackExchange.Redis reconectar-se automaticamente. |
| ConnectRetry |O número de vezes para repetir tentativas de conexão durante a conexão inicial. |Consulte as observações a seguir para se orientar. |
| ConnectTimeout |Tempo limite em ms para operações de conexão. |Consulte as observações a seguir para se orientar. |

Normalmente, os valores padrão do cliente são suficientes. Você pode realizar o ajuste fino das opções com base na sua carga de trabalho.

* **Novas tentativas**
  * Para ConnectRetry e ConnectTimeout, a orientação geral é falhar no modo rápido e tentar novamente. Essa diretriz se baseia na sua carga de trabalho e em quanto tempo em média leva para o cliente emitir um comando Redis e receber uma resposta.
  * Deixe o StackExchange.Redis reconectar-se automaticamente, em vez de você verificar o status de conexão e realizar a reconexão. **Evitar usar a propriedade ConnectionMultiplexer.IsConnected**.
  * Bola de neve - às vezes, você poderá ter um problema em que você esteja realizando novas tentativas e isso se torne uma bola de neve, sem nunca se recuperar. Caso ocorra essa bola de neve, você deve considerar usar um algoritmo de nova tentativa de retirada exponencial, conforme descrito em [Orientação geral sobre novas tentativas](../best-practices-retry-general.md) publicado pelo grupo de Padrões e práticas da Microsoft.
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

### <a name="what-redis-cache-clients-can-i-use"></a>Quais clientes do cache Redis eu posso usar?
Uma das coisas legais sobre o Redis é que há muitos clientes que dão suporte a várias linguagens de programação diferentes. Para obter uma lista atual de clientes, confira [Clientes Redis](http://redis.io/clients). Para ver tutoriais que abrangem várias linguagens e clientes diferentes, confira [Como utilizar o Cache Redis do Azure](cache-dotnet-how-to-use-azure-redis-cache.md) e clique na linguagem desejada do seletor de linguagens logo no começo do artigo.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-redis-cache"></a>Há um emulador local para o Cache Redis do Azure?
Não há emulador local para o Cache Redis do Azure, mas é possível executar a versão MSOpenTech do redis-server.exe nas [ferramentas de linha de comando do Redis](https://github.com/MSOpenTech/redis/releases/) no computador local e conectá-lo para obter uma experiência semelhante ao emulador de cache local, conforme mostrado no exemplo a seguir:

    private static Lazy<ConnectionMultiplexer>
          lazyConnection = new Lazy<ConnectionMultiplexer>
        (() =>
        {
            // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
            return ConnectionMultiplexer.Connect("127.0.0.1:6379");
        });

        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


Se desejar, é possível configurar um arquivo [redis.conf](http://redis.io/topics/config) para corresponder melhor às [configurações de cache padrão](cache-configure.md#default-redis-server-configuration) do Cache Redis do Azure online.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Como posso executar comandos do Redis?
Você pode usar qualquer um dos comandos listados em [Comandos do Redis](http://redis.io/commands#), exceto pelos comandos listados em [Comandos do Redis sem suporte no Cache Redis do Azure](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Você tem várias opções para executar comandos do Redis.

* Se você tiver um cache Standard ou Premium, poderá executar comandos do Redis usando o [Console do Redis](cache-configure.md#redis-console). O console Redis fornece uma maneira segura para executar comandos do Redis no portal do Azure.
* Também é possível usar as ferramentas de linha de comando do Redis. Para usá-las, você executará as seguintes etapas:
* Baixe as [Ferramentas de linha de comando do Redis](https://github.com/MSOpenTech/redis/releases/).
* Conecte-se ao cache usando `redis-cli.exe`. Passe no ponto de extremidade de cache usando que o comutador -h e a chave usando - a, como mostrado no exemplo a seguir:
* `redis-cli -h <your cache="" name="">
  .redis.cache.windows.net -a <key>
  `

> [!NOTE]
> As ferramentas de linha de comando do Redis não funcionam com a porta SSL, mas você pode usar um utilitário como `stunnel` para conectar as ferramentas com segurança à porta SSL seguindo as instruções na postagem de blog [Anunciando o provedor de estado de sessão ASP.NET para versão de visualização do Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) .
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Por que o Cache Redis do Azure não tem uma referência da biblioteca de classes MSDN, assim como alguns dos outros serviços do Azure?
O Cache Redis do Microsoft Azure baseia-se no popular Cache Redis de software livre e pode ser acessado por uma ampla variedade de [clientes Redis](http://redis.io/clients) para várias linguagens de programação. Cada cliente tem sua própria API, que faz chamadas para a instância de cache Redis usando [comandos do Redis](http://redis.io/commands).

Como cada cliente é diferente, não há não uma referência de classe centralizada no MSDN, e cada cliente mantém sua própria documentação de referência. Além de documentação de referência, há vários tutoriais mostrando como começar a usar o Cache Redis do Azure usando diferentes linguagens e clientes de cache. Para acessar esses tutoriais, confira [Como utilizar o Cache Redis do Azure](cache-dotnet-how-to-use-azure-redis-cache.md) e clique no linguagem desejada do seletor de linguagens logo no início do artigo.

### <a name="can-i-use-azure-redis-cache-as-a-php-session-cache"></a>Posso usar o Cache Redis do Azure como um cache de sessão do PHP?
Sim, para usar o Cache Redis do Azure como um cache de sessão do PHP, especifique a cadeia de conexão para sua instância de Cache Redis do Azure no `session.save_path`.

> [!IMPORTANT]
> Ao usar o Cache Redis do Azure como um cache de sessão do PHP, você deve aplicar codificação URL à chave de segurança usada para se conectar ao cache, conforme mostrado no exemplo a seguir:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Se a chave não for codificada em URL, você receberá uma exceção com uma mensagem como: `Failed to parse session.save_path`
>
>

Para obter mais informações sobre como usar o Cache Redis como um cache de sessão do PHP com o cliente PhpRedis, veja [Manipulador de sessão do PHP](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Quais são os bancos de dados do Redis?

Os Bancos de Dados Redis são apenas uma separação lógica dos dados dentro da mesma instância do Redis. A memória do cache é compartilhada entre todos os bancos de dados, e o consumo real da memória de um determinado banco de dados depende de chaves/valores armazenados no banco de dados. Por exemplo, um cache C6 tem 53 GB de memória. Você pode optar por colocar todos os 53 GB em um banco de dados, ou você pode dividi-los entre vários bancos de dados. 

> [!NOTE]
> Ao usar um Cache Redis do Azure Premium com cluster habilitado, somente o banco de dados 0 estará disponível. Essa limitação é uma limitação do Redis intrínseca e não é específica ao Cache Redis do Azure. Para saber mais, confira [Preciso fazer alguma alteração no meu aplicativo cliente para usar clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>Quando devo habilitar a porta não SSL para conexão ao Redis?
Redis servidor não oferece suporte a SSL, mas não de Cache Redis do Azure. Se você estiver conectando ao Cache Redis do Azure e o seu cliente dá suporte a SSL, como StackExchange.Redis, você deve usar SSL.

>[!NOTE]
>A porta que não é do tipo SSL é desabilitada por padrão para novas instâncias de Cache Redis do Azure. Se o cliente não dá suporte a SSL, você deve habilitar a porta não SSL seguindo as instruções na seção [Portas de acesso](cache-configure.md#access-ports) do artigo [Configurar um cache no Cache Redis do Azure](cache-configure.md).
>
>

Ferramentas do Redis como o `redis-cli` não funcionam com a porta SSL, mas você pode usar um utilitário como `stunnel` para conectar as ferramentas com segurança à porta SSL seguindo as instruções na postagem de blog [Anunciando o provedor de estado de sessão ASP.NET para versão de visualização do Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Para obter instruções sobre como baixar as ferramentas do Redis, consulte a seção [Como posso executar comandos do Redis?](#cache-commands)

### <a name="what-are-some-production-best-practices"></a>Quais são algumas práticas recomendadas de produção?
* [Práticas recomendadas do StackExchange.Redis](#stackexchangeredis-best-practices)
* [Configuração e conceitos](#configuration-and-concepts)
* [Testes de desempenho](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Práticas recomendadas do StackExchange.Redis
* Defina `AbortConnect` como false e deixe o ConnectionMultiplexer se reconectar automaticamente. [Consulte aqui para obter detalhes](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Reutilize o ConnectionMultiplexer – não crie um novo para cada solicitação. O padrão `Lazy<ConnectionMultiplexer>` [mostrado aqui](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) é recomendável.
* O Redis funciona melhor com valores menores, portanto, considere talhar dados maiores em várias chaves. Nesta [discussão do Redis](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 kb é considerado grande. Leia [este artigo](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) para obter um problema de exemplo que pode ser causado por valores grandes.
* Configure suas [configurações de ThreadPool](#important-details-about-threadpool-growth) para evitar tempos limites.
* Use pelo menos o connectTimeout padrão de 5 segundos. Esse intervalo forneceria ao StackExchange.Redis tempo suficiente para restabelecer a conexão, no caso de um blip de rede.
* Lembre-se dos custos de desempenho associados a operações diferentes que você está executando. Por exemplo, o `KEYS` comando é uma operação O(n) e deve ser evitado. O [site redis.io](http://redis.io/commands/) apresenta detalhes sobre a complexidade de tempo para cada operação a qual ele dá suporte. Clique em cada comando para ver a complexidade para cada operação.

#### <a name="configuration-and-concepts"></a>Configuração e conceitos
* Use as camadas Standard ou Premium para Sistemas de Produção. A camada Básica é um sistema de nó único sem replicação de dados e sem SLA. Além disso, use pelo menos um cache C1. Caches C0 normalmente são usados para cenários de desenvolvimento/teste simples.
* Lembre-se de que o Redis é um armazenamento de dados **na memória** . Leia [este artigo](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) para que você fique ciente de situações em que pode haver perda de dados.
* Desenvolva seu sistema, de modo que possa lidar com blips de conexão [devido à aplicação de patch e ao failover](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Testes de desempenho
* Comece usando `redis-benchmark.exe` para ter uma ideia de taxa de transferência possível antes de escrever seus próprios testes de desempenho. Porque `redis-benchmark` não dá suporte a SSL, você deve [habilitar a porta não SSL no portal do Azure](cache-configure.md#access-ports) antes de executar o teste. Por exemplo, consulte [Como medir e testar o desempenho do meu cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* A VM do cliente usada para testes deve estar na mesma região que a instância de cache Redis.
* É recomendável usar a Série de VM Dv2 série para o cliente, pois ela tem hardware melhor e deve oferecer melhores resultados.
* Verifique se a VM do cliente escolhida tem ao menos a quantidade de computação e funcionalidade de largura de banda de cache que você está testando.
* Habilite o VRSS no computador cliente, se você estiver usando o Windows. [Consulte aqui para obter detalhes](https://technet.microsoft.com/library/dn383582.aspx).
* As instâncias do Redis nível Premium terão melhor latência de rede e taxa de transferência porque estão em execução tanto para a CPU quanto para a Rede.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Quais são algumas das considerações ao usar os comandos comuns do Redis?
* Você não deve executar determinados comandos do Redis que levam muito tempo para ser concluídos sem compreender o impacto desses comandos.
  * Por exemplo, não execute o comando [CHAVES](http://redis.io/commands/keys) durante a produção, já que ele pode levar muito tempo para retornar dependendo do número de chaves. O Redis é um servidor de thread único e processa um comando por vez. Se você emitir outros comandos após CHAVES, eles não serão processados até que o Redis processe o comando CHAVES. O [site redis.io](http://redis.io/commands/) apresenta detalhes sobre a complexidade de tempo para cada operação a qual ele dá suporte. Clique em cada comando para ver a complexidade para cada operação.
* Tamanhos de chave - devo usar valores pequenos ou grandes de chave? Em geral, depende do cenário. Se seu cenário exigir chaves maiores, você poderá ajustar os valores de ConnectionTimeout e de novas tentativas e ajustar sua lógica de repetição de tentativas. Da perspectiva do servidor Redis, valores menores têm um desempenho melhor.
* Essas considerações não significam que você não pode armazenar valores maiores em Redis, mas que deve estar ciente das considerações a seguir. As latências serão maiores. Se você tiver um conjunto de dados maior e um menor, você pode usar várias instâncias do ConnectionMultiplexer, cada uma configurada com um conjunto diferente de valores de tempo limite e de repetição de tentativa, conforme descrito anteriormente na seção [O que as opções de configuração de StackExchange.Redis fazem](#cache-configuration) .

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Como medir e testar o desempenho do meu cache?
* [Habilite o diagnóstico de cache](cache-how-to-monitor.md#enable-cache-diagnostics) para que você possa [monitorar](cache-how-to-monitor.md) a integridade do cache. Você pode exibir as métricas no portal do Azure e também pode [baixá-las e analisá-las](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) usando as ferramentas de sua escolha.
* Você pode usar o redis-benchmark.exe para testar a carga em seu servidor do Redis.
* Certifique-se que o cliente e o cache Redis de teste de carga estejam na mesma região.
* Use o redis cli.exe e monitore o cache usando o comando INFO.
* Se sua carga está causando um nível elevado de fragmentação de memória, você deve escalar verticalmente para um tamanho de cache maior.
* Para obter instruções sobre como baixar as ferramentas do Redis, consulte a seção [Como posso executar comandos do Redis?](#cache-commands)

Os comandos a seguir fornecem um exemplo de uso benchmark.exe redis. Para obter resultados precisos, execute esses comandos de uma VM na mesma região que o cache.

* Teste solicitações SET de Pipeline usando um conteúdo de 1k

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Solicitações GET de Pipelined usando uma conteúdo de 1k.
  OBSERVAÇÃO: execute o teste SET abaixo primeiro para popular o cache

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Detalhes importantes sobre o crescimento de ThreadPool
O ThreadPool do CLR tem dois tipos de threads: "Trabalho" e "Porta de conclusão E/S" (também conhecida como IOCP).

* Os threads de trabalho são usados em situações como o processamento dos métodos `Task.Run(…)` ou `ThreadPool.QueueUserWorkItem(…)`. Esses threads também são usados por vários componentes no CLR quando o trabalho precisa ser executado em um thread em segundo plano.
* Os threads IOCP são usados quando há E/S assíncrona (por exemplo, lendo da rede).

O pool de threads fornece novos threads de trabalho ou de conclusão de E/S sob demanda (sem qualquer limitação) até atingir a configuração de "Mínimo" para cada tipo de thread. Por padrão, o número mínimo de threads é definido como o número de processadores em um sistema.

Depois que o número de threads existentes (ocupados) atinge o número "mínimo" de threads, o ThreadPool limitará a taxa à qual injeta novos threads em um thread por 500 milissegundos. Geralmente, se seu sistema obtiver um pico de trabalho que precisa de um thread IOCP, ele processará esse trabalho muito rapidamente. No entanto, se o pico de trabalho for maior que a configuração "Mínimo", haverá algum atraso no processamento do trabalho, já que o ThreadPool aguarda um dentre dois acontecimentos.

1. Um thread existente ficar livre para processar o trabalho.
2. Nenhum thread existente ficar livre para 500 ms, o que leva à criação de outro thread.

Basicamente, isso significa que quando o número de threads ocupados é maior que o mínimo de threads, você provavelmente está pagando um atraso de 500 ms antes que o tráfego de rede seja processado pelo aplicativo. Além disso, é importante observar que, quando um thread existente permanece ocioso por mais de 15 segundos (se não me falha a memória), ele é limpo e esse ciclo de crescimento e redução pode ser repetido.

Se examinarmos um exemplo de mensagem de erro do StackExchange.Redis (versão 1.0.450 ou posterior), você verá que agora ela imprime estatísticas de ThreadPool (confira os detalhes de TRABALHO e IOCP abaixo).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

No exemplo anterior, você pode ver que, para o thread IOCP, há seis threads ocupados e o sistema está configurado para permitir o mínimo de quatro threads. Nesse caso, o cliente provavelmente veria dois atrasos de 500 ms porque 6 > 4.

Observe que o StackExchange.Redis poderá atingir tempos limite se o crescimento de threads de TRABALHO ou IOCP for limitado.

### <a name="recommendation"></a>Recomendações
Dadas essas informações, recomendamos fortemente que os clientes definam o valor de configuração mínima para threads de TRABALHO e IOCP com um valor maior que o valor padrão. Não podemos dar uma orientação que sirva para todos sobre esse valor porque o valor correto para um aplicativo será muito alto/baixo para outro aplicativo. Essa configuração também pode afetar o desempenho de outras partes do aplicativos complicados; portanto, cada cliente precisa ajustar essa configuração para as suas necessidades específicas. Um bom ponto de partida é 200 ou 300, para testar e ajustar conforme necessário.

Como definir essa configuração:

* No ASP.NET, use a [configuração “minIoThreads”]["minIoThreads" configuration setting] no elemento de configuração `<processModel>` no web.config. Se você estiver executando nos Sites do Azure, essa configuração não será exposta pelas opções de configuração. No entanto, você deve ainda ser capaz de definir essa configuração por meio de programação (veja abaixo) do seu método Application_Start em global.asax.cs.

  > [!NOTE] 
  > O valor especificado nesse elemento de configuração é uma *configuração por núcleo*. Por exemplo, se você tiver um computador com quatro núcleos e desejar que sua configuração minIOThreads seja de 200 no tempo de execução, use `<processModel minIoThreads="50"/>`.
  >

* Fora do ASP.NET, use a API [ThreadPool.SetMinThreads(…)](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads.aspx) .

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Habilitar a GC (coleta de lixo) do servidor para obter mais produtividade no cliente ao usar o StackExchange.Redis
Habilitar a GC do servidor pode otimizar o cliente e proporcionar melhor desempenho e produtividade ao usar o Stackexchange.Redis. Para saber mais sobre a GC do servidor e como habilitá-la, confira os artigos a seguir:

* [Para habilitar a GC (coleta de lixo) do servidor](https://msdn.microsoft.com/library/ms229357.aspx)
* [Noções básicas sobre a coleta de lixo](https://msdn.microsoft.com/library/ee787088.aspx)
* [Coleta de lixo e desempenho](https://msdn.microsoft.com/library/ee851764.aspx)


### <a name="performance-considerations-around-connections"></a>Considerações sobre desempenho em torno de conexões

Cada tipo de preço tem limites diferentes para conexões de cliente, memória e largura de banda. Embora cada tamanho de cache permita *até* um determinado número de conexões, cada conexão com o Redis tem sobrecarga associadas. Um exemplo de tal sobrecarga seria o uso da CPU e de memória como resultado de criptografia TLS/SSL. O limite máximo de conexão para um tamanho de cache determinado pressupõe um cache com pouca carga. Se a carga da conexão de sobrecarga *mais* a carga de operações do cliente excederem a capacidade do sistema, o cache poderá enfrentar problemas de capacidade mesmo se não exceder o limite de conexão para o tamanho atual do cache.

Para obter mais informações sobre os limites de conexões diferentes para cada camada, consulte [preços do Azure Redis Cache](https://azure.microsoft.com/pricing/details/cache/). Para obter mais informações sobre as conexões e outras configurações padrão, consulte [configuração do servidor padrão Redis](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Como monitorar a integridade e o desempenho do meu cache?
As instâncias do Cache Redis do Microsoft Azure podem ser monitoradas no [Portal do Azure](https://portal.azure.com). Você pode exibir métricas, fixar gráficos de métricas ao quadro inicial, personalizar o intervalo de data e hora de gráficos de monitoramento, adicionar/remover as métricas de gráficos e definir alertas quando determinadas condições forem atendidas. Para saber mais, veja [Como monitorar o Cache Redis do Azure](cache-how-to-monitor.md).

O Cache Redis **menu recursos** também contém várias ferramentas para monitorar e solucionar problemas de seus caches.

* **Diagnosticar e solucionar problemas** fornece informações sobre problemas comuns e estratégias para resolvê-los.
* **Resource Health** observa seu recurso e informa se ele está sendo executado conforme o esperado. Para saber mais sobre o serviço Azure Resource Health, confira [Visão geral do Azure Resource Health](../resource-health/resource-health-overview.md).
* **Nova solicitação de suporte** fornece opções para abrir uma solicitação de suporte para seu cache.

Essas ferramentas permitem monitorar a integridade de suas instâncias de Cache Redis do Azure e ajudá-lo a gerenciar seus aplicativos de cache. Para obter mais informações, consulte a seção "Configurações de solução de problemas e suporte" em [Como configurar o Cache Redis do Azure](cache-configure.md).

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Por que vejo tempos limite?
Tempos limite ocorrem no cliente que você usa para se comunicar com o Redis. Quando um comando é enviado ao servidor Redis, o comando é colocado na fila e o servidor Redis, eventualmente, seleciona o comando e o executa. No entanto, o cliente pode atingir o tempo limite durante este processo e, se ele faz isso, uma exceção é gerada no lado que realiza a chamada. Para saber mais sobre como solucionar problemas de tempo limite, consulte [Solução de problemas do lado do cliente](cache-how-to-troubleshoot.md#client-side-troubleshooting) e [Exceções de tempo limite do StackExchange.Redis](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions).

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Por que meu cliente foi desconectado do cache?
A seguir estão alguns motivos comuns para uma desconexão de cache.

* Motivos no lado do cliente
  * O aplicativo cliente foi reimplantado.
  * O aplicativo cliente executou uma operação de dimensionamento.
    * No caso de serviços de nuvem ou aplicativos Web, isso pode ser devido ao dimensionamento automático.
  * A camada de rede no lado do cliente foi alterada.
  * Erros transitórios ocorreram no cliente ou em nós de rede entre o cliente e o servidor.
  * Os limites de largura de banda foram atingidos.
  * Operações vinculadas à CPU demoraram muito para ser concluídas.
* Motivos no lado do servidor
  * Com a oferta de cache padrão, o serviço de Cache Redis do Azure iniciou um failover do nó primário para o nó secundário.
  * O Azure estava aplicando um patch na instância onde o cache foi implantado
    * Isso pode ser para atualizações de servidor do Redis ou manutenção geral de VM.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Qual oferta de Cache do Azure é a correta para mim?
> [!IMPORTANT]
> De acordo com o [comunicado](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) do ano passado, o Serviço de Cache Gerenciado do Azure e o serviço Cache na Função do Azure **foram desativados** em 30 de novembro de 2016. A nossa recomendação é usar o [Cache Redis do Azure](https://azure.microsoft.com/services/cache/). Para saber mais sobre a migração, veja [Migrar do Serviço de Cache Gerenciado para o Cache Redis do Azure](cache-migrate-to-redis.md).
>
>

### <a name="azure-redis-cache"></a>Cache Redis do Azure
O Cache Redis do Azure está disponível em tamanhos de 53 GB e tem SLA de disponibilidade de 99,9%. A nova [camada premium](cache-premium-tier-intro.md) oferece tamanhos de até 530 GB e suporte para clustering, VNET e persistência, com um SLA de 99,9%.

O Cache Redis do Azure dá aos clientes a capacidade de usar um Cache Redis seguro e dedicado, gerenciado pela Microsoft. Com essa oferta, você pode aproveitar o rico conjunto de recursos e ecossistema fornecido pelo Redis, além de hospedagem e monitoramento confiáveis da Microsoft.

Diferentemente dos caches tradicionais que lidam exclusivamente com pares chave-valor, o Redis é popular por seus tipos de dados altamente funcionais. O Redis também permite executar operações atômicas nesses tipos, como acréscimos a uma cadeia de caracteres; incrementação de valor em hash; envios para uma lista; intersecção, união e diferença em conjuntos de computação; ou colocar o membro com classificação mais alta em um conjunto ordenado. Outros recursos incluem suporte para transações, publicação/assinatura, scripts LUA, chaves com vida útil limitada e definições de configuração para fazer com que o Redis se comporte mais como um cache tradicional.

Outro aspecto fundamental do sucesso do Redis é o ecossistema de software livre íntegro e vibrante criado em torno dele. Isso é refletido no conjunto variado de clientes Redis disponíveis em vários idiomas. Esse ecossistema e uma ampla gama de clientes permitem que o Cache Redis do Azure para ser usado por quase qualquer carga de trabalho construída dentro do Azure.

Para saber mais sobre como começar a usar o Cache Redis do Azure, confira [Como usar o Cache Redis do Azure](cache-dotnet-how-to-use-azure-redis-cache.md) e a [documentação do Cache Redis do Azure](index.md).

### <a name="managed-cache-service"></a>Serviço de Cache gerenciado
[O serviço de Cache Gerenciado foi descontinuado em 30 de novembro de 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Para exibir a documentação arquivada, consulte [Documentação de Serviço de Cache Gerenciado arquivada](https://msdn.microsoft.com/library/azure/dn386094.aspx).

### <a name="in-role-cache"></a>Cache em Função
[Cache na função foi descontinuado em 30 de novembro de 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Para exibir a documentação arquivada, consulte [Documentação de Cache na função arquivada](https://msdn.microsoft.com/library/azure/dn386103.aspx).

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
