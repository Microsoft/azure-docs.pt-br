---
title: Configurar clustering do Redis-cache Premium do Azure para Redis
description: Saiba como criar e gerenciar o clustering do Redis para as instâncias de camada Premium do Cache do Azure para Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: f1e84c838d310721cba604274388ae2767eb1502
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389664"
---
# <a name="configure-redis-clustering-for-a-premium-azure-cache-for-redis-instance"></a>Configurar o clustering do Redis para um cache do Azure Premium para a instância do Redis

O Cache do Azure para Redis oferece o cluster Redis como [implementado no Redis](https://redis.io/topics/cluster-tutorial). Com o Cluster Redis, você obtém os seguintes benefícios: 

* A capacidade de dividir automaticamente o conjunto de dados entre vários nós. 
* A capacidade de continuar as operações quando um subconjunto dos nós estiver apresentando falhas ou não conseguir se comunicar com o restante do cluster. 
* Mais taxa de transferência: a taxa de transferência aumenta linearmente à medida que o número de fragmentos aumenta. 
* Mais tamanho de memória: aumenta linearmente à medida que o número de fragmentos aumenta.  

O clustering não aumenta o número de conexões disponíveis para um cache em cluster. Para obter mais informações sobre tamanho, taxa de transferência e largura de banda com caches Premium, consulte [escolher a camada correta](cache-overview.md#choosing-the-right-tier)

No Azure, o cluster Redis é oferecido como um modelo primário/de réplica em que cada fragmento tem um par primário/de réplica, com replicação gerenciada pelo serviço de Cache do Azure para Redis. 

## <a name="set-up-clustering"></a>Configurar o clustering

O clustering é habilitado na folha **Novo Cache do Azure para Redis** durante a criação do cache. 

1. Para criar um cache Premium, entre no [portal do Azure](https://portal.azure.com) e selecione **criar um recurso**. Além de criar caches no portal do Azure, você também pode criá-los usando os modelos do Resource Manager, PowerShell ou CLI do Azure. Para obter mais informações sobre a criação de um Cache do Azure para Redis, consulte [Criar um cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Criar recurso.":::
   
2. Na página **Novo**, selecione **Bancos de dados** e, em seguida, **Cache do Azure para Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Selecionar o Cache do Azure para Redis.":::

3. Na página **novo cache Redis** , defina as configurações para seu novo cache Premium.
   
   | Configuração      | Valor sugerido  | DESCRIÇÃO |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Insira um nome global exclusivo. | O nome de cache precisa ser uma cadeia de caracteres com 1 a 63 caracteres que contém somente números, letras ou hifens. O nome precisa começar e terminar com um número ou uma letra e não pode conter hifens consecutivos. O *nome do host* de sua instância de cache será *\<DNS name>.redis.cache.windows.net*. | 
   | **Assinatura** | Na lista suspensa e selecione sua assinatura. | A assinatura na qual essa nova instância do Cache do Azure para Redis será criada. | 
   | **Grupo de recursos** | Menu suspenso e selecione um grupo de recursos ou selecione **criar novo** e insira um novo nome de grupo de recursos. | Nome do grupo de recursos no qual o cache e outros recursos serão criados. Ao colocar todos os seus recursos de aplicativos em um só grupo de recursos, você pode gerenciá-los ou excluí-los juntos com facilidade. | 
   | **Localidade** | Menu suspenso e selecione um local. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que usarão o cache. |
   | **Tipo de cache** | Menu suspenso e selecione um cache Premium para configurar os recursos premium. Para obter detalhes, veja [preços do cache do Azure para Redis](https://azure.microsoft.com/pricing/details/cache/). |  O tipo de preço determina o tamanho, o desempenho e os recursos disponíveis para o cache. Para obter mais informações, confira [Visão geral do Cache do Azure para Redis](cache-overview.md). |

4. Selecione a guia **Rede** ou clique no botão **Rede** na parte inferior da página.

5. Na guia **Rede**, escolha o método de conectividade. Para instâncias de cache Premium, você pode se conectar de forma pública, por meio de endereços IP públicos ou pontos de extremidade de serviço, ou, em particular, usando um ponto de extremidade privado.

6. Selecione **Próximo: Avançado** ou clique no botão **Próximo: Avançado** na parte inferior da página.

7. Na guia **avançado** de uma instância de cache Premium, defina as configurações para porta não TLS, clustering e persistência de dados. Para habilitar o clustering, clique em **habilitar**.

    :::image type="content" source="media/cache-how-to-premium-clustering/redis-cache-clustering.png" alt-text="Alternância de clustering.":::

    Você pode ter até 10 fragmentos no cluster. Depois de clicar em **habilitar**, deslize o controle deslizante ou digite um número entre 1 e 10 para **contagem de fragmentos** e clique em **OK**.

    Cada fragmento é um par de cache primário/de réplica gerenciado pelo Azure, e o tamanho total do cache é calculado multiplicando o número de fragmentos pelo tamanho do cache selecionado no tipo de preço.

    :::image type="content" source="media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png" alt-text="Alternância de clustering selecionada.":::

    Depois que o cache for criado, conecte-se a ele e use-o apenas como um cache não clusterizado, e o Redis distribui os dados por todos os fragmentos do Cache. Se o diagnóstico for [habilitado](cache-how-to-monitor.md#enable-cache-diagnostics), as métricas serão capturadas separadamente para cada fragmento e poderão ser [visualizadas](cache-how-to-monitor.md) na folha Cache do Azure para Redis. 

8. Selecione **Próximo: Marcas** ou clique no botão **Próximo: Botão** Categorias na parte inferior da página.

9. Opcionalmente, na guia **Marcas**, insira o nome e o valor caso deseje categorizar o recurso. 

10. Selecione **Examinar + criar**. Você será levado para a guia Examinar + criar, na qual o Azure validará a configuração.

11. Depois que a mensagem em verde Validação aprovada for exibida, selecione **Criar**.

A criação do cache demora um pouco. Monitore o progresso na página **Visão Geral** do Cache do Azure para Redis. Quando o **Status** for mostrado como **Em execução**, o cache estará pronto para uso. 

> [!NOTE]
> 
> Há algumas pequenas diferenças necessárias em seu aplicativo cliente quando o cluster é configurado. Para saber mais, confira [Preciso fazer alguma alteração no meu aplicativo cliente para usar clustering?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

Para obter um exemplo de código sobre como trabalhar com clustering com o cliente StackExchange.Redis, confira a parte [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) da amostra [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Alterar o tamanho do cluster em um cache premium em execução
Para alterar o tamanho do cluster em um cache Premium em execução com clustering habilitado, clique em **tamanho do cluster** no **menu de recursos**.

![Tamanho do cluster Redis][redis-cache-redis-cluster-size]

Para alterar o tamanho do cluster, use o controle deslizante ou digite um número entre 1 e 10 na caixa de texto **Contagem de fragmentos** e clique em **OK** para salvar.

Aumentar o tamanho do cluster aumenta a taxa de transferência máxima e o tamanho do cache. Aumentar o tamanho do cluster não aumenta o as conexões máximas disponíveis para clientes.

> [!NOTE]
> O dimensionamento de um cluster executa o comando [MIGRAR](https://redis.io/commands/migrate), que é um comando oneroso. Assim, tendo em vista o impacto mínimo, considere a execução dessa operação fora do horário de pico. Durante o processo de migração, você verá um aumento na carga do servidor. O dimensionamento de um cluster é um processo demorado, e o tempo necessário depende do número de chaves e do tamanho dos valores associados a essas chaves.
> 
> 

## <a name="clustering-faq"></a>Perguntas frequentes sobre clustering

A lista a seguir contém respostas para perguntas frequentes sobre o clustering do Cache do Azure para Redis.

* [Preciso fazer alguma alteração no meu aplicativo cliente para usar clustering?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Como as chaves são distribuídas em um cluster?](#how-are-keys-distributed-in-a-cluster)
* [Qual é o maior tamanho de cache que eu posso criar?](#what-is-the-largest-cache-size-i-can-create)
* [Todos os clientes do Redis dão suporte ao clustering?](#do-all-redis-clients-support-clustering)
* [Como posso me conectar ao meu cache quando o clustering estiver habilitado?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Posso me conectar diretamente aos fragmentos individuais do meu cache?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Posso configurar o clustering para um cache criado anteriormente?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Posso configurar o clustering para um cache básico ou standard?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Posso usar clustering com os provedores de Estado de Sessão ASP.NET Redis e Caching de Saída?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Estou recebendo exceções MOVE ao usar StackExchange.Redis e clustering. O que devo fazer?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>Preciso fazer alguma alteração no meu aplicativo cliente para usar clustering?
* Quando o clustering estiver habilitado, somente o banco de dados 0 estará disponível. Se seu aplicativo cliente usa vários bancos de dados e tenta ler ou gravar em um banco de dados diferente de 0, a seguinte exceção é lançada. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Para saber mais, confira [Redis Cluster Specification - Implemented subset (Especificação do cluster Redis — subconjunto implementado)](https://redis.io/topics/cluster-spec#implemented-subset).
* Se você estiver usando [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/), use a versão 1.0.481 ou posterior. É possível se conectar ao cache usando os mesmos [pontos de extremidade, portas e chaves](cache-configure.md#properties) usados ao se conectar a um cache que não tem o clustering habilitado. A única diferença é que todas as leituras e gravações devem ser feitas no banco de dados 0.
  
  Outros clientes podem ter requisitos diferentes. Veja [Todos os clientes do Redis dão suporte ao clustering?](#do-all-redis-clients-support-clustering)
* Se seu aplicativo usa várias operações de chave em lote em um único comando, todas as chaves devem estar localizadas no mesmo fragmento. Para localizar chaves no mesmo fragmento, consulte [Como as chaves são distribuídas em um cluster?](#how-are-keys-distributed-in-a-cluster)
* Se estiver usando o provedor de Estado de Sessão ASP.NET Redis, você deverá usar a versão 2.0.1 ou superior. Veja [Posso usar clustering com os provedores de Estado de Sessão ASP.NET Redis e Caching de Saída?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Como as chaves são distribuídas em um cluster?
De acordo com a documentação do [modelo de distribuição de chaves](https://redis.io/topics/cluster-spec#keys-distribution-model) do Redis: o espaço da chave é dividido em 16.384 slots. Cada chave é resumida e atribuída a um desses slots, que são distribuídos entre os nós do cluster. Você pode configurar qual parte da chave é resumida para garantir que várias chaves estejam localizadas no mesmo fragmento usando hashtags.

* Chaves com marca hash – se alguma parte da chave estiver entre `{` e `}`, somente essa parte da chave terá hash, a fim de determinar o slot de hash de uma chave. Por exemplo, as 3 chaves a seguir devem estar localizadas no mesmo fragmento:  `{key}1`, `{key}2` e `{key}3`, uma vez que apenas a parte `key` do nome está entre chaves. Para obter uma lista completa das especificações da marca hash de chaves, confira [Keys hash tags](https://redis.io/topics/cluster-spec#keys-hash-tags).
* Chaves sem uma hashtag: o nome completo da chave é usado para fazer hash. Isso resulta em uma distribuição estatisticamente uniforme entre os fragmentos do cache.

Para obter um melhor desempenho e uma melhor taxa de transferência, recomendamos distribuir as chaves por igual. Se você estiver usando chaves com uma hashtag, é responsabilidade do aplicativo garantir que as chaves sejam distribuídas uniformemente.

Para saber mais, confira [Keys distribution model](https://redis.io/topics/cluster-spec#keys-distribution-model), [Redis Cluster data sharding](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding) e [Keys hash tags](https://redis.io/topics/cluster-spec#keys-hash-tags).

Para obter um exemplo de código sobre como trabalhar com clustering e localizar chaves no mesmo fragmento com o cliente StackExchange.Redis, confira a parte [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) da amostra [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Qual é o maior tamanho de cache que eu posso criar?
O maior tamanho de cache Premium é de 120 GB. Você pode criar até 10 fragmentos, fornecendo um tamanho máximo de 1,2 TB GB. Se precisar de um tamanho maior, você pode [solicitar mais](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Para obter mais informações, consulte [Preço do Cache do Azure para Redis](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Todos os clientes do Redis dão suporte ao clustering?
Nem todos os clientes dão suporte ao clustering Redis! Verifique a documentação da biblioteca que você está usando para verificar se você está usando uma biblioteca e uma versão que dão suporte a clustering. StackExchange. Redis é uma biblioteca que oferece suporte a clustering, em suas versões mais recentes. Para obter mais informações sobre outros clientes, confira a seção [Playing with the cluster](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) do [Redis cluster tutorial](https://redis.io/topics/cluster-tutorial). 

O protocolo de clustering Redis requer que cada cliente se conecte a cada fragmento diretamente no modo de clustering e também defina novas respostas de erro, como ' MOVEd ' na ' CROSSSLOTS '. A tentativa de usar um cliente que não dá suporte ao clustering com um cache de modo de cluster pode resultar em muitas [exceções de redirecionamento movidas](https://redis.io/topics/cluster-spec#moved-redirection)ou apenas interromper seu aplicativo, se você estiver fazendo solicitações de várias chaves de slot.

> [!NOTE]
> Se estiver usando o StackExchange.Redis como seu cliente, verifique se está usando a versão mais recente do [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 ou posterior para que o clustering funcione corretamente. Se você tiver problemas com as exceções de movimentação, confira [exceções move](#move-exceptions) para obter mais informações.
>

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Como posso me conectar ao meu cache quando o clustering estiver habilitado?
Você pode se conectar ao seu cache usando os mesmos [pontos de extremidade](cache-configure.md#properties), [portas](cache-configure.md#properties) e [chaves](cache-configure.md#access-keys) usados ao conectar-se a um cache que não tem o clustering habilitado. O Redis gerencia o clustering no back-end para que você não precise gerenciá-lo por meio de seu cliente.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Posso me conectar diretamente aos fragmentos individuais do meu cache?
O protocolo de clustering exige que o cliente faça as conexões de fragmento corretas. Portanto, o cliente deve fazer isso corretamente para você. Dito isso, cada fragmento consiste em um par de cache primário/de réplica que é conhecido coletivamente como uma instância de cache. Você pode se conectar a essas instâncias de cache usando o utilitário redis-cli na ramificação [instável](https://redis.io/download) do repositório do Redis no GitHub. Esta versão implementa o suporte básico quando iniciado com o switch `-c` . Para obter mais informações, consulte [jogando com o cluster](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) no [https://redis.io](https://redis.io) [tutorial do cluster Redis](https://redis.io/topics/cluster-tutorial).

Para não TLS, use os comandos a seguir.

```bash
Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
...
Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)
```

Para TLS, substitua `1300N` por `1500N` .

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Posso configurar o clustering para um cache criado anteriormente?
Sim. Primeiro, verifique se o cache é Premium, dimensionando se não for. Em seguida, você deve ser capaz de ver as opções de configuração de cluster, incluindo uma opção para habilitar o cluster. Você pode alterar o tamanho do cluster após a criação do cache ou depois de ter habilitado o clustering pela primeira vez.

   >[!IMPORTANT]
   >Não é possível desfazer a habilitação do clustering. E um cache com clustering habilitado e apenas um fragmento se comporta de *forma diferente* de um cache do mesmo tamanho *sem* clustering.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Posso configurar o clustering para um cache básico ou standard?
O clustering está disponível apenas para os caches premium.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Posso usar clustering com os provedores de Estado de Sessão ASP.NET Redis e Caching de Saída?
* **Provedor de Cache de Saída Redis** : sem necessidade de alterações.
* **Provedor de Estado de Sessão Redis** : para usar o clustering, você deve usar [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 ou superior; do contrário, uma exceção será lançada. Essa é uma alteração significativa; para obter mais informações, consulte [v 2.0.0 de alteração significativa](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Estou recebendo exceções MOVE ao usar StackExchange.Redis e clustering. O que devo fazer?
Se você estiver usando StackExchange.Redis e receber exceções `MOVE` ao usar clustering, verifique se está usando [StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) ou posterior. Para obter instruções sobre como configurar seus aplicativos .NET para usar StackExchange.Redis, confira [Configurar os clientes de cache](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o cache do Azure para recursos do Redis.

* [Cache do Azure para camadas de serviço do Redis Premium](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png
