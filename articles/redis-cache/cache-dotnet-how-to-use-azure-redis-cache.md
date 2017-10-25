---
title: Como usar o Cache Redis do Azure | Microsoft Docs
description: Saiba como melhorar o desempenho de seus aplicativos do Azure com o Cache Redis do Azure
services: redis-cache,app-service
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: c502f74c-44de-4087-8303-1b1f43da12d5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/27/2017
ms.author: sdanie
ms.openlocfilehash: 3dfc026490093523446650c510dbebdd660e8b6b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-azure-redis-cache"></a>Como utilizar o cache Redis do Azure
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Este guia mostra como começar a usar o **Cache Redis do Azure**. O cache Redis do Microsoft Azure é baseado no popular software livre Cache Redis. Ele fornece acesso a um cache Redis dedicado e seguro, gerenciado pela Microsoft. Um cache criado usando o cache Redis do Azure é acessível de qualquer aplicativo do Microsoft Azure.

O Cache Redis do Microsoft Azure está disponível nas seguintes camadas:

* **Básico** – um único nó. Vários tamanhos acima de 53 GB.
* **Standard** – principal/réplica com dois nós. Vários tamanhos acima de 53 GB. SLA de 99,9%.
* **Premium** – dois nós Primário/Réplica com até 10 fragmentos. Vários tamanhos de 6 GB a 530 GB. Todos os recursos do tipo Standard e outros, incluindo suporte para [cluster Redis](cache-how-to-premium-clustering.md), [persistência Redis](cache-how-to-premium-persistence.md) e [Rede Virtual do Azure](cache-how-to-premium-vnet.md). SLA de 99,9%.

Cada camada é diferente em termos de recursos e preços. Para saber mais sobre preços, consulte [Detalhes de preços do cache][Cache Pricing Details].

Este guia mostra a você como usar o cliente [StackExchange.Redis][StackExchange.Redis] usando o código em C\#. Os cenários abordados incluem a **criação e configuração de um cache**, a **configuração de clientes de cache** e a **adição e remoção de objetos do cache**. Para obter mais informações sobre como usar o Cache Redis do Azure, consulte [Próximas etapas][Next Steps]. Para obter um tutorial passo a passo da complicação de um aplicativo Web ASP.NET MVC com o Cache Redis, confira [Como criar um Aplicativo Web com o Cache Redis](cache-web-app-howto.md).

<a name="getting-started-cache-service"></a>

## <a name="get-started-with-azure-redis-cache"></a>Introdução ao Cache Redis do Azure
Começar a usar o cache Redis do Azure é fácil. Para começar, você provisiona e configura um cache. Em seguida, você configura os clientes de cache para que possam acessar o cache. Quando os clientes de cache estiverem configurados, você pode começar a trabalhar com eles.

* [Criar o cache][Create the cache]
* [Configurar os clientes de cache][Configure the cache clients]

<a name="create-cache"></a>

## <a name="create-a-cache"></a>Criar um cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

### <a name="to-access-your-cache-after-its-created"></a>Para acessar o cache depois que ele for criado
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Para saber mais sobre como configurar o cache, confira [Como configurar o Cache Redis do Azure](cache-configure.md).

<a name="NuGet"></a>

## <a name="configure-the-cache-clients"></a>Configurar os clientes de cache
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

Depois que o projeto de cliente estiver configurado para caching, você poderá usar as técnicas descritas nas seções a seguir para trabalhar com o cache.

<a name="working-with-caches"></a>

## <a name="working-with-caches"></a>Trabalhando com caches
As etapas desta seção descrevem como realizar tarefas comuns com o Cache.

* [Conectar-se ao cache][Connect to the cache]
* [Adicionar e recuperar objetos do cache][Add and retrieve objects from the cache]
* [Trabalhar com objetos .NET no cache](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>

## <a name="connect-to-the-cache"></a>Conectar-se ao cache
Para trabalhar de forma programática com um cache, você precisa de uma referência ao cache. Adicione o seguinte à parte superior de qualquer arquivo do qual você deseja usar o cliente StackExchange.Redis para acessar um cache Redis do Azure:

    using StackExchange.Redis;

> [!NOTE]
> O cliente StackExchange.Redis requer o .NET Framework 4 ou posterior.
> 
> 

A conexão com o Cache Redis do Azure é gerenciada pela classe `ConnectionMultiplexer` . Essa classe deve ser compartilhada e reutilizada em todo aplicativo cliente e não precisa ser criada para cada operação. 

Para conectar-se a um Cache Redis do Azure e obter uma instância de um `ConnectionMultiplexer` conectado, chame o método estático `Connect` e passe a chave e o ponto de extremidade do cache. Use a chave gerada do Portal do Azure como o parâmetro de senha.

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

> [!IMPORTANT]
> Aviso: nunca armazene credenciais no código-fonte. Para manter esse exemplo simples, eu estou mostrando-lhes no código-fonte. Consulte [Como cadeias de caracteres de aplicativo e cadeias de caracteres de conexão funcionam][How Application Strings and Connection Strings Work] para obter informações sobre como armazenar credenciais.
> 
> 

Se não desejar usar o SSL, defina `ssl=false` ou omita o parâmetro `ssl`.

> [!NOTE]
> A porta não SSL é desabilitada por padrão para novos caches. Para obter instruções sobre como habilitar a porta não SSL, consulte [Portas de acesso](cache-configure.md#access-ports).
> 
> 

Uma abordagem para compartilhar uma instância do `ConnectionMultiplexer` em seu aplicativo deve ter uma propriedade estática que retorna uma instância conectada, semelhante ao exemplo a seguir. Essa abordagem oferece uma maneira thread-safe de inicializar somente uma instância única conectada do `ConnectionMultiplexer`. Nestes exemplos, `abortConnect` é definido como false, o que significa que a chamada terá êxito mesmo que não seja possível estabelecer uma conexão com o Cache Redis do Azure. Um recurso chave do `ConnectionMultiplexer` é que ele restaura automaticamente a conectividade com o cache assim que o problema de rede ou outras causas sejam resolvidos.

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

Para obter mais informações nas opções de configuração de conexão avançada, consulte [Modelo de configuração de StackExchange.Redis][StackExchange.Redis configuration model].

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

Quando a conexão for estabelecida, retorne uma referência para o banco de dados do redis cache chamando o método `ConnectionMultiplexer.GetDatabase` . O objeto retornado pelo método `GetDatabase` é um objeto leve de passagem e não precisa ser armazenado.

    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = Connection.GetDatabase();

    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);

    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

Os caches Redis do Azure têm um número configurável de bancos de dados (padrão de 16) que podem ser usados para separar logicamente os dados em um cache Redis. Para saber mais, veja [O que são os bancos de dados do Redis?](cache-faq.md#what-are-redis-databases) e [Configuração padrão do servidor Redis](cache-configure.md#default-redis-server-configuration).

Agora que você já sabe como conectar-se a uma instância do Cache Redis do Azure e retornar uma referência para o banco de dados do cache, vamos ver como trabalhar com o cache.

<a name="add-object"></a>

## <a name="add-and-retrieve-objects-from-the-cache"></a>Adicionar e recuperar objetos do cache
Itens podem ser armazenados e recuperados de um cache usando os métodos `StringSet` e `StringGet`.

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");

    string value = cache.StringGet("key1");

O Redis armazena mais dados como cadeias de caracteres Redis, mas essas cadeias de caracteres podem conter muitos tipos de dados, incluindo dados binários serializados, que podem ser usados ao armazenar objetos .NET no cache.

Ao se chamar `StringGet`, se o objeto existir, ele será retornado e, se não existir, será retornado `null`. Se `null` for retornado, você poderá recuperar o valor da fonte de dados desejada e armazená-lo no cache para uso subsequente. Esse padrão de uso é conhecido como padrão cache-aside.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

Você também pode usar `RedisValue`, conforme mostra o exemplo a seguir. O `RedisValue` tem operadores implícitos para trabalhar com tipos de dados integrais e pode ser útil se `null` for um valor esperado para um item em cache.


    RedisValue value = cache.StringGet("key1");
    if (!value.HasValue)
    {
        value = GetValueFromDataSource();
        cache.StringSet("key1", value);
    }


Para especificar a expiração de um item no cache, use o parâmetro `TimeSpan` de `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## <a name="work-with-net-objects-in-the-cache"></a>Trabalhar com objetos .NET no cache
O Cache Redis do Azure pode armazenar objetos .NET e tipos de dados primitivos em cache, mas antes que um objeto .NET seja armazenado em cache, ele deve ser serializado. Essa serialização de objetos .NET é de responsabilidade do desenvolvedor do aplicativo e proporciona ao desenvolvedor a flexibilidade na escolha do serializador.

Uma maneira simples de serializar objetos é usar os métodos de serialização do `JsonConvert` em [Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1) e serializar para e do JSON. O exemplo a seguir mostra um get e set usando uma instância do objeto `Employee` .

    class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }

        public Employee(int EmployeeId, string Name)
        {
            this.Id = EmployeeId;
            this.Name = Name;
        }
    }

    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));

    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu os conceitos básicos, siga estes links para saber mais sobre o Cache Redis do Azure.

* Confira os provedores ASP.NET para o Cache Redis do Azure.
  * [Provedor de estado de sessão do Redis do Azure](cache-aspnet-session-state-provider.md)
  * [Provedor de Cache de Saída ASP.NET do Cache Redis do Azure](cache-aspnet-output-cache-provider.md)
* [Habilite o diagnóstico de cache](cache-how-to-monitor.md#enable-cache-diagnostics) para que você possa [monitorar](cache-how-to-monitor.md) a integridade do cache. Você pode exibir as métricas no portal do Azure e também pode [baixá-las e analisá-las](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) usando as ferramentas de sua escolha.
* Confira a [documentação do cliente de cache StackExchange.Redis][StackExchange.Redis cache client documentation].
  * O Cache Redis do Azure pode ser acessado por meio de muitos clientes Redis e linguagens de desenvolvimento. Para obter mais informações, veja [http://redis.io/clients][http://redis.io/clients].
* O Cache Redis do Azure também pode ser usado com ferramentas como Redsmin e Redis Desktop Manager.
  * Para obter mais informações sobre o Redsmin, confira [Como recuperar uma cadeia de conexão do Redis do Azure e usá-la com o Redsmin][How to retrieve an Azure Redis connection string and use it with Redsmin].
  * Acesse e inspecione seus dados no Cache Redis do Azure com uma interface gráfica do usuário usando [RedisDesktopManager](https://github.com/uglide/RedisDesktopManager).
* Veja a documentação do [Redis][redis] e leia sobre [tipos de dados de Redis][redis data types] e [uma introdução de quinze minutos aos tipos de dados de Redis][a fifteen minute introduction to Redis data types].

<!-- INTRA-TOPIC LINKS -->
[Next Steps]: #next-steps
[Introduction to Azure Redis Cache (Video)]: #video
[What is Azure Redis Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Get Started with Azure Redis Cache]: #getting-started-cache-service
[Create the cache]: #create-cache
[Configure the cache]: #enable-caching
[Configure the cache clients]: #NuGet
[Working with Caches]: #working-with-caches
[Connect to the cache]: #connect-to-cache
[Add and retrieve objects from the cache]: #add-object
[Specify the expiration of an object in the cache]: #specify-expiration
[Store ASP.NET session state in the cache]: #store-session


<!-- IMAGES -->


[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png







<!-- LINKS -->
[http://redis.io/clients]: http://redis.io/clients
[Develop in other languages for Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[How to retrieve an Azure Redis connection string and use it with Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis Session State Provider]: http://go.microsoft.com/fwlink/?LinkId=398249
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Configure a cache in Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[StackExchange.Redis configuration model]: https://stackexchange.github.io/StackExchange.Redis/Configuration

[Work with .NET objects in the cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Cache Pricing Details]: http://www.windowsazure.com/pricing/details/cache/
[Azure portal]: https://portal.azure.com/

[Overview of Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Using Resource groups to manage your Azure resources]: ../azure-resource-manager/resource-group-overview.md

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[StackExchange.Redis cache client documentation]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Redis data types]: http://redis.io/topics/data-types
[a fifteen minute introduction to Redis data types]: http://redis.io/topics/data-types-intro

[How Application Strings and Connection Strings Work]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/


