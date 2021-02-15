---
title: Provedor de estado de sessão ASP.NET de cache
description: Saiba como armazenar o estado de sessão ASP.NET na memória usando o cache do Azure para Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 05/01/2017
ms.openlocfilehash: ce77f5074d707da5cfb251a103653b96e4644b5f
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92544521"
---
# <a name="aspnet-session-state-provider-for-azure-cache-for-redis"></a>Provedor de Estado de Sessão ASP.NET para o Cache do Azure para Redis

O Cache do Azure para Redis fornece um provedor de estado de sessão que você pode usar para armazenar o estado de sessão na memória com o Cache do Azure para Redis em vez de um banco de dados do SQL Server. Para usar o provedor de estado de sessão de cache, primeiro configure o cache e, em seguida, configure o aplicativo ASP.NET para o cache usando o pacote NuGet de Estado de Sessão do Cache do Azure para Redis. Para aplicativos ASP.NET Core, leia [Gerenciamento de sessão e estado no ASP.NET Core](/aspnet/core/fundamentals/app-state).

Geralmente, não é prático em uma aplicativo em nuvem real evitar o armazenamento de alguma forma de estado para uma sessão de usuário, mas algumas abordagens afetam o desempenho e a escalabilidade mais do que outras. Se você precisar armazenar o estado, a melhor solução será manter o estado em uma quantidade pequena e armazená-lo em cookies. Se isso não for viável, a próxima solução mais adequada será usar o estado de sessão ASP.NET com um provedor para cache distribuído na memória. A pior solução de um ponto de vista de escalabilidade e desempenho é usar um provedor de estado de sessão com backup em um banco de dados. Este tópico fornece diretrizes sobre como usar o Provedor de Estado de Sessão do ASP.NET para o Cache do Azure para Redis. Para saber mais sobre outras opções de estado de sessão, consulte [Opções de estado da sessão ASP.NET](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Armazenar o estado da sessão ASP.NET no cache

Para configurar um aplicativo cliente no Visual Studio usando o pacote NuGet de Estado de Sessão do Cache do Azure para Redis, clique em **Gerenciador de Pacotes NuGet** e **Console do Gerenciador de Pacotes** no menu **Ferramentas** .

Execute o comando a seguir na janela `Package Manager Console`.
    

```powershell
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Se você estiver usando o recurso de cluster a partir da camada premium, use [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 ou superior, caso contrário uma exceção será emitida. Mudar para 2.0.1 ou superior é uma alteração significativa; para saber mais, confira [v2.0.0 Breaking Change Details](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details). No momento da atualização deste artigo, a versão atual deste pacote é 2.2.3.
> 
> 

O pacote NuGet do Provedor de estado de sessão Redis tem uma dependência do pacote StackExchange.Redis.StrongName. Se o pacote StackExchange.Redis.StrongName não estiver presente em seu projeto, ele será instalado.

>[!NOTE]
>Além do pacote StackExchange.Redis.StrongName com nome forte, também há a versão do StackExchange.Redis sem nome forte. Se o seu projeto estiver usando a versão StackExchange.Redis sem nome forte, será necessário desinstalá-la, caso contrário, você terá conflitos de nomenclatura em seu projeto. Para saber mais sobre esses pacotes, consulte [Configurar clientes de cache .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

O pacote do NuGet baixa e adiciona as referências de assembly necessárias e adiciona a seção a seguir ao seu arquivo web.config. Esta seção contém a configuração necessária para o aplicativo ASP.NET usar o Provedor de Estado de Sessão do Cache do Azure para Redis.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
  <providers>
    <!-- Either use 'connectionString' OR 'settingsClassName' and 'settingsMethodName' OR use 'host','port','accessKey','ssl','connectionTimeoutInMilliseconds' and 'operationTimeoutInMilliseconds'. -->
    <!-- 'throwOnError','retryTimeoutInMilliseconds','databaseId' and 'applicationName' can be used with both options. -->
    <!--
      <add name="MySessionStateStore" 
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        throwOnError = "true" [true|false]
        retryTimeoutInMilliseconds = "5000" [number]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "1000" [number]
        connectionString = "<Valid StackExchange.Redis connection string>" [String]
        settingsClassName = "<Assembly qualified class name that contains settings method specified below. Which basically return 'connectionString' value>" [String]
        settingsMethodName = "<Settings method should be defined in settingsClass. It should be public, static, does not take any parameters and should have a return type of 'String', which is basically 'connectionString' value.>" [String]
        loggingClassName = "<Assembly qualified class name that contains logging method specified below>" [String]
        loggingMethodName = "<Logging method should be defined in loggingClass. It should be public, static, does not take any parameters and should have a return type of System.IO.TextWriter.>" [String]
        redisSerializerType = "<Assembly qualified class name that implements Microsoft.Web.Redis.ISerializer>" [String]
      />
    -->
    <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider"
         host=""
         accessKey=""
         ssl="true" />
  </providers>
</sessionState>
```

A seção comentada fornece um exemplo dos atributos e as configurações de exemplos de cada atributo.

Configure os atributos usando os valores da sua folha de cache no Portal do Microsoft Azure e defina os demais valores conforme sua preferência. Para obter instruções sobre como acessar as propriedades do cache, consulte [Definir configurações do Cache Redis do Azure](cache-configure.md#configure-azure-cache-for-redis-settings).

* **host** – especifique o ponto de extremidade do seu cache.
* **porta** – Use sua porta não TLS/SSL ou sua porta TLS/SSL, dependendo das configurações de TLS.
* **accessKey** – use a chave primária ou secundária do seu cache.
* **SSL** – verdadeiro se você quiser proteger as comunicações de cache/cliente com TLS; caso contrário, false. Especifique a porta correta.
  * A porta não TLS é desabilitada por padrão para novos caches. Especifique true para que essa configuração Use a porta TLS. Para obter mais informações sobre como habilitar a porta não TLS, consulte a seção [portas de acesso](cache-configure.md#access-ports) no tópico [configurar um cache](cache-configure.md) .
* **throwOnError** – true se você quiser que uma exceção seja lançada em caso de falha, ou false se quiser que a operação falhe silenciosamente. Você pode verificar a existência de uma falha na propriedade estática Microsoft.Web.Redis.RedisSessionStateProvider.LastException. O padrão é true.
* **retryTimeoutInMilliseconds** – as operações que apresentam falhas recebem uma nova tentativa durante esse intervalo, especificado em milissegundos. A primeira nova tentativa ocorre após 20 milissegundos e outras novas tentativas ocorrem a cada segundo até que o intervalo de retryTimeoutInMilliseconds expire. Imediatamente após esse intervalo, a operação será repetida uma última vez. Se a operação ainda falhar, a exceção será lançada de volta ao chamador, dependendo da configuração de throwOnError. O valor padrão é 0, que significa nenhuma tentativa nova.
* **databaseId** – especifica qual banco de dados usar para os dados de saída do cache. Se esse campo não for especificado, o valor padrão 0 será usado.
* **applicationName** – as chaves são armazenadas no Redis como `{<Application Name>_<Session ID>}_Data`. Esse esquema de nomenclatura permite que vários aplicativos compartilhem a mesma instância do Redis. Esse parâmetro é opcional e, se você não configurá-lo, um valor padrão é usado.
* **connectionTimeoutInMilliseconds** – essa configuração permite a substituição da configuração syncTimeout no cliente StackExchange.Redis. Se ela não for especificada, a configuração padrão do syncTimeout, 1000, será usada. Para obter mais informações, consulte [Modelo de configuração StackExchange.Redis](https://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – essa configuração permite a substituição da configuração de syncTimeout no cliente StackExchange.Redis. Se ela não for especificada, a configuração padrão de syncTimeout, 1000, será usada. Para obter mais informações, consulte [Modelo de configuração StackExchange.Redis](https://go.microsoft.com/fwlink/?LinkId=398705).
* **redisSerializerType** – essa configuração permite que você especifique a serialização personalizada de conteúdo da sessão que é enviado ao Redis. O tipo especificado deve implementar `Microsoft.Web.Redis.ISerializer` e deve declarar um construtor público sem parâmetros. Por padrão, `System.Runtime.Serialization.Formatters.Binary.BinaryFormatter` é usado.

Para saber mais sobre essas propriedades, consulte o anúncio original da postagem do blog em [Anunciando o Provedor de estado de sessão ASP.NET para Redis](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/).

Não se esqueça de comentar a seção do provedor de estado de sessão InProc padrão em seu Web.config.

```xml
<!-- <sessionState mode="InProc"
     customProvider="DefaultSessionProvider">
     <providers>
        <add name="DefaultSessionProvider"
              type="System.Web.Providers.DefaultSessionStateProvider,
                    System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                    PublicKeyToken=31bf3856ad364e35"
              connectionStringName="DefaultConnection" />
      </providers>
</sessionState> -->
```

Após a execução dessas etapas, o aplicativo será configurado para usar o Provedor de Estado de Sessão do Cache do Azure para Redis. Quando você usa o estado de sessão no aplicativo, ele é armazenado em uma instância do Cache do Azure para Redis.

> [!IMPORTANT]
> Os dados armazenados em cache devem ser serializáveis, ao contrário dos dados que podem ser armazenados no Provedor de estado de sessão padrão do ASP.NET na memória. Quando o Provedor de estado de sessão para Redis for usado, certifique-se de que os tipos de dados que estão sendo armazenados no estado de sessão sejam serializáveis.
> 
> 

## <a name="aspnet-session-state-options"></a>Opções de estado da sessão ASP.NET

* Provedor de estado de sessão na memória - Esse provedor armazena o Estado da sessão na memória. A vantagem de usar esse provedor é que ele é simples e rápido. No entanto, não é possível dimensionar seus Aplicativos Web se você estiver usando o provedor na memória, pois ele não é distribuído.
* Provedor de estado de sessão do SQL Server - Esse provedor armazena o Estado da sessão no SQL Server. Use esse provedor se quiser armazenar o estado da Sessão em um armazenamento persistente. Você pode dimensionar seu Aplicativo Web, mas o uso do Sql Server para sessão afeta o desempenho de seu Aplicativo Web. Você também pode usar este provedor com uma [Configuração de OLTP na memória](/archive/blogs/sqlserverstorageengine/asp-net-session-state-with-sql-server-in-memory-oltp) para ajudar a melhorar o desempenho.
* Provedor de Estado de Sessão Distribuído na Memória, como o Provedor de Estado de Sessão do Cache do Azure para Redis - Esse provedor oferece o melhor dos dois mundos. Seu Aplicativo Web pode ter um Provedor de estado de sessão simples, rápido e escalonável. Como esse provedor armazena o Estado da sessão em um Cache, seu aplicativo precisa levar em consideração todas as características associadas ao conversar com um Cache distribuído na memória, por exemplo, falhas de rede temporárias. Para conhecer as práticas recomendadas sobre o uso do Cache, consulte [Orientação sobre cache](/azure/architecture/best-practices/caching) da Microsoft Patterns & Practices [Orientação sobre design e implementação de aplicativo na nuvem do Azure](https://github.com/mspnp/azure-guidance).

Para saber mais sobre o estado da sessão e outras práticas recomendadas, consulte [Práticas recomendadas para o desenvolvimento na Web (Criando aplicativos de nuvem reais com o Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="third-party-session-state-providers"></a>Provedores de estado de sessão de terceiros

* [NCache](https://www.alachisoft.com/ncache/session-index.html)
* [Apache Ignite](https://apacheignite-net.readme.io/docs/aspnet-session-state-caching)

## <a name="next-steps"></a>Próximas etapas

Confira o [Provedor de Cache de Saída do ASP.NET para o Cache do Azure para Redis](cache-aspnet-output-cache-provider.md).