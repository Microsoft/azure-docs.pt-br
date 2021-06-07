---
title: Provedor de Cache de Saída ASP.NET para o Cache Redis do Azure
description: Saiba como armazenar em cache a saída de página ASP.NET usando o Cache do Azure para Redis. O Provedor de Cache de Saída Redis é um mecanismo de armazenamento fora do processo para dados do cache de saída.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 04/22/2018
ms.openlocfilehash: e7ea409163a6ce28f65799163bd3217d47569751
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220582"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>Provedor de Cache de Saída ASP.NET para o Cache Redis do Azure

O Provedor de Cache de Saída Redis é um mecanismo de armazenamento fora do processo para dados do cache de saída. Esses dados são usados especificamente para respostas HTTP completas (cache de saída de página). O provedor se conecta ao novo ponto de extensibilidade do provedor de cache de saída que foi apresentado no ASP.NET 4. Para aplicativos ASP.NET Core, leia [Cache de resposta no ASP.NET Core](/aspnet/core/performance/caching/response). 

Para usar o Provedor de Cache de Saída Redis, primeiro configure seu cache e, em seguida, configure seu aplicativo ASP.NET usando o pacote NuGet do Provedor de Cache de Saída do Redis. Este tópico fornece informações sobre como configurar seu aplicativo para usar o Provedor de Cache de Saída Redis. Para saber mais sobre como criar e configurar uma instância do Cache Redis do Azure, consulte [Criar um cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Armazenar a saída da página ASP.NET no cache

Para configurar um aplicativo cliente no Visual Studio usando o pacote NuGet de Estado de Sessão do Cache do Azure para Redis, clique em **Gerenciador de Pacotes NuGet** e **Console do Gerenciador de Pacotes** no menu **Ferramentas**.

Execute o comando a seguir na janela `Package Manager Console`.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Para usar o pacote NuGet do Provedor de Cache de Saída Redis, é necessário ter o pacote StackExchange.Redis.StrongName. Se o pacote StackExchange.Redis.StrongName não estiver presente em seu projeto, ele será instalado. Para obter mais informações sobre o pacote NuGet do Provedor de Cache de Saída Redis, consulte a página do NuGet [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/).

>[!NOTE]
>Além do pacote StackExchange.Redis.StrongName com nome forte, também há a versão do StackExchange.Redis sem nome forte. Se o seu projeto estiver usando a versão StackExchange.Redis sem nome forte, será necessário desinstalá-la, caso contrário, você terá conflitos de nomenclatura no projeto. Para saber mais sobre esses pacotes, consulte [Configurar clientes de cache .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

O pacote do NuGet baixa e adiciona as referências de assembly necessárias e adiciona a seção a seguir ao seu arquivo web.config. Essa seção contém a configuração necessária para que seu aplicativo ASP.NET use o Provedor de Cache de Saída Redis.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

Configure os atributos usando os valores da sua folha de cache no Portal do Microsoft Azure e defina os demais valores conforme sua preferência. Para obter instruções sobre como acessar as propriedades do cache, consulte [Definir configurações do Cache Redis do Azure](cache-configure.md#configure-azure-cache-for-redis-settings).

| Atributo | Type | Padrão | Descrição |
| --------- | ---- | ------- | ----------- |
| *host* | string | "localhost" | O nome do host ou o endereço IP do servidor Redis |
| *port* | número inteiro positivo | 6379 (não TLS/SSL)<br/>6380 (TLS/SSL) | Porta do servidor Redis |
| *accessKey* | string | "" | Senha do servidor Redis quando a autorização do Redis está habilitada. O valor é uma cadeia de caracteres vazia por padrão, o que significa que o provedor de estado de sessão não usará nenhuma senha ao se conectar ao servidor Redis. **Se o servidor Redis estiver em uma rede publicamente acessível, como o Cache do Azure para Redis, lembre-se de habilitar a autorização do Redis para aprimorar a segurança e fornecer uma senha segura.** |
| *ssl* | booleano | **false** | Indica se a conexão ao servidor Redis será feita via TLS. Esse valor é **false** por padrão porque o Redis não dá suporte ao TLS pronto para uso. **Se você estiver usando o Cache do Azure para Redis que dá suporte ao SSL pronto para uso, lembre-se de definir isso como true para aprimorar a segurança.**<br/><br/>A porta não TLS é desabilitada por padrão para novos caches. Especifique **true** para que essa configuração use a porta não TLS. Para obter mais informações sobre como habilitar a porta não TLS, confira a seção [Portas de acesso](cache-configure.md#access-ports) do tópico [Configurar um cache](cache-configure.md). |
| *databaseIdNumber* | número inteiro positivo | 0 | *Esse atributo só pode ser especificado por meio de web.config ou AppSettings.*<br/><br/>Especifique o banco de dados Redis a ser usado. |
| *connectionTimeoutInMilliseconds* | número inteiro positivo | Fornecido por StackExchange.Redis | Usado para definir *ConnectTimeout* ao criar StackExchange.Redis.ConnectionMultiplexer. |
| *operationTimeoutInMilliseconds* | número inteiro positivo | Fornecido por StackExchange.Redis | Usado para definir *SyncTimeout* ao criar StackExchange.Redis.ConnectionMultiplexer. |
| *connectionString* (cadeia de conexão StackExchange.Redis válida) | string | *n/a* | Uma referência de parâmetro a AppSettings ou web.config ou uma cadeia de conexão StackExchange.Redis válida. Esse atributo pode fornecer valores para *host*, *port*, *accessKey*, *ssl* e outros atributos de StackExchange.Redis. Para obter uma análise mais detalhada de *connectionString*, confira [Como definir connectionString](#setting-connectionstring) na seção [Observações sobre o atributo](#attribute-notes). |
| *settingsClassName*<br/>*settingsMethodName* | string<br/>string | *n/a* | *Esses atributos só podem ser especificados por meio de web.config ou AppSettings.*<br/><br/>Use-os para fornecer uma cadeia de conexão. *settingsClassName* deve ser um nome de classe de assembly qualificado que contenha o método especificado por *settingsMethodName*.<br/><br/>O método especificado por *settingsMethodName* deve ser público, estático e nulo (não usar parâmetros), com o tipo de retorno **string**. Esse método retorna a cadeia de conexão real. |
| *loggingClassName*<br/>*loggingMethodName* | string<br/>string | *n/a* | *Esses atributos só podem ser especificados por meio de web.config ou AppSettings.*<br/><br/>Use esses atributos para depurar seu aplicativo fornecendo logs do Cache de Saída/Estado de Sessão com os logs de StackExchange.Redis. *loggingClassName* deve ser um nome de classe de assembly qualificado que contenha o método especificado por *loggingMethodName*.<br/><br/>O método especificado por *loggingMethodName* deve ser público, estático e nulo (não usar parâmetros), com o tipo de retorno **System.IO.TextWriter**. |
| *applicationName* | string | O nome do módulo do processo atual ou "/" | *Somente SessionStateProvider*<br/>*Esse atributo só pode ser especificado por meio de web.config ou AppSettings.*<br/><br/>O prefixo do nome do aplicativo a ser usado no Cache Redis. O cliente pode usar o mesmo Cache Redis para finalidades diferentes. A fim de garantir que não haja colisão das chaves de sessão, elas podem ser prefixadas com o nome do aplicativo. |
| *throwOnError* | booleano | true | *Somente SessionStateProvider*<br/>*Esse atributo só pode ser especificado por meio de web.config ou AppSettings.*<br/><br/>Indica se uma exceção será gerada em caso de erro.<br/><br/>Para obter mais informações sobre *throwOnError*, confira [Observações sobre *throwOnError*](#notes-on-throwonerror) na seção [Observações sobre o atributo](#attribute-notes). |
| *retryTimeoutInMilliseconds* | número inteiro positivo | 5.000 | *Somente SessionStateProvider*<br/>*Esse atributo só pode ser especificado por meio de web.config ou AppSettings.*<br/><br/>A duração de novas tentativas em caso de falha de uma operação. Se esse valor for menor que *operationTimeoutInMilliseconds*, o provedor não repetirá a operação.<br/><br/>Para obter mais informações sobre *retryTimeoutInMilliseconds*, confira [Observações sobre *retryTimeoutInMilliseconds*](#notes-on-retrytimeoutinmilliseconds) na seção [Observações sobre o atributo](#attribute-notes). |
| *redisSerializerType* | string | *n/a* | Especifica o nome de tipo de assembly qualificado de uma classe que implementa Microsoft.Web.Redis. Ele e ISerializer contêm a lógica personalizada usada para serializar e desserializar os valores. Para obter mais informações, confira [Sobre *redisSerializerType*](#about-redisserializertype) na seção [Observações sobre o atributo](#attribute-notes). |

## <a name="attribute-notes"></a>Observações sobre o atributo

### <a name="setting-connectionstring"></a>Como definir *connectionString*

O valor de *connectionString* é usado como chave para buscar a cadeia de conexão real em AppSettings, caso essa cadeia de caracteres exista nele. Se ela não for encontrada em AppSettings, o valor de *connectionString* será usado como chave para buscar a cadeia de conexão real na seção **ConnectionString** de web.config, caso essa seção exista. Se a cadeia de conexão não existir em AppSettings nem na seção **ConnectionString** de web.config, o valor literal de *connectionString* será usado como a cadeia de conexão ao criar StackExchange.Redis.ConnectionMultiplexer.

Os exemplos a seguir ilustram como *connectionString* é usada.

#### <a name="example-1"></a>Exemplo 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

Em `web.config`, use a chave acima como o valor do parâmetro em vez do valor real.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>Exemplo 2

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

Em `web.config`, use a chave acima como o valor do parâmetro em vez do valor real.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>Exemplo 3

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>Observações sobre *throwOnError*

Atualmente, se ocorre um erro durante uma operação de sessão, o provedor de estado de sessão gera uma exceção. Isso desligará o aplicativo.

Esse comportamento foi modificado de modo a dar suporte às expectativas dos usuários existentes do provedor de estado de sessão ASP.NET, fornecendo também a capacidade de resolver as exceções, se desejado. O comportamento padrão ainda gera uma exceção quando ocorre um erro. De maneira consistente com outros provedores de estado de sessão ASP.NET, o código existente deve funcionar da mesma forma que antes.

Se você definir *throwOnError* como **false**, em vez de gerar uma exceção quando ocorrer um erro, ele falhará silenciosamente. Para ver se houve um erro e, em caso afirmativo, descobrir qual foi a exceção, verifique a propriedade estática *Microsoft.Web.Redis.RedisSessionStateProvider.LastException*.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Observações sobre *retryTimeoutInMilliseconds*

Isso fornece uma lógica de repetição para simplificar as ocasiões em que uma operação de sessão precise ser repetida em caso de falha devido a uma falha de rede, permitindo também que você controle o tempo limite de repetição ou recuse por completo a tentativa.

Se você definir *retryTimeoutInMilliseconds* com um número, por exemplo, 2.000, quando uma operação de sessão falhar, ele repetirá a operação novamente por 2.000 milissegundos antes de tratá-la como um erro. Portanto, para que o provedor de estado de sessão aplique essa lógica de repetição, basta configurar o tempo limite. A primeira repetição ocorrerá após 20 milissegundos, o que é suficiente na maioria dos casos em que ocorre uma falha de rede. Depois disso, ele repetirá a operação a cada segundo até atingir o tempo limite. Logo após o tempo limite, ele repetirá a operação mais uma vez para garantir que não cortará o tempo limite por (no máximo) um segundo.

Se você não achar que precisa de uma nova tentativa (por exemplo, durante a execução do servidor Redis no mesmo computador do aplicativo) ou se desejar processar a lógica de repetição por conta própria, defina *retryTimeoutInMilliseconds* como 0.

### <a name="about-redisserializertype"></a>Sobre *redisSerializerType*

Por padrão, a serialização usada para armazenar os valores no Redis é feita em um formato binário fornecido pela classe **BinaryFormatter**. Use *redisSerializerType* para especificar o nome de tipo de assembly qualificado de uma classe que implemente **Microsoft.Web.Redis.ISerializer** e tenha a lógica personalizada para serializar e desserializar os valores. Por exemplo, esta é uma classe de serializador JSON com o JSON.NET:

```cs
namespace MyCompany.Redis
{
    public class JsonSerializer : ISerializer
    {
        private static JsonSerializerSettings _settings = new JsonSerializerSettings() { TypeNameHandling = TypeNameHandling.All };

        public byte[] Serialize(object data)
        {
            return Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(data, _settings));
        }

        public object Deserialize(byte[] data)
        {
            if (data == null)
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

Supondo que essa classe seja definida em um assembly com o nome **MyCompanyDll**, você pode definir o parâmetro *redisSerializerType* para usá-lo:

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvider"
             name = "MySessionStateStore"
             redisSerializerType = "MyCompany.Redis.JsonSerializer,MyCompanyDll"
             ... />
    </providers>
</sessionState>
```

## <a name="output-cache-directive"></a>Diretiva de cache de saída

Adicione uma diretiva OutputCache a cada página da qual você deseja armazenar a saída em cache.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

No exemplo anterior, os dados da página em cache permaneceram no cache por 60 segundos, e outra versão da página foi armazenada em cache para cada combinação de parâmetros. Para saber mais sobre a diretiva OutputCache, consulte [@OutputCache](/previous-versions/dotnet/netframework-4.0/hdxfb6cy(v=vs.100)).

Após a conclusão dessas etapas, seu aplicativo será configurado para usar o Provedor de Cache de Saída Redis.

## <a name="third-party-output-cache-providers"></a>Provedores de cache de saída de terceiros

* [NCache](https://www.alachisoft.com/blogs/how-to-use-a-distributed-cache-for-asp-net-output-cache/)
* [Apache Ignite](https://apacheignite-net.readme.io/docs/aspnet-output-caching)


## <a name="next-steps"></a>Próximas etapas

Consulte [Provedor de Estado de Sessão ASP.NET para o Cache Redis do Azure](cache-aspnet-session-state-provider.md).
