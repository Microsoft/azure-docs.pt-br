---
title: Provedor de Cache de Saída ASP.NET para o Cache Redis do Azure
description: Saiba como armazenar em cache a saída de página do ASP.NET usando o cache do Azure para Redis. O Provedor de Cache de Saída Redis é um mecanismo de armazenamento fora do processo para dados do cache de saída.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 04/22/2018
ms.openlocfilehash: 6d711b07a10e04dcdf31259f3e53c9687af28e28
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993396"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>Provedor de Cache de Saída ASP.NET para o Cache Redis do Azure

O Provedor de Cache de Saída Redis é um mecanismo de armazenamento fora do processo para dados do cache de saída. Esses dados são usados especificamente para respostas HTTP completas (cache de saída de página). O provedor se conecta ao novo ponto de extensibilidade do provedor de cache de saída que foi apresentado no ASP.NET 4. Para aplicativos ASP.NET Core, leia [cache de resposta em ASP.NET Core](/aspnet/core/performance/caching/response). 

Para usar o Provedor de Cache de Saída Redis, primeiro configure seu cache e, em seguida, configure seu aplicativo ASP.NET usando o pacote NuGet do Provedor de Cache de Saída do Redis. Este tópico fornece informações sobre como configurar seu aplicativo para usar o Provedor de Cache de Saída Redis. Para saber mais sobre como criar e configurar uma instância do Cache Redis do Azure, consulte [Criar um cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Armazenar a saída da página ASP.NET no cache

Para configurar um aplicativo cliente no Visual Studio usando o pacote NuGet de Estado de Sessão do Cache do Azure para Redis, clique em **Gerenciador de Pacotes NuGet** e **Console do Gerenciador de Pacotes** no menu **Ferramentas**.

Execute o comando a seguir na janela `Package Manager Console`.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Para usar o pacote NuGet do Provedor de Cache de Saída Redis, é necessário ter o pacote StackExchange.Redis.StrongName. Se o pacote StackExchange.Redis.StrongName não estiver presente em seu projeto, ele será instalado. Para obter mais informações sobre o pacote NuGet do Provedor de Cache de Saída Redis, consulte a página do NuGet [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/).

>[!NOTE]
>Além do pacote StackExchange.Redis.StrongName com nome forte, também há a versão do StackExchange.Redis sem nome forte. Se o seu projeto estiver usando a versão StackExchange. Redis que não é de nome forte, você deverá desinstalá-la; caso contrário, você encontrará conflitos de nomenclatura em seu projeto. Para saber mais sobre esses pacotes, consulte [Configurar clientes de cache .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

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
| *hospedeira* | string | localhost | O endereço IP do servidor Redis ou o nome do host |
| *port* | número inteiro positivo | 6379 (não TLS/SSL)<br/>6380 (TLS/SSL) | Porta do servidor Redis |
| *accessKey* | string | "" | Senha do servidor Redis quando a autorização do Redis estiver habilitada. O valor é uma cadeia de caracteres vazia por padrão, o que significa que o provedor de estado de sessão não usará nenhuma senha ao se conectar ao servidor Redis. **Se o servidor do Redis estiver em uma rede acessível publicamente, como o cache Redis do Azure, certifique-se de habilitar a autorização do Redis para melhorar a segurança e fornecer uma senha segura.** |
| *SSL* | booleano | **false** | Se deseja se conectar ao servidor Redis via TLS. Esse valor é **false** por padrão porque Redis não dá suporte a TLS pronto para uso. **Se você estiver usando o cache Redis do Azure que dá suporte ao SSL pronto para uso, certifique-se de definir isso como verdadeiro para melhorar a segurança.**<br/><br/>A porta não TLS é desabilitada por padrão para novos caches. Especifique **true** para que essa configuração Use a porta não TLS. Para obter mais informações sobre como habilitar a porta não TLS, consulte a seção [portas de acesso](cache-configure.md#access-ports) no tópico [configurar um cache](cache-configure.md) . |
| *databaseIdNumber* | número inteiro positivo | 0 | *Esse atributo pode ser especificado somente por meio de web.config ou AppSettings.*<br/><br/>Especifique o banco de dados Redis a ser usado. |
| *connectionTimeoutInMilliseconds* | número inteiro positivo | Fornecido por StackExchange. Redis | Usado para definir *ConnectTimeout* ao criar stackexchange. Redis. ConnectionMultiplexer. |
| *operationTimeoutInMilliseconds* | número inteiro positivo | Fornecido por StackExchange. Redis | Usado para definir *SyncTimeout* ao criar stackexchange. Redis. ConnectionMultiplexer. |
| *ConnectionString* (cadeia de conexão stackexchange. Redis válida) | string | *n/a* | Uma referência de parâmetro para AppSettings ou web.config ou outra cadeia de conexão StackExchange. Redis válida. Esse atributo pode fornecer valores para *host*, *porta*, *accessKey*, *SSL* e outros atributos stackexchange. Redis. Para uma análise mais detalhada de *ConnectionString*, consulte [definindo ConnectionString](#setting-connectionstring) na seção de [observações do atributo](#attribute-notes) . |
| *settingsClassName*<br/>*settingsMethodName* | string<br/>string | *n/a* | *Esses atributos podem ser especificados somente por meio de web.config ou AppSettings.*<br/><br/>Use esses atributos para fornecer uma cadeia de conexão. *settingsClassName* deve ser um nome de classe de assembly qualificado que contém o método especificado por *settingsMethodName*.<br/><br/>O método especificado por *settingsMethodName* deve ser público, estático e nulo (não usar parâmetros), com um tipo de retorno de **cadeia de caracteres**. Esse método retorna a cadeia de conexão real. |
| *loggingClassName*<br/>*loggingMethodName* | string<br/>string | *n/a* | *Esses atributos podem ser especificados somente por meio de web.config ou AppSettings.*<br/><br/>Use esses atributos para depurar seu aplicativo fornecendo logs do cache de estado/saída de sessão juntamente com logs de StackExchange. Redis. *loggingClassName* deve ser um nome de classe de assembly qualificado que contém o método especificado por *loggingMethodName*.<br/><br/>O método especificado por *loggingMethodName* deve ser público, estático e nulo (não usar parâmetros), com um tipo de retorno de **System. IO. TextWriter**. |
| *applicationName* | string | O nome do módulo do processo atual ou "/" | *SessionStateprovider somente*<br/>*Esse atributo pode ser especificado somente por meio de web.config ou AppSettings.*<br/><br/>O prefixo do nome do aplicativo a ser usado no cache Redis. O cliente pode usar o mesmo cache Redis para finalidades diferentes. Para garantir que as chaves de sessão não colidem, ela pode ser prefixada com o nome do aplicativo. |
| *throwOnError* | booleano | true | *SessionStateprovider somente*<br/>*Esse atributo pode ser especificado somente por meio de web.config ou AppSettings.*<br/><br/>Se uma exceção deve ser lançada quando ocorre um erro.<br/><br/>Para obter mais informações sobre *throwOnError*, consulte [observações sobre o *throwOnError*](#notes-on-throwonerror) na seção de [notas de atributo](#attribute-notes) . |>*Microsoft. Web. Redis. RedisSessionStateProvider. LastException*. |
| *retryTimeoutInMilliseconds* | número inteiro positivo | 5.000 | *SessionStateprovider somente*<br/>*Esse atributo pode ser especificado somente por meio de web.config ou AppSettings.*<br/><br/>Quanto tempo deve ser repetido quando uma operação falha. Se esse valor for menor que *operationTimeoutInMilliseconds*, o provedor não tentará novamente.<br/><br/>Para obter mais informações sobre *retryTimeoutInMilliseconds*, consulte [observações sobre o *retryTimeoutInMilliseconds*](#notes-on-retrytimeoutinmilliseconds) na seção de [notas de atributo](#attribute-notes) . |
| *redisSerializerType* | string | *n/a* | Especifica o nome do tipo qualificado do assembly de uma classe que implementa Microsoft. Web. Redis. ISerializer e que contém a lógica personalizada para serializar e desserializar os valores. Para obter mais informações, consulte [about *redisSerializerType*](#about-redisserializertype) na seção de [observações sobre atributos](#attribute-notes) . |

## <a name="attribute-notes"></a>Observações do atributo

### <a name="setting-connectionstring"></a>Definindo *ConnectionString*

O valor de *ConnectionString* é usado como chave para buscar a cadeia de conexão real de appSettings, se essa cadeia de caracteres existir em appSettings. Se não for encontrado dentro de AppSettings, o valor de *ConnectionString* será usado como chave para buscar a cadeia de conexão real da seção web.config **ConnectionString** , se essa seção existir. Se a cadeia de conexão não existir em AppSettings ou na seção web.config **ConnectionString** , o valor literal de *ConnectionString* será usado como a cadeia de conexão ao criar stackexchange. Redis. ConnectionMultiplexer.

Os exemplos a seguir ilustram como *ConnectionString* é usado.

#### <a name="example-1"></a>Exemplo 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

No `web.config` , use a chave acima como valor do parâmetro em vez do valor real.

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

No `web.config` , use a chave acima como valor do parâmetro em vez do valor real.

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

### <a name="notes-on-throwonerror"></a>Observações sobre o *throwOnError*

Atualmente, se ocorrer um erro durante uma operação de sessão, o provedor de estado de sessão gerará uma exceção. Isso desliga o aplicativo.

Esse comportamento foi modificado de uma maneira que dá suporte às expectativas de usuários existentes do provedor de estado de sessão ASP.NET, fornecendo também a capacidade de agir em exceções, se desejado. O comportamento padrão ainda gera uma exceção quando ocorre um erro, consistente com outros provedores de estado de sessão ASP.NET; o código existente deve funcionar da mesma forma que antes.

Se você definir *throwOnError* como **false**, em vez de lançar uma exceção quando ocorrer um erro, ele falhará silenciosamente. Para ver se houve um erro e, em caso afirmativo, descubra qual foi a exceção, verifique a propriedade estática *Microsoft. Web. Redis. RedisSessionStateProvider. LastException*.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Observações sobre o *retryTimeoutInMilliseconds*

Isso fornece uma lógica de repetição para simplificar o caso em que alguma operação de sessão deve tentar novamente em caso de falha devido a problemas de rede, enquanto também permite que você controle o tempo limite de repetição ou cancele inteiramente a tentativa.

Se você definir *retryTimeoutInMilliseconds* como um número, por exemplo, 2000, quando uma operação de sessão falhar, ele tentará novamente por 2000 milissegundos antes de tratá-lo como um erro. Portanto, para que o provedor de estado de sessão aplique essa lógica de repetição, basta configurar o tempo limite. A primeira repetição ocorrerá após 20 milissegundos, o que é suficiente na maioria dos casos em que ocorre uma falha de rede. Depois disso, ele tentará novamente a cada segundo até atingir o tempo limite. Logo após o tempo limite, ele tentará mais uma vez para certificar-se de que ele não cortará o tempo limite por (no máximo) um segundo.

Se você não achar que precisa de nova tentativa (por exemplo, quando estiver executando o servidor Redis no mesmo computador que o seu aplicativo) ou se quiser manipular a lógica de repetição por conta própria, defina *retryTimeoutInMilliseconds* como 0.

### <a name="about-redisserializertype"></a>Sobre o *redisSerializerType*

Por padrão, a serialização para armazenar os valores em Redis é feita em um formato binário fornecido pela classe **BinaryFormatter** . Use *redisSerializerType* para especificar o nome de tipo qualificado do assembly de uma classe que implementa **Microsoft. Web. Redis. ISerializer** e tem a lógica personalizada para serializar e desserializar os valores. Por exemplo, aqui está uma classe de serializador JSON usando JSON.NET:

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

No exemplo anterior, os dados da página em cache permaneceram no cache por 60 segundos, e outra versão da página foi armazenada em cache para cada combinação de parâmetros. Para obter mais informações sobre a diretiva OutputCache, consulte [@OutputCache](/previous-versions/dotnet/netframework-4.0/hdxfb6cy(v=vs.100)) .

Após a conclusão dessas etapas, seu aplicativo será configurado para usar o Provedor de Cache de Saída Redis.

## <a name="third-party-output-cache-providers"></a>Provedores de cache de saída de terceiros

* [NCache](https://www.alachisoft.com/blogs/how-to-use-a-distributed-cache-for-asp-net-output-cache/)
* [Apache Ignite](https://apacheignite-net.readme.io/docs/aspnet-output-caching)


## <a name="next-steps"></a>Próximas etapas

Consulte [Provedor de Estado de Sessão ASP.NET para o Cache Redis do Azure](cache-aspnet-session-state-provider.md).