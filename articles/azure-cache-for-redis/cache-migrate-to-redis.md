---
title: Migrar aplicativos do Serviço de Cache Gerenciado para o Redis - Azure | Microsoft Docs
description: Saiba como migrar aplicativos do Serviço de Cache Gerenciado e aplicativos de Cache na Função para o Cache do Azure para Redis
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 041f077b-8c8e-4d7c-a3fc-89d334ed70d6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 05/30/2017
ms.author: yegu
ms.openlocfilehash: 116e54fd39af801cf8941a974da2b72c483097dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60830225"
---
# <a name="migrate-from-managed-cache-service-to-azure-cache-for-redis"></a>Migrar do Serviço de Cache Gerenciado para o Cache do Azure para Redis
A migração de seus aplicativos que usam o Serviço de Cache Gerenciado do Azure para o Cache do Azure para Redis pode ser feita com alterações mínimas no aplicativo, dependendo dos recursos do Serviço de Cache Gerenciado usados pelo seu aplicativo de cache. Apesar das APIs não serem exatamente a mesmo coisa elas são semelhantes e grande parte do seu código existente que usa o Serviço de Cache Gerenciado para acessar um cache pode ser reutilizado com alterações mínimas. Este artigo mostra como fazer as alterações de aplicativo e configuração necessárias para migrar seus aplicativos do Serviço de Cache Gerenciado para usar o Cache do Azure para Redis, além de mostrar como alguns dos recursos do Cache do Azure para Redis podem ser usados para implementar a funcionalidade de um cache do Serviço de Cache Gerenciado.

>[!NOTE]
>Serviço de Cache Gerenciado e na Cache na Função foram [desativados](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) em 30 de novembro de 2016. Se você tiver quaisquer implantações de Cache na Função que quer migrar para o Cache do Azure para Redis, execute as etapas neste artigo.

## <a name="migration-steps"></a>Etapas da migração
As etapas a seguir são necessárias para migrar um aplicativo do Serviço de Cache Gerenciado para usar o Cache do Azure para Redis.

* Mapear recursos do Serviço de Cache Gerenciado para o Cache do Azure para Redis
* Escolher uma oferta de Cache
* Criar um Cache
* Configurar os clientes de Cache
  * Remover a configuração do Serviço de Cache Gerenciado
  * Configurar um cliente de cache usando o Pacote NuGet Stackexchange.Redis
* Migrar o código do Serviço de Cache Gerenciado
  * Conectar-se ao cache usando a classe ConnectionMultiplexer
  * Tipos de dados primitivos de acesso no cache
  * Trabalhar com objetos .NET no cache
* Migrar o Estado de Sessão do ASP.NET e o cache de saída para o Cache do Azure para Redis 

## <a name="map-managed-cache-service-features-to-azure-cache-for-redis"></a>Mapear recursos do Serviço de Cache Gerenciado para o Cache do Azure para Redis
O Serviço de Cache Gerenciado do Azure e o Cache do Azure para Redis são similares, mas implementam alguns dos seus recursos de maneiras diferentes. Esta seção descreve algumas das diferenças e fornece orientação sobre como implementar os recursos do Serviço de Cache Gerenciado no Cache do Azure para Redis.

| Recurso do Serviço de Cache Gerenciado | Suporte do Serviço de Cache Gerenciado | Exemplos do Cache do Azure para Redis |
| --- | --- | --- |
| Caches nomeados |Um cache padrão está configurado e nas ofertas de cache Standard e Premium, até nove caches nomeados adicionais podem ser configuradas se desejado. |O Cache do Azure para Redis tem um número configurável de bancos de dados (padrão de 16) que pode ser usado para implementar uma funcionalidade semelhante aos caches nomeados. Para saber mais, veja [O que são os bancos de dados do Redis?](cache-faq.md#what-are-redis-databases) e [Configuração padrão do servidor Redis](cache-configure.md#default-redis-server-configuration). |
| Alta disponibilidade |Fornece alta disponibilidade para itens no cache nas ofertas de cache Standard e Premium. Se os itens são perdidos devido a uma falha, cópias de backup dos itens no cache ainda ficam disponíveis. Gravações no cache secundário são feitas de forma síncrona. |Alta disponibilidade nas ofertas de cache Standard e Premium, que possuem uma configuração Principal/Réplica de dois nós (cada fragmento em um cache Premium tem um par de principal/réplica). Gravações de réplica são feitas de forma assíncrona. Para obter mais informações, consulte [Preços do Cache do Azure para Redis](https://azure.microsoft.com/pricing/details/cache/). |
| Notificações |Permite que os clientes recebam notificações assíncronas quando várias operações de cache ocorrem em um cache nomeado. |Aplicativos cliente podem usar o Redis pub/sub ou [Notificações de keyspace](cache-configure.md#keyspace-notifications-advanced-settings) para obter uma funcionalidade semelhante para notificações. |
| Cache local |Armazena uma cópia dos objetos armazenados em cache localmente no cliente para acesso extremamente rápido. |Os aplicativos cliente precisariam implementar essa funcionalidade usando um dicionário ou uma estrutura de dados semelhantes. |
| Política de remoção |Nenhuma ou LRU. A política padrão é LRU. |O Cache do Azure para Redis dá suporte para as seguintes políticas de remoção: volatile-lru, allkeys-lru, volatile-random, allkeys-random, volatile-ttl, noeviction. A política padrão é volatile-lru. Para obter mais informações, confira [Configuração padrão do servidor Redis](cache-configure.md#default-redis-server-configuration). |
| Política de expiração |A política de expiração padrão é Absolute e o intervalo de expiração padrão é de dez minutos. As políticas Sliding e Never também estão disponíveis. |Por padrão, itens no cache não expiram, mas uma expiração pode ser configurada por gravação usando sobrecargas de conjunto de cache. |
| Regiões e marcação |As regiões são subgrupos para itens em cache. Regiões também oferecem suporte à anotação dos itens em cache com mais sequências de caracteres descritivas denominadas marcas. As regiões oferecem suporte à capacidade de realizar operações de pesquisa em qualquer item marcado nessa região. Todos os itens dentro de uma região estão localizados dentro de um único nó do cluster de cache. |Um Cache do Azure para Redis consiste em um único nó (a menos que o cluster Redis esteja habilitado) e, sendo assim, o conceito de regiões do Serviço de Cache Gerenciado não se aplica. O Redis oferece suporte à pesquisa e operações com caractere curinga ao recuperar chaves para que marcas descritivas possam ser incorporadas nos nomes de chave e usadas para recuperar os itens posteriormente. Para obter um exemplo de implementação de uma solução de marcação usando Redis, consulte [Implementar marcação de cache com Redis](https://stackify.com/implementing-cache-tagging-redis/). |
| Serialização |O Cache gerenciado suporta o NetDataContractSerializer, BinaryFormatter e o uso de serializadores personalizados. O padrão é o NetDataContractSerializer. |É responsabilidade do aplicativo cliente para serializar objetos .NET antes de colocá-los no cache, com a opção do serializador para o desenvolvedor do aplicativo cliente. Para obter mais informações e um código de exemplo, consulte [Trabalhar com objetos .NET no cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache). |
| Emulador de cache |O Cache Gerenciado fornece um emulador de cache local. |O Cache do Azure para Redis não tem um emulador, mas é possível [executar o build MSOpenTech do redis-server.exe localmente](cache-faq.md#cache-emulator) para proporcionar uma experiência de emulador. |

## <a name="choose-a-cache-offering"></a>Escolher uma oferta de Cache
O Cache do Microsoft Azure para Redis está disponível nas seguintes camadas:

* **Básico** – um único nó. Vários tamanhos acima de 53 GB.
* **Standard** – principal/réplica com dois nós. Vários tamanhos acima de 53 GB. SLA de 99,9%.
* **Premium** – dois nós Primário/Réplica com até 10 fragmentos. Vários tamanhos de 6 GB a 530 GB. Todos os recursos do tipo Standard e outros, incluindo suporte para [cluster Redis](cache-how-to-premium-clustering.md), [persistência Redis](cache-how-to-premium-persistence.md) e [Rede Virtual do Azure](cache-how-to-premium-vnet.md). SLA de 99,9%.

Cada camada é diferente em termos de recursos e preços. Os recursos são abordados posteriormente neste manual e, para obter mais informações sobre preços, consulte [Detalhes de preços do Cache](https://azure.microsoft.com/pricing/details/cache/).

Um ponto de partida para a migração é escolher o tamanho que corresponda ao tamanho do cache do Serviço de Cache Gerenciado anterior e, em seguida, aumentar ou diminuir, dependendo dos requisitos do seu aplicativo. Para obter informações sobre como escolher a oferta certa do Cache do Azure para Redis, consulte [Qual oferta do Cache do Azure para Redis e tamanho devo usar](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Criar um Cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Configurar os clientes de Cache
Depois que o cache é criado e configurado, a próxima etapa é remover a configuração do Serviço de Cache Gerenciado e adicionar a configuração do Cache do Azure para Redis e as referências para que os clientes de cache possam acessar o cache.

* Remover a configuração do Serviço de Cache Gerenciado
* Configurar um cliente de cache usando o Pacote NuGet Stackexchange.Redis

### <a name="remove-the-managed-cache-service-configuration"></a>Remover a configuração do Serviço de Cache Gerenciado
Antes que os aplicativos cliente possam ser configurados para o Cache do Azure para Redis, a configuração do Serviço de Cache Gerenciado existente e as referências de assembly devem ser removidas desinstalando o pacote NuGet do Serviço de Cache Gerenciado.

Para desinstalar o pacote NuGet do Serviço de Cache Gerenciado, clique com o botão direito do mouse no projeto do cliente no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**. Selecione o nó **Pacotes instalados** e digite W**indowsAzure.Caching** na caixa Pesquisar pacotes instalados. Selecione **Windows** **Cache do Azure** (ou **Windows** **Caching do Azure** dependendo da versão do pacote NuGet), clique em **Desinstalar**, e, em seguida, clique em **Fechar**.

![Desinstalar o pacote NuGet do Serviço de Cache Gerenciado do Azure](./media/cache-migrate-to-redis/IC757666.jpg)

Desinstalar o pacote NuGet de Serviço de Cache Gerenciado remove os assemblies do Serviço de Cache Gerenciado e as entradas do Serviço de Cache Gerenciado no app.config ou web.config do aplicativo cliente. Como algumas configurações personalizadas não podem ser removidas quando você desinstala o pacote NuGet, abra web.config ou app.config e certifique-se de que os seguintes elementos são removidos.

Verifique se a entrada `dataCacheClients` é removida do elemento `configSections`. Não remova todo o elemento `configSections`; remova apenas a entrada `dataCacheClients`, se estiver presente.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Verifique se a seção `dataCacheClients` é removida. A seção `dataCacheClients` será semelhante ao exemplo a seguir.

```xml
<dataCacheClients>
  <dataCacheClientname="default">
    <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
    <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
    <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

    <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
    <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
    <!--<securityProperties mode="Message" sslEnabled="true">
      <messageSecurity authorizationInfo="[Authentication Key]" />
    </securityProperties>-->
  </dataCacheClient>
</dataCacheClients>
```

Depois que a configuração do Serviço de Cache Gerenciado é removida, você pode configurar o cliente de cache conforme descrito na seção a seguir.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Configurar um cliente de cache usando o Pacote NuGet Stackexchange.Redis
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Migrar o código do Serviço de Cache Gerenciado
A API para o cliente de Cache para Redis StackExchange.Azure é similar para o Serviço de Cache Gerenciado. Esta seção fornece uma visão geral das diferenças.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Conectar-se ao cache usando a classe ConnectionMultiplexer
No Serviço de Cache Gerenciado, as conexões com o cache eram manipuladas pelas classes `DataCacheFactory` e `DataCache`. No Cache do Azure para Redis, essas conexões são gerenciadas pela classe `ConnectionMultiplexer`.

Adicione o seguinte usando a instrução na parte superior de qualquer arquivo do qual você deseja acessar o cache.

```csharp
using StackExchange.Redis
```

Se esse namespace não resolver, certifique-se de ter adicionado o pacote NuGet StackExchange.Redis conforme descrito em [Início Rápido: Usar o Cache do Azure para Redis com um aplicativo .NET](cache-dotnet-how-to-use-azure-redis-cache.md).

> [!NOTE]
> Observe que o cliente StackExchange.Redis requer o .NET Framework 4 ou superior.
> 
> 

Para conectar-se a uma instância do Cache do Azure para Redis, chame o método estático `ConnectionMultiplexer.Connect` e passe-o no ponto de extremidade e na chave. Uma abordagem para compartilhar uma instância do `ConnectionMultiplexer` em seu aplicativo deve ter uma propriedade estática que retorna uma instância conectada, semelhante ao exemplo a seguir. Essa abordagem oferece uma maneira thread-safe de inicializar uma única instância `ConnectionMultiplexer` conectada. Neste exemplo, `abortConnect` é definido como false, o que significa que a chamada terá êxito mesmo que não seja possível estabelecer uma conexão com o cache. Um recurso chave do `ConnectionMultiplexer` é que ele vai restaurar automaticamente a conectividade ao cache assim que o problema de rede ou outras causas sejam resolvidos.

```csharp
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
```

O ponto de extremidade do cache, as chaves e as portas podem ser obtidos na folha **Cache do Azure para Redis** da sua instância de cache. Para obter mais informações, consulte [Propriedades do Cache do Azure para Redis](cache-configure.md#properties).

Quando a conexão for estabelecida, retorne uma referência para o banco de dados do Cache do Azure para Redis chamando o método `ConnectionMultiplexer.GetDatabase`. O objeto retornado pelo método `GetDatabase` é um objeto leve de passagem e não precisa ser armazenado.

```csharp
IDatabase cache = Connection.GetDatabase();

// Perform cache operations using the cache object...
// Simple put of integral data types into the cache
cache.StringSet("key1", "value");
cache.StringSet("key2", 25);

// Simple get of data types from the cache
string key1 = cache.StringGet("key1");
int key2 = (int)cache.StringGet("key2");
```

O cliente StackExchange.Redis usa os tipos `RedisKey` e `RedisValue` tipos para acessar e armazenar itens no cache. Esses tipos mapeiam para tipos de linguagem mais primitivos, incluindo a cadeia de caracteres e geralmente não são usados diretamente. As Cadeias de caracteres Redis são o tipo mais básico de valor do Redis e podem conter muitos tipos de dados, incluindo fluxos de binários serializados e apesar de você não poderá usar o tipo diretamente, você usará métodos que contêm `String` no nome. Para a maioria dos tipos de dados primitivos você armazena e recupera itens do cache usando os métodos `StringSet` e `StringGet`, a menos que você pretenda armazenar coleções ou outros tipos de dados Redis no cache. 

`StringSet` e `StringGet` são semelhantes aos métodos `Put` e `Get` do Serviço de Cache Gerenciado, com uma grande diferença de que antes de definir e obter um objeto .NET no cache você deve serializá-lo primeiro. 

Ao se chamar `StringGet`, se o objeto existir, ele será retornado e, se não existir, será retornado nulo. Nesse caso você pode recuperar o valor da fonte de dados desejado e armazená-lo no cache para uso subsequente. Esse padrão é conhecido como padrão cache-aside.

Para especificar a expiração de um item no cache, use o parâmetro `TimeSpan` de `StringSet`.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

O Cache do Azure para Redis pode trabalhar com objetos .NET, bem como tipos de dados primitivos, mas antes que um objeto .NET possa ser armazenado em cache, ele deve ser serializado. Essa serialização é de responsabilidade do desenvolvedor do aplicativo e proporciona ao desenvolvedor a flexibilidade na escolha do serializador. Para obter mais informações e um código de exemplo, consulte [Trabalhar com objetos .NET no cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-cache-for-redis"></a>Migrar o Estado de Sessão do ASP.NET e o cache de saída para o Cache do Azure para Redis
O Cache do Azure para Redis tem provedores de Estado de Sessão ASP.NET e cache de Saída de Página. Para migrar seu aplicativo que usa as versões do Serviço de Cache Gerenciado desses provedores, primeiro remova as seções existentes do web.config e configure as versões do Cache do Azure para Redis dos provedores. Para obter instruções sobre como usar os provedores ASP.NET do Cache do Azure para Redis, consulte [Provedor de estado de sessão ASP.NET para Cache do Azure para Redis](cache-aspnet-session-state-provider.md) e [Provedor de Cache de Saída do ASP.NET para Cache do Azure para Redis](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Próximas etapas
Explore a [documentação do Cache do Azure para Redis](https://azure.microsoft.com/documentation/services/cache/) e veja os tutoriais, exemplos, vídeos e muito mais.

