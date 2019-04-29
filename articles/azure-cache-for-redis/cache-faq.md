---
title: Perguntas frequentes do Cache do Azure para Redis | Microsoft Docs
description: Conheça as respostas a perguntas, padrões e práticas recomendadas comuns do Cache do Azure para Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: c2c52b7d-b2d1-433a-b635-c20180e5cab2
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: yegu
ms.openlocfilehash: 65e8553969aa92848b1c4496724a7b7754b5d659
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60552021"
---
# <a name="azure-cache-for-redis-faq"></a>Perguntas frequentes sobre o Cache Redis do Azure
Conheça as respostas a perguntas, padrões e práticas recomendadas comuns do Cache do Azure para Redis.

## <a name="what-if-my-question-isnt-answered-here"></a>E se dúvida não foi respondida aqui?
Se sua pergunta não estiver listada aqui, fale conosco e nós ajudaremos a encontrar uma resposta.

* Você pode postar uma pergunta nos comentários no final das perguntas frequentes e entrar em contato com a equipe do Cache do Azure e outros membros da comunidade sobre este artigo.
* Para atingir um público mais amplo, você pode postar uma pergunta sobre o [Fórum do MSDN do Cache do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) e entrar em contato com a equipe do Cache do Azure e outros membros da comunidade.
* Se você quiser fazer uma solicitação de recurso, envie suas solicitações e ideias para o [User Voice do Cache do Azure para Redis](https://feedback.azure.com/forums/169382-cache).
* Você também pode enviar um email para nós com [Comentários externos sobre o Cache do Azure](mailto:azurecache@microsoft.com).

## <a name="azure-cache-for-redis-basics"></a>Noções básicas sobre o Cache do Azure para Redis
As perguntas frequentes nesta seção abordam alguns dos conceitos básicos do Cache do Azure para Redis.

* [O que é o Cache do Azure para Redis?](#what-is-azure-cache-for-redis)
* [Como começo a usar o Cache do Azure para Redis?](#how-can-i-get-started-with-azure-cache-for-redis)

As perguntas frequentes a seguir abordam os conceitos básicos e as perguntas sobre o Cache do Azure para Redis e são respondidas em uma das outras seções das perguntas frequentes.

* [Qual oferta e tamanho do Cache do Azure para Redis devo usar?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Quais clientes do Cache do Azure para Redis posso usar?](#what-azure-cache-for-redis-clients-can-i-use)
* [Há um emulador local para o Cache do Azure para Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Como monitorar a integridade e o desempenho do meu cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Perguntas frequentes sobre planejamento
* [Qual oferta e tamanho do Cache do Azure para Redis devo usar?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Desempenho do Cache do Azure para Redis](#azure-cache-for-redis-performance)
* [Em que região posso localizar meu cache?](#in-what-region-should-i-locate-my-cache)
* [Como eu sou cobrado pelo Cache do Azure para Redis?](#how-am-i-billed-for-azure-cache-for-redis)
* [Posso usar o Cache do Azure para Redis com o Azure Governamental na Nuvem, o Azure China na Nuvem ou o Microsoft Azure Alemanha?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Perguntas frequentes sobre desenvolvimento
* [O que as opções de configuração do StackExchange.Redis fazem?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Quais clientes do Cache do Azure para Redis posso usar?](#what-azure-cache-for-redis-clients-can-i-use)
* [Há um emulador local para o Cache do Azure para Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Como posso executar comandos do Redis?](#how-can-i-run-redis-commands)
* [Por que o Cache do Azure para Redis não tem uma referência da biblioteca de classes MSDN, assim como alguns dos outros serviços do Azure?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Posso usar o Cache do Azure para Redis como um cache de sessão em PHP?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
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
As Perguntas frequentes nesta seção tratam de dúvidas comuns sobre monitoramento e solução de problemas. Para saber mais sobre como monitorar e solucionar problemas de suas instâncias do Cache do Azure para Redis, confira [Como monitorar o Cache do Azure para Redis](cache-how-to-monitor.md) e [Como solucionar problemas do Cache do Azure para Redis](cache-how-to-troubleshoot.md).

* [Como monitorar a integridade e o desempenho do meu cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Por que vejo tempos limite?](#why-am-i-seeing-timeouts)
* [Por que meu cliente foi desconectado do cache?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Perguntas frequentes sobre ofertas anteriores de cache
* [Qual oferta de Cache do Azure é a correta para mim?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-cache-for-redis"></a>O que é o Cache do Azure para Redis?
O Cache do Azure para Redis é baseado no software livre [Redis](https://redis.io/). Ele oferece acesso seguro e dedicado ao Cache do Azure para Redis, que é gerenciado pela Microsoft e pode ser acessado de qualquer aplicativo no Azure. Para obter uma visão mais detalhada, confira a página do produto [Cache do Azure para Redis](https://azure.microsoft.com/services/cache/) em Azure.com.

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Como começo a usar o Cache do Azure para Redis?
Há várias maneiras de começar a usar o Cache do Azure para Redis.

* Você pode conferir um dos nossos tutoriais disponíveis para [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md) e [Python](cache-python-get-started.md).
* Você pode assistir ao vídeo sobre [Como criar aplicativos de alto desempenho usando o Cache do Microsoft Azure para Redis](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Você pode conferir a documentação do cliente para os clientes que correspondem à linguagem de desenvolvimento do seu projeto a fim de saber como usar o Redis. Há muitos clientes Redis que podem ser usados com o Cache do Azure para Redis. Lista de clientes Redis, consulte [https://redis.io/clients](https://redis.io/clients).

Se ainda não tiver uma conta do Azure, você poderá:

* [Abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Obtenha créditos que possam ser usados para experimentar os serviços pagos do Azure. Mesmo depois que os créditos são usados, você pode manter a conta e usar os serviços e recursos gratuitos do Azure.
* [Ativar benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Todos os meses, sua assinatura do MSDN lhe oferece créditos que podem ser usados para serviços pagos do Azure.

<a name="cache-size"></a>

### <a name="what-azure-cache-for-redis-offering-and-size-should-i-use"></a>Qual oferta e tamanho do Cache do Azure para Redis devo usar?
Cada oferta de Cache do Azure para Redis fornece diferentes níveis de opções de **tamanho**, **largura de banda**, **alta disponibilidade** e **SLA**.

Veja abaixo as considerações para a escolha de uma oferta de Cache.

* **Memória**: as camadas Básica e Standard oferecem de 250 MB a 53 GB. A camada Premium oferece até 530 GB. Para obter mais informações, confira [Preços do Cache do Azure para Redis](https://azure.microsoft.com/pricing/details/cache/).
* **Desempenho de rede**: se você tiver uma carga de trabalho que exija uma alta taxa de transferência, a camada Premium oferece mais largura de banda quando comparada às camadas Standard ou Básica. Também dentro de cada camada, caches de tamanhos maiores têm mais largura de banda, devido à VM subjacente que hospeda o cache. Confira a [tabela a seguir](#cache-performance) para saber mais.
* **Taxa de transferência**: a camada Premium oferece a maior taxa de transferência disponível. Se o cliente ou o servidor de cache atingir os limites de largura de banda, você poderá receber tempos limite no lado do cliente. Para obter mais informações, confira a tabela a seguir.
* **Alta Disponibilidade/SLA**: o Cache do Azure para Redis garante que um cache Standard/Premium esteja disponível durante, pelo menos, 99,9% do tempo. Para saber mais sobre nosso SLA, confira [Preço do Cache do Azure para Redis](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). O SLA abrange apenas a conectividade com os pontos de extremidade do Cache. O SLA não abrange a proteção contra perda de dados. Recomendamos usar o recurso de persistência de dados do Redis na camada Premium para aumentar a resiliência contra a perda de dados.
* **Persistência de Dados do Redis**: A camada Premium permite persistir os dados de cache em uma conta do Armazenamento do Azure. Em um cache Básico/Standard, todos os dados são armazenados apenas na memória. Se houver problemas de infraestrutura subjacente, pode haver uma possível perda de dados. Recomendamos usar o recurso de persistência de dados do Redis na camada Premium para aumentar a resiliência contra a perda de dados. O Cache do Azure para Redis oferece opções de RDB e AOF (em breve) na persistência do Redis. Para saber mais, confira [Como configurar a persistência para um Cache do Azure para Redis Premium](cache-how-to-premium-persistence.md).
* **Cluster Redis**: se desejar criar caches maiores que 53 GB ou fragmentar dados entre vários nós do Redis, você pode usar o clustering do Redis que está disponível na camada Premium. Cada nó consiste em um par de cache primário/de réplica para alta disponibilidade. Para saber mais, confira [Como configurar o clustering para um Cache do Azure para Redis Premium](cache-how-to-premium-clustering.md).
* **Segurança e isolamento de rede aprimorados**: A implantação da VNET (Rede Virtual do Azure) melhora a segurança e o isolamento do Cache do Azure para Redis, bem como as sub-redes, as políticas de controle de acesso e outros recursos para restringir ainda mais o acesso. Para saber mais, confira [Como configurar o suporte de Rede Virtual para um Cache do Azure para Redis Premium](cache-how-to-premium-vnet.md).
* **Configurar Redis**: nas camadas Standard e Premium, você pode configurar o Redis para notificações de Keyspace.
* **Quantidade máxima de conexões de cliente**: a camada Premium oferece o número máximo de clientes que podem se conectar ao Redis, com um número maior de conexões para caches de tamanhos maiores. O clustering não aumenta o número de conexões disponíveis para um cache em cluster. Para obter mais informações, confira [Preços do Cache do Azure para Redis](https://azure.microsoft.com/pricing/details/cache/).
* **Núcleo dedicado para o Servidor do Redis**: na camada Premium, todos os tamanhos de cache têm um núcleo dedicado para o Redis. Nas camadas Básica/Standard, o tamanho C1 e acima têm um núcleo dedicado para o servidor do Redis.
* **Como o Redis usa thread único** , ter mais de dois núcleos não oferece um benefício adicional em relação a ter apenas dois, mas tamanhos maiores de VM normalmente têm mais largura de banda que tamanhos menores. Se o cliente ou servidor de cache atingir os limites de largura de banda, você receberá tempos limite no lado do cliente.
* **Aprimoramentos de desempenho**: os caches na camada Premium são implantados no hardware que tem processadores mais rápidos e que oferece um melhor desempenho quando comparado às camadas Básica ou Standard. Os Caches da camada Premium têm a taxa de transferência mais alta e as latências mais baixas.

<a name="cache-performance"></a>

### <a name="azure-cache-for-redis-performance"></a>Desempenho do Cache do Azure para Redis
A tabela a seguir mostra os valores máximos de largura de banda observados durante o teste de vários tamanhos de caches Standard e Premium usando `redis-benchmark.exe` de uma VM de IaaS no ponto de extremidade do Cache do Azure para Redis. Para a taxa de transferência SSL, o redis-benchmark é usado com stunnel para se conectar ao ponto de extremidade do Cache do Azure para Redis.

>[!NOTE] 
>Esses valores não são garantidos e que não há nenhum SLA para esses números, mas eles devem ser típicos. Você deve realizar teste de carga em seu próprio aplicativo para determinar o tamanho de cache certo para ele.
>Esses números podem mudar à medida que postarmos resultados mais novos periodicamente.
>

Podemos tirar as seguintes conclusões desta tabela:

* A taxa de transferência para os caches são do mesmo tamanho é superior na camada Premium em comparação com a camada Standard. Por exemplo, com um Cache de 6 GB, a taxa de transferência de P1 será 180 mil RPS, em comparação com 100 mil para C3.
* Com o cluster Redis, a taxa de transferência aumenta linearmente à medida que o número de fragmentos (nós) no cluster aumenta. Por exemplo, se você criar um cluster P4 de 10 fragmentos, a taxa de transferência disponível será de 400.000 * 10 = 4 milhões de RPS.
* A taxa de transferência tamanhos de chave maiores é mais alta na camada Premium quando comparada à camada Standard.

| Tipo de preço | Tamanho | Núcleos de CPU | Largura de banda disponível | Tamanho do valor de 1 KB | Tamanho do valor de 1 KB |
| --- | --- | --- | --- | --- | --- |
| **Tamanhos de cache padrão** | | |**Megabits por segundo (Mb/s) / Megabytes por segundo (MB/s)** |**RPS (solicitações por segundo) não SSL** |**RPS (solicitações por segundo) SSL** |
| C0 |250 MB |Compartilhado |100 / 12,5 |15.000 |7.500 |
| C1 |1 GB |1 |500 / 62,5 |38.000 |20,720 |
| C2 |2,5 GB |2 |500 / 62,5 |41.000 |37.000 |
| C3 |6 GB |4 |1000 / 125 |100.000 |90.000 |
| C4 |13 GB |2 |500 / 62,5 |60.000 |55.000 |
| C5 |26 GB |4 |1.000 / 125 |102.000 |93.000 |
| C6 |53 GB |8 |2.000 / 250 |126.000 |120.000 |
| **Tamanhos de cache Premium** | |**Núcleos de CPU por fragmento** | **Megabits por segundo (Mb/s) / Megabytes por segundo (MB/s)** |**RPS (solicitações por segundo) não SSL, por fragmento** |**RPS (solicitações por segundo) SSL, por fragmento** |
| P1 |6 GB |2 |1.500 / 187.5 |180,000 |172.000 |
| P2 |13 GB |4 |3.000 / 375 |350.000 |341.000 |
| P3 |26 GB |4 |3.000 / 375 |350.000 |341.000 |
| P4 |53 GB |8 |6.000 / 750 |400.000 |373.000 |

Para obter instruções sobre como configurar stunnel ou baixar as ferramentas do Redis como `redis-benchmark.exe`, consulte a seção [Como posso executar comandos do Redis?](#cache-commands)

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>Em que região posso localizar meu cache?
Para melhor desempenho e menor latência, localize o Cache do Azure para Redis na mesma região que o aplicativo cliente de cache.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Como eu sou cobrado pelo Cache do Azure para Redis?
O preço do Cache do Azure para Redis está [aqui](https://azure.microsoft.com/pricing/details/cache/). A página de preços lista os preços com uma taxa horária. Os caches são cobrados em uma base por minuto do momento em que o cache é criado até o momento em que um cache é excluído. Não há nenhuma opção para parar ou pausar a cobrança de um cache.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Posso usar o Cache do Azure para Redis com o Azure Governamental na Nuvem, o Azure China na Nuvem ou o Microsoft Azure Alemanha?
Sim, o Cache do Azure para Redis está disponível no Azure Governamental na Nuvem, Azure China na Nuvem e Microsoft Azure Alemanha. As URLs para acesso e gerenciamento do Cache do Azure para Redis são diferentes no Azure Governamental na Nuvem e no Azure China na Nuvem em comparação com a Nuvem Pública do Azure. 

| Nuvem   | Sufixo DNS para Redis            |
|---------|---------------------------------|
| Público  | *.redis.cache.windows.net       |
| Gov dos EUA  | *.redis.cache.usgovcloudapi.net |
| Alemanha | *.redis.cache.cloudapi.de       |
| China   | *.redis.cache.chinacloudapi.cn  |

Para obter mais informações sobre considerações ao usar o Cache do Azure para Redis com outras nuvens, consulte os links a seguir.

- [Bancos de dados do Azure Governamental – Cache do Azure para Redis](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Azure China na Nuvem – Cache do Azure para Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)

Para obter mais informações sobre como usar o Cache do Azure para Redis com o PowerShell no Azure Governamental na Nuvem, Azure China na Nuvem e no Microsoft Azure Alemanha, veja [Como se conectar a outras nuvens – PowerShell do Cache do Azure para Redis](cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>O que as opções de configuração do StackExchange.Redis fazem?
O StackExchange.Redis tem muitas opções. Esta seção fala sobre algumas das configurações comuns. Para obter mais informações sobre opções do StackExchange.Redis, consulte [configuração do StackExchange.Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | DESCRIÇÃO | Recomendações |
| --- | --- | --- |
| AbortOnConnectFail |Quando definido como true, a conexão não reconectará após uma falha de rede. |Defina como false e deixe o StackExchange.Redis reconectar-se automaticamente. |
| ConnectRetry |O número de vezes para repetir tentativas de conexão durante a conexão inicial. |Consulte as observações a seguir para se orientar. |
| ConnectTimeout |Tempo limite em ms para operações de conexão. |Consulte as observações a seguir para se orientar. |

Normalmente, os valores padrão do cliente são suficientes. Você pode realizar o ajuste fino das opções com base na sua carga de trabalho.

* **Novas tentativas**
  * Para ConnectRetry e ConnectTimeout, a orientação geral é falhar rapidamente e tentar novamente. Essa diretriz se baseia na sua carga de trabalho e em quanto tempo em média leva para o cliente emitir um comando Redis e receber uma resposta.
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

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Quais clientes do Cache do Azure para Redis posso usar?
Uma das coisas legais sobre o Redis é que há muitos clientes que dão suporte a várias linguagens de programação diferentes. Para obter uma lista atual de clientes, confira [Clientes Redis](https://redis.io/clients). Para obter tutoriais que abrangem várias linguagens e clientes diferentes, confira [Como usar o Cache Redis do Azure](cache-dotnet-how-to-use-azure-redis-cache.md) e seus artigos relacionados no sumário.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Há um emulador local para o Cache do Azure para Redis?
Não há emulador local para o Cache do Azure para Redis, mas é possível executar a versão MSOpenTech do redis-server.exe nas [ferramentas de linha de comando do Redis](https://github.com/MSOpenTech/redis/releases/) no computador local e conectá-lo para obter uma experiência semelhante ao emulador de cache local, conforme mostrado no exemplo a seguir:

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


Se desejar, é possível configurar um arquivo [redis.conf](https://redis.io/topics/config) para corresponder melhor às [configurações de cache padrão](cache-configure.md#default-redis-server-configuration) do Cache do Azure para Redis online.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Como posso executar comandos do Redis?
Você pode usar qualquer comando listado em [Comandos do Redis](https://redis.io/commands#), exceto os listados em [Comandos do Redis sem suporte no Cache do Azure para Redis](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis). Você tem várias opções para executar comandos do Redis.

* Se você tiver um cache Standard ou Premium, poderá executar comandos do Redis usando o [Console do Redis](cache-configure.md#redis-console). O console Redis fornece uma maneira segura para executar comandos do Redis no portal do Azure.
* Também é possível usar as ferramentas de linha de comando do Redis. Para usá-las, você executará as seguintes etapas:
* Baixe as [Ferramentas de linha de comando do Redis](https://github.com/MSOpenTech/redis/releases/).
* Conecte-se ao cache usando `redis-cli.exe`. Passe no ponto de extremidade de cache usando que o comutador -h e a chave usando - a, como mostrado no exemplo a seguir:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> As ferramentas de linha de comando do Redis não funcionam com a porta SSL, mas você pode usar um utilitário como `stunnel` para conectar as ferramentas com segurança à porta SSL seguindo as instruções na postagem de blog [Anunciando o provedor de estado de sessão ASP.NET para versão de visualização do Redis](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) .
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Por que o Cache do Azure para Redis não tem uma referência da biblioteca de classes MSDN, assim como alguns dos outros serviços do Azure?
O Cache do Microsoft Azure para Redis baseia-se no popular Cache do Azure para Redis de software livre e pode ser acessado por uma ampla variedade de [clientes Redis](https://redis.io/clients) para várias linguagens de programação. Cada cliente tem sua própria API, que faz chamadas para a instância de Cache do Azure para Redis usando [comandos do Redis](https://redis.io/commands).

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
>

Para obter mais informações sobre como usar o Cache do Azure para Redis como um cache de sessão de PHP com o cliente PhpRedis, confira [Manipulador de sessão do PHP](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Quais são os bancos de dados do Redis?

Os Bancos de Dados Redis são apenas uma separação lógica dos dados dentro da mesma instância do Redis. A memória do cache é compartilhada entre todos os bancos de dados, e o consumo real da memória de um determinado banco de dados depende de chaves/valores armazenados no banco de dados. Por exemplo, um cache C6 tem 53 GB de memória. Você pode optar por colocar todos os 53 GB em um banco de dados, ou você pode dividi-los entre vários bancos de dados. 

> [!NOTE]
> Ao usar um Cache do Azure para Redis Premium com cluster habilitado, somente o banco de dados 0 estará disponível. Essa limitação é uma limitação do Redis intrínseca e não é específica ao Cache do Azure para Redis. Para saber mais, confira [Preciso fazer alguma alteração no meu aplicativo cliente para usar clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>Quando devo habilitar a porta não SSL para conexão ao Redis?
O servidor Redis não oferece suporte a SSL, mas o Cache do Azure para Redis sim. Se você estiver se conectando ao Cache do Azure para Redis e seu cliente oferecer suporte a SSL, como StackExchange.Redis, você deverá usar SSL.

>[!NOTE]
>A porta não SSL está desabilitada por padrão para novas instâncias do Cache do Azure para Redis. Se o cliente não oferecer suporte a SSL, você deverá habilitar a porta não SSL seguindo as instruções na seção [Portas de acesso](cache-configure.md#access-ports) do artigo [Configurar um cache no Cache do Azure para Redis](cache-configure.md).
>
>

Ferramentas do Redis como o `redis-cli` não funcionam com a porta SSL, mas você pode usar um utilitário como `stunnel` para conectar as ferramentas com segurança à porta SSL seguindo as instruções na postagem de blog [Anunciando o provedor de estado de sessão ASP.NET para versão de visualização do Redis](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

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
* Lembre-se dos custos de desempenho associados a operações diferentes que você está executando. Por exemplo, o `KEYS` comando é uma operação O(n) e deve ser evitado. O [site redis.io](https://redis.io/commands/) apresenta detalhes sobre a complexidade de tempo para cada operação a qual ele dá suporte. Clique em cada comando para ver a complexidade para cada operação.

#### <a name="configuration-and-concepts"></a>Configuração e conceitos
* Use as camadas Standard ou Premium para Sistemas de Produção. A camada Básica é um sistema de nó único sem replicação de dados e sem SLA. Além disso, use pelo menos um cache C1. Caches C0 normalmente são usados para cenários de desenvolvimento/teste simples.
* Lembre-se de que o Redis é um armazenamento de dados **na memória** . Leia [este artigo](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) para que você fique ciente de situações em que pode haver perda de dados.
* Desenvolva seu sistema, de modo que possa lidar com blips de conexão [devido à aplicação de patch e ao failover](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Testes de desempenho
* Comece usando `redis-benchmark.exe` para ter uma ideia de taxa de transferência possível antes de escrever seus próprios testes de desempenho. Porque `redis-benchmark` não dá suporte a SSL, você deve [habilitar a porta não SSL no portal do Azure](cache-configure.md#access-ports) antes de executar o teste. Por exemplo, consulte [Como medir e testar o desempenho do meu cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* A VM do cliente usada para testes deve estar na mesma região que a instância do Cache do Azure para Redis.
* É recomendável usar a Série de VM Dv2 série para o cliente, pois ela tem hardware melhor e deve oferecer melhores resultados.
* Verifique se a VM do cliente escolhida tem ao menos a quantidade de computação e funcionalidade de largura de banda de cache que você está testando.
* Habilite o VRSS no computador cliente, se você estiver usando o Windows. [Consulte aqui para obter detalhes](https://technet.microsoft.com/library/dn383582.aspx).
* As instâncias do Redis nível Premium terão melhor latência de rede e taxa de transferência porque estão em execução tanto para a CPU quanto para a Rede.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Quais são algumas das considerações ao usar os comandos comuns do Redis?
* Você não deve executar determinados comandos do Redis que levam muito tempo para ser concluídos sem compreender o impacto desses comandos.
  * Por exemplo, não execute o comando [CHAVES](https://redis.io/commands/keys) durante a produção, já que ele pode levar muito tempo para retornar dependendo do número de chaves. O Redis é um servidor de thread único e processa um comando por vez. Se você emitir outros comandos após CHAVES, eles não serão processados até que o Redis processe o comando CHAVES. O [site redis.io](https://redis.io/commands/) apresenta detalhes sobre a complexidade de tempo para cada operação a qual ele dá suporte. Clique em cada comando para ver a complexidade para cada operação.
* Tamanhos de chave - devo usar valores pequenos ou grandes de chave? Em geral, depende do cenário. Se seu cenário exigir chaves maiores, você poderá ajustar os valores de ConnectionTimeout e de novas tentativas e ajustar sua lógica de repetição de tentativas. Da perspectiva do servidor Redis, valores menores têm um desempenho melhor.
* Essas considerações não significam que você não pode armazenar valores maiores em Redis, mas que deve estar ciente das considerações a seguir. As latências serão maiores. Se você tiver um conjunto de dados maior e um menor, você pode usar várias instâncias do ConnectionMultiplexer, cada uma configurada com um conjunto diferente de valores de tempo limite e de repetição de tentativa, conforme descrito anteriormente na seção [O que as opções de configuração de StackExchange.Redis fazem](#cache-configuration) .

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Como medir e testar o desempenho do meu cache?
* [Habilite o diagnóstico de cache](cache-how-to-monitor.md#enable-cache-diagnostics) para que você possa [monitorar](cache-how-to-monitor.md) a integridade do cache. Você pode exibir as métricas no portal do Azure e também pode [baixá-las e analisá-las](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) usando as ferramentas de sua escolha.
* Você pode usar o redis-benchmark.exe para testar a carga em seu servidor do Redis.
* Verifique se o cliente de teste de carga e o Cache do Azure para Redis estão na mesma região.
* Use o redis cli.exe e monitore o cache usando o comando INFO.
* Se sua carga está causando um nível elevado de fragmentação de memória, você deve escalar verticalmente para um tamanho de cache maior.
* Para obter instruções sobre como baixar as ferramentas do Redis, consulte a seção [Como posso executar comandos do Redis?](#cache-commands)

Os comandos a seguir fornecem um exemplo de uso benchmark.exe redis. Para obter resultados precisos, execute esses comandos de uma VM na mesma região que o cache.

* Teste solicitações SET de Pipeline usando um conteúdo de 1k

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Solicitações GET de Pipelined usando uma conteúdo de 1k.
  OBSERVAÇÃO:  primeiro execute o teste SET abaixo para popular o cache

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

* No ASP.NET, use a [definição de configuração “minIoThreads” ou “minWorkerThreads”]["minIoThreads" configuration setting] no elemento de configuração `<processModel>` no web.config. Se você estiver executando nos Sites do Azure, essa configuração não será exposta pelas opções de configuração. No entanto, você deve ainda ser capaz de definir essa configuração por meio de programação (veja abaixo) do seu método Application_Start em global.asax.cs.

  > [!NOTE] 
  > O valor especificado nesse elemento de configuração é uma *configuração por núcleo*. Por exemplo, se você tiver um computador com quatro núcleos e desejar que sua configuração minIOThreads seja de 200 no tempo de execução, use `<processModel minIoThreads="50"/>`.
  >

* Fora do ASP.NET e do Azure WebSites global.asax, use o [ThreadPool.SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) API.

  > [!NOTE]
  > O valor especificado por essa API é uma configuração global que afeta todo o AppDomain. Se tiver uma máquina com quatro núcleos e desejar definir as configurações minWorkerThreads e minIOThreads como 50 por CPU durante o tempo de execução, use ThreadPool.SetMinThreads (200, 200).

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Habilitar a GC (coleta de lixo) do servidor para obter mais produtividade no cliente ao usar o StackExchange.Redis
Habilitar a GC do servidor pode otimizar o cliente e proporcionar melhor desempenho e produtividade ao usar o Stackexchange.Redis. Para saber mais sobre a GC do servidor e como habilitá-la, confira os artigos a seguir:

* [Para habilitar a GC (coleta de lixo) do servidor](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Noções básicas sobre a coleta de lixo](/dotnet/standard/garbage-collection/fundamentals)
* [Coleta de lixo e desempenho](/dotnet/standard/garbage-collection/performance)


### <a name="performance-considerations-around-connections"></a>Considerações sobre desempenho em torno de conexões

Cada tipo de preço tem limites diferentes para conexões de cliente, memória e largura de banda. Embora cada tamanho de cache permita *até* um determinado número de conexões, cada conexão com o Redis tem sobrecarga associadas. Um exemplo de tal sobrecarga seria o uso da CPU e de memória como resultado de criptografia TLS/SSL. O limite máximo de conexão para um tamanho de cache determinado pressupõe um cache com pouca carga. Se a carga da conexão de sobrecarga *mais* a carga de operações do cliente excederem a capacidade do sistema, o cache poderá enfrentar problemas de capacidade mesmo se não exceder o limite de conexão para o tamanho atual do cache.

Para obter mais informações sobre os limites de conexões diferentes para cada camada, consulte [preços do Cache do Azure para Redis](https://azure.microsoft.com/pricing/details/cache/). Para obter mais informações sobre as conexões e outras configurações padrão, consulte [configuração do servidor padrão Redis](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Como monitorar a integridade e o desempenho do meu cache?
As instâncias do Cache do Microsoft Azure para Redis podem ser monitoradas no [portal do Azure](https://portal.azure.com). Você pode exibir métricas, fixar gráficos de métricas ao quadro inicial, personalizar o intervalo de data e hora de gráficos de monitoramento, adicionar/remover as métricas de gráficos e definir alertas quando determinadas condições forem atendidas. Para obter mais informações, confira [Como monitorar o Cache do Azure para Redis](cache-how-to-monitor.md).

O **menu Recursos** do Cache do Azure para Redis também contém várias ferramentas para monitorar e solucionar problemas em seus caches.

* **Diagnosticar e solucionar problemas** fornece informações sobre problemas comuns e estratégias para resolvê-los.
* **Resource Health** observa seu recurso e informa se ele está sendo executado conforme o esperado. Para saber mais sobre o serviço Azure Resource Health, confira [Visão geral do Azure Resource Health](../resource-health/resource-health-overview.md).
* **Nova solicitação de suporte** fornece opções para abrir uma solicitação de suporte para seu cache.

Essas ferramentas permitem monitorar a integridade de suas instâncias do Cache do Azure para Redis e ajudá-lo a gerenciar seus aplicativos de cache. Para obter mais informações, consulte a seção “Configurações de solução de problemas e suporte” em [Como configurar o Cache do Azure para Redis](cache-configure.md).

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
  * Com a oferta de cache padrão, o serviço de Cache do Azure para Redis iniciou um failover do nó primário para o nó secundário.
  * O Azure estava aplicando um patch na instância onde o cache foi implantado
    * Isso pode ser para atualizações de servidor do Redis ou manutenção geral de VM.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Qual oferta de Cache do Azure é a correta para mim?
> [!IMPORTANT]
> De acordo com o [comunicado](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) do ano passado, o Serviço de Cache Gerenciado do Azure e o serviço Cache na Função do Azure **foram desativados** em 30 de novembro de 2016. Recomenda-se usar o [Cache do Azure para Redis](https://azure.microsoft.com/services/cache/). Para saber mais sobre a migração, confira [Migrar do Serviço de Cache Gerenciado para o Cache do Azure para Redis](cache-migrate-to-redis.md).
>
>

### <a name="azure-cache-for-redis"></a>Cache Redis do Azure
O Cache do Azure para Redis está disponível em tamanhos de até 53 GB e tem SLA de disponibilidade de 99,9%. A nova [camada premium](cache-premium-tier-intro.md) oferece tamanhos de até 530 GB e suporte para clustering, VNET e persistência, com um SLA de 99,9%.

Os clientes têm acesso ao uso de um Cache do Azure para Redis seguro e dedicado, gerenciado pela Microsoft. Com essa oferta, você pode aproveitar o rico conjunto de recursos e ecossistema fornecido pelo Redis, além de hospedagem e monitoramento confiáveis da Microsoft.

Diferentemente dos caches tradicionais que lidam exclusivamente com pares chave-valor, o Redis é popular por seus tipos de dados altamente funcionais. O Redis também permite executar operações atômicas nesses tipos, como acréscimos a uma cadeia de caracteres; incrementação de valor em hash; envios para uma lista; intersecção, união e diferença em conjuntos de computação; ou colocar o membro com classificação mais alta em um conjunto ordenado. Outros recursos incluem suporte para transações, publicação/assinatura, scripts LUA, chaves com vida útil limitada e definições de configuração para fazer com que o Redis se comporte mais como um cache tradicional.

Outro aspecto fundamental do sucesso do Redis é o ecossistema de software livre íntegro e vibrante criado em torno dele. Isso é refletido no conjunto variado de clientes Redis disponíveis em vários idiomas. Esse ecossistema e uma ampla gama de clientes permitem que o Cache do Azure para Redis seja usado por quase qualquer carga de trabalho construída dentro do Azure.

Para saber mais sobre como começar a usar o Cache do Azure para Redis, consulte [Como usar o Cache do Azure para Redis](cache-dotnet-how-to-use-azure-redis-cache.md) e a [documentação do Cache do Azure para Redis](index.md).

### <a name="managed-cache-service"></a>Serviço de Cache gerenciado
[O serviço de Cache Gerenciado foi descontinuado em 30 de novembro de 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Para exibir a documentação arquivada, consulte [Documentação de Serviço de Cache Gerenciado arquivada](/previous-versions/azure/azure-services/dn386094(v=azure.100)).

### <a name="in-role-cache"></a>Cache em Função
[Cache na função foi descontinuado em 30 de novembro de 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Para exibir a documentação arquivada, consulte [Documentação de Cache na função arquivada](/previous-versions/azure/azure-services/dn386103(v=azure.100)).

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
