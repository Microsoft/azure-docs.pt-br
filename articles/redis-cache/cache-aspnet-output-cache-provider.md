---
title: "Provedor de Cache de Saída ASP.NET do Cache"
description: "Saiba como armazenar em cache a saída de página ASP.NET usando o Cache Redis do Azure"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/14/2017
ms.author: sdanie
ms.openlocfilehash: 845f25637a0e48460fc76c1ee36060274b3cec38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="aspnet-output-cache-provider-for-azure-redis-cache"></a>Provedor de Cache de Saída ASP.NET para Cache Redis do Azure
O Provedor de Cache de Saída Redis é um mecanismo de armazenamento fora do processo para dados do cache de saída. Esses dados são usados especificamente para respostas HTTP completas (cache de saída de página). O provedor se conecta ao novo ponto de extensibilidade do provedor de cache de saída que foi apresentado no ASP.NET 4.

Para usar o Provedor de Cache de Saída Redis, primeiro configure seu cache e, em seguida, configure seu aplicativo ASP.NET usando o pacote NuGet do Provedor de Cache de Saída do Redis. Este tópico fornece informações sobre como configurar seu aplicativo para usar o Provedor de Cache de Saída Redis. Para saber mais sobre como criar e configurar uma instância do Cache Redis do Azure, consulte [Criar um cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Armazenar a saída da página ASP.NET no cache
Para configurar um aplicativo cliente no Visual Studio usando o pacote NuGet do Estado de Sessão do Cache Redis, clique em **Gerenciador de Pacotes NuGet** e **Console do Gerenciador de Pacotes** no menu **Ferramentas**.

Execute o comando a seguir na janela `Package Manager Console`.
    
```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Para usar o pacote NuGet do Provedor de Cache de Saída Redis, é necessário ter o pacote StackExchange.Redis.StrongName. Se o pacote StackExchange.Redis.StrongName não estiver presente em seu projeto, ele será instalado. Para obter mais informações sobre o pacote NuGet do Provedor de Cache de Saída Redis, consulte a página do NuGet [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/).

>[!NOTE]
>Além do pacote StackExchange.Redis.StrongName com nome forte, também há a versão do StackExchange.Redis sem nome forte. Se o seu projeto estiver usando a versão StackExchange.Redis sem nome forte, será necessário desinstalá-la, caso contrário, você terá conflitos de nomenclatura em seu projeto. Para saber mais sobre esses pacotes, consulte [Configurar clientes de cache .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

O pacote do NuGet baixa e adiciona as referências de assembly necessárias e adiciona a seção a seguir ao seu arquivo web.config. Essa seção contém a configuração necessária para que seu aplicativo ASP.NET use o Provedor de Cache de Saída Redis.

```xml
<caching>
  <outputCachedefault Provider="MyRedisOutputCache">
    <providers>
      <!--
      <add name="MyRedisOutputCache"
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "5000" [number]
      />
      -->
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider" host="127.0.0.1" accessKey="" ssl="false"/>
    </providers>
  </outputCache>
</caching>
```

A seção comentada fornece um exemplo dos atributos e as configurações de exemplos de cada atributo.

Configure os atributos usando os valores da sua folha de cache no Portal do Microsoft Azure e defina os demais valores conforme sua preferência. Para obter instruções sobre como acessar as propriedades do cache, consulte [Definir configurações de cache Redis](cache-configure.md#configure-redis-cache-settings).

* **host** – especifique o ponto de extremidade do seu cache.
* **porta** – use sua porta SSL ou não SSL, dependendo das configurações de SSL.
* **accessKey** – use a chave primária ou secundária do seu cache.
* **ssl** – true, se você quiser proteger as comunicações de cache/cliente com SSL; caso contrário, false. Não esqueça de especificar a porta correta. Especifique a porta correta.
  * A porta não SSL é desabilitada por padrão para novos caches. Escolha true nessa configuração para usar a porta SSL. Para mais informações sobre como habilitar a porta não SSL, confira a seção [Portas de acesso](cache-configure.md#access-ports) do tópico [Como configurar um cache](cache-configure.md).
* **databaseId** – especifica qual banco de dados deve ser usado para os dados de saída do cache. Se esse campo não for especificado, o valor padrão 0 será usado.
* **applicationName** – as chaves são armazenadas no Redis como `<AppName>_<SessionId>_Data`. Esse esquema de nomenclatura permite que vários aplicativos compartilhem a mesma chave. Esse parâmetro é opcional e, se você não configurá-lo, um valor padrão é usado.
* **connectionTimeoutInMilliseconds** – essa configuração permite a substituição da configuração syncTimeout no cliente StackExchange.Redis. Se ela não for especificada, a configuração padrão do syncTimeout, 1000, será usada. Para obter mais informações, consulte [Modelo de configuração StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – essa configuração permite a substituição da configuração de syncTimeout no cliente StackExchange.Redis. Se ela não for especificada, a configuração padrão de syncTimeout, 1000, será usada. Para obter mais informações, consulte [Modelo de configuração StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Adicione uma diretiva OutputCache a cada página da qual você deseja armazenar a saída em cache.

```
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

No exemplo anterior, os dados da página em cache permaneceram no cache por 60 segundos, e outra versão da página foi armazenada em cache para cada combinação de parâmetros. Para saber mais sobre a diretiva OutputCache, consulte [@OutputCache](http://go.microsoft.com/fwlink/?linkid=320837).

Após a conclusão dessas etapas, seu aplicativo será configurado para usar o Provedor de Cache de Saída Redis.

## <a name="next-steps"></a>Próximas etapas
Confira [Provedor de estado de sessão ASP.NET para Cache Redis do Azure](cache-aspnet-session-state-provider.md)

