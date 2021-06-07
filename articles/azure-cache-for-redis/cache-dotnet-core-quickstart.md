---
title: 'Início Rápido: Usar o Cache do Azure para Redis no .NET Core'
description: Neste início rápido, saiba como acessar o Cache do Azure para Redis em seus aplicativos .NET Core
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: dotnet
ms.custom: devx-track-csharp, mvc
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: 198580ad41b62cf044ade7b525728c38aabd0f9e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720991"
---
# <a name="quickstart-use-azure-cache-for-redis-in-net-core"></a>Início Rápido: Usar o Cache do Azure para Redis no .NET Core

Neste guia de início rápido, você incorporará o Cache do Azure para Redis a um aplicativo .NET Core para ter acesso a um cache seguro e dedicado que pode ser acessado de qualquer aplicativo no Azure. Você especificamente usará o cliente [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) com o código C# em um aplicativo de console do .NET Core.

## <a name="skip-to-the-code-on-github"></a>Pular para o código no GitHub

Se você quiser pular diretamente para o código, confira o [Guia de início rápido do .NET Core](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/dotnet-core) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- [SDK do .NET Core](https://dotnet.microsoft.com/download)

## <a name="create-a-cache"></a>Criar um cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Anote o **NOME DE HOST** e a chave de acesso **primária**. Você usará esses valores posteriormente para construir o segredo *CacheConnection*.



## <a name="create-a-console-app"></a>Criar um aplicativo do console

Abra uma nova janela de comando e execute o seguinte comando para criar um novo aplicativo de console .NET Core:

```
dotnet new console -o Redistest
```

Na janela de comando, altere para o novo diretório do projeto *Redistest*.



## <a name="add-secret-manager-to-the-project"></a>Adicionar o Gerenciador de Segredos ao projeto

Nesta seção, você adicionará a [ferramenta Gerenciador de Segredos](/aspnet/core/security/app-secrets) ao seu projeto. A ferramenta Gerenciador de Segredos armazena dados confidenciais para o trabalho de desenvolvimento fora da árvore do seu projeto. Essa abordagem ajuda a impedir o compartilhamento acidental de segredos do aplicativo no código-fonte.

Abra o arquivo *Redistest.csproj*. Adicione um elemento `DotNetCliToolReference` para incluir *Microsoft.Extensions.SecretManager.Tools*. Adicione também um elemento `UserSecretsId`, conforme mostrado abaixo e salve o arquivo.

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>net5.0</TargetFramework>
        <UserSecretsId>Redistest</UserSecretsId>
    </PropertyGroup>
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
    </ItemGroup>
</Project>
```

Execute o seguinte comando para adicionar o pacote *Microsoft.Extensions.Configuration.UserSecrets* ao projeto:

```
dotnet add package Microsoft.Extensions.Configuration.UserSecrets
```

Execute o seguinte comando para restaurar seus pacotes:

```
dotnet restore
```

Na janela de comando, execute o seguinte comando para armazenar um novo segredo denominado *CacheConnection*, depois de substituir os espaços reservados (incluindo colchetes) para o nome do cache e a chave de acesso primária:

```
dotnet user-secrets set CacheConnection "<cache name>.redis.cache.windows.net,abortConnect=false,ssl=true,allowAdmin=true,password=<primary-access-key>"
```

Adicione a seguinte instrução `using` a *Program.cs*:

```csharp
using Microsoft.Extensions.Configuration;
```

Adicione os seguintes membros à classe `Program` em *Program.cs*. Esse código inicializa uma configuração para acessar o segredo do usuário para a cadeia de conexão de Cache do Azure para Redis.

```csharp
private static IConfigurationRoot Configuration { get; set; }
const string SecretName = "CacheConnection";

private static void InitializeConfiguration()
{
    var builder = new ConfigurationBuilder()
        .AddUserSecrets<Program>();

    Configuration = builder.Build();
}
```

## <a name="configure-the-cache-client"></a>Configurar o cliente de cache

Nesta seção, você configurará o aplicativo de console para usar o cliente [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) para .NET.

Na janela de comando, execute o seguinte comando no diretório do projeto *Redistest*:

```
dotnet add package StackExchange.Redis
```

Depois que a instalação for concluída, o cliente de cache *StackExchange.Redis* está disponível para uso com o seu projeto.


## <a name="connect-to-the-cache"></a>Conectar-se ao cache

Adicione a seguinte instrução `using` a *Program.cs*:

```csharp
using StackExchange.Redis;
```

A conexão com o Cache do Azure para Redis é gerenciada pela classe `ConnectionMultiplexer`. Essa classe deve ser compartilhada e reutilizada em todo o seu aplicativo cliente. Não crie uma nova conexão para cada operação. 

Em *Program.cs*, adicione os seguintes membros à classe `Program` do seu aplicativo de console:

```csharp
private static Lazy<ConnectionMultiplexer> lazyConnection = CreateConnection();

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}

private static Lazy<ConnectionMultiplexer> CreateConnection()
{
    return new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = Configuration[SecretName];
        return ConnectionMultiplexer.Connect(cacheConnection);
    });
}
```

Uma abordagem para compartilhar uma instância do `ConnectionMultiplexer` em seu aplicativo usa uma propriedade estática que retorna uma instância conectada. O código oferece uma maneira segura ao thread de inicializar somente uma única instância conectada do `ConnectionMultiplexer`. `abortConnect` é definido como false, o que significa que a chamada é bem-sucedida mesmo que não seja possível estabelecer uma conexão com o Cache do Azure para Redis. Um recurso chave do `ConnectionMultiplexer` é que ele restaura automaticamente a conectividade com o cache assim que o problema de rede ou outras causas sejam resolvidos.

O valor do segredo *CacheConnection* é acessado usando o provedor de configuração do Gerenciador de Segredos e usado como o parâmetro de senha.

## <a name="handle-redisconnectionexception-and-socketexception-by-reconnecting"></a>Processar RedisConnectionException e SocketException com uma nova conexão

Uma melhor prática recomendada ao chamar métodos em `ConnectionMultiplexer` é tentar resolver as exceções `RedisConnectionException` e `SocketException` automaticamente fechando e reestabelecendo a conexão.

Adicione as instruções `using` a seguir ao *Program.cs*:

```csharp
using System.Net.Sockets;
using System.Threading;
```

Em *Program.cs*, adicione os seguintes membros à classe `Program`:

```csharp
private static long lastReconnectTicks = DateTimeOffset.MinValue.UtcTicks;
private static DateTimeOffset firstErrorTime = DateTimeOffset.MinValue;
private static DateTimeOffset previousErrorTime = DateTimeOffset.MinValue;

private static readonly object reconnectLock = new object();

// In general, let StackExchange.Redis handle most reconnects,
// so limit the frequency of how often ForceReconnect() will
// actually reconnect.
public static TimeSpan ReconnectMinFrequency => TimeSpan.FromSeconds(60);

// If errors continue for longer than the below threshold, then the
// multiplexer seems to not be reconnecting, so ForceReconnect() will
// re-create the multiplexer.
public static TimeSpan ReconnectErrorThreshold => TimeSpan.FromSeconds(30);

public static int RetryMaxAttempts => 5;

private static void CloseConnection(Lazy<ConnectionMultiplexer> oldConnection)
{
    if (oldConnection == null)
        return;

    try
    {
        oldConnection.Value.Close();
    }
    catch (Exception)
    {
        // Example error condition: if accessing oldConnection.Value causes a connection attempt and that fails.
    }
}

/// <summary>
/// Force a new ConnectionMultiplexer to be created.
/// NOTES:
///     1. Users of the ConnectionMultiplexer MUST handle ObjectDisposedExceptions, which can now happen as a result of calling ForceReconnect().
///     2. Don't call ForceReconnect for Timeouts, just for RedisConnectionExceptions or SocketExceptions.
///     3. Call this method every time you see a connection exception. The code will:
///         a. wait to reconnect for at least the "ReconnectErrorThreshold" time of repeated errors before actually reconnecting
///         b. not reconnect more frequently than configured in "ReconnectMinFrequency"
/// </summary>
public static void ForceReconnect()
{
    var utcNow = DateTimeOffset.UtcNow;
    long previousTicks = Interlocked.Read(ref lastReconnectTicks);
    var previousReconnectTime = new DateTimeOffset(previousTicks, TimeSpan.Zero);
    TimeSpan elapsedSinceLastReconnect = utcNow - previousReconnectTime;

    // If multiple threads call ForceReconnect at the same time, we only want to honor one of them.
    if (elapsedSinceLastReconnect < ReconnectMinFrequency)
        return;

    lock (reconnectLock)
    {
        utcNow = DateTimeOffset.UtcNow;
        elapsedSinceLastReconnect = utcNow - previousReconnectTime;

        if (firstErrorTime == DateTimeOffset.MinValue)
        {
            // We haven't seen an error since last reconnect, so set initial values.
            firstErrorTime = utcNow;
            previousErrorTime = utcNow;
            return;
        }

        if (elapsedSinceLastReconnect < ReconnectMinFrequency)
            return; // Some other thread made it through the check and the lock, so nothing to do.

        TimeSpan elapsedSinceFirstError = utcNow - firstErrorTime;
        TimeSpan elapsedSinceMostRecentError = utcNow - previousErrorTime;

        bool shouldReconnect =
            elapsedSinceFirstError >= ReconnectErrorThreshold // Make sure we gave the multiplexer enough time to reconnect on its own if it could.
            && elapsedSinceMostRecentError <= ReconnectErrorThreshold; // Make sure we aren't working on stale data (e.g. if there was a gap in errors, don't reconnect yet).

        // Update the previousErrorTime timestamp to be now (e.g. this reconnect request).
        previousErrorTime = utcNow;

        if (!shouldReconnect)
            return;

        firstErrorTime = DateTimeOffset.MinValue;
        previousErrorTime = DateTimeOffset.MinValue;

        Lazy<ConnectionMultiplexer> oldConnection = lazyConnection;
        CloseConnection(oldConnection);
        lazyConnection = CreateConnection();
        Interlocked.Exchange(ref lastReconnectTicks, utcNow.UtcTicks);
    }
}

// In real applications, consider using a framework such as
// Polly to make it easier to customize the retry approach.
private static T BasicRetry<T>(Func<T> func)
{
    int reconnectRetry = 0;
    int disposedRetry = 0;

    while (true)
    {
        try
        {
            return func();
        }
        catch (Exception ex) when (ex is RedisConnectionException || ex is SocketException)
        {
            reconnectRetry++;
            if (reconnectRetry > RetryMaxAttempts)
                throw;
            ForceReconnect();
        }
        catch (ObjectDisposedException)
        {
            disposedRetry++;
            if (disposedRetry > RetryMaxAttempts)
                throw;
        }
    }
}

public static IDatabase GetDatabase()
{
    return BasicRetry(() => Connection.GetDatabase());
}

public static System.Net.EndPoint[] GetEndPoints()
{
    return BasicRetry(() => Connection.GetEndPoints());
}

public static IServer GetServer(string host, int port)
{
    return BasicRetry(() => Connection.GetServer(host, port));
}
```

## <a name="executing-cache-commands"></a>Executar comandos de cache

No *Program.cs*, adicione o seguinte código para o procedimento `Main` com a classe `Program` para seu aplicativo de console:

```csharp
static void Main(string[] args)
{
    InitializeConfiguration();

    IDatabase cache = GetDatabase();

    // Perform cache operations using the cache object...

    // Simple PING command
    string cacheCommand = "PING";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    Console.WriteLine("Cache response : " + cache.Execute(cacheCommand).ToString());

    // Simple get and put of integral data types into the cache
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    cacheCommand = "SET Message \"Hello! The cache is working from a .NET Core console app!\"";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringSet()");
    Console.WriteLine("Cache response : " + cache.StringSet("Message", "Hello! The cache is working from a .NET Core console app!").ToString());

    // Demonstrate "SET Message" executed as expected...
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    // Get the client list, useful to see if connection list is growing...
    // Note that this requires allowAdmin=true in the connection string
    cacheCommand = "CLIENT LIST";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    var endpoint = (System.Net.DnsEndPoint)GetEndPoints()[0];
    IServer server = GetServer(endpoint.Host, endpoint.Port);
    ClientInfo[] clients = server.ClientList();

    Console.WriteLine("Cache response :");
    foreach (ClientInfo client in clients)
    {
        Console.WriteLine(client.Raw);
    }

    CloseConnection(lazyConnection);
}
```

Salve o *Program.cs*.

O Cache Redis do Azure tem um número configurável de bancos de dados (padrão de 16) que podem ser usados para separar logicamente os dados em um Cache Redis do Azure. O código se conecta ao banco de dados padrão, DB 0. Para saber mais, veja [O que são os bancos de dados do Redis?](cache-development-faq.md#what-are-redis-databases) e [Configuração padrão do servidor Redis](cache-configure.md#default-redis-server-configuration).

Os itens de cache podem ser armazenados e recuperados usando os métodos `StringSet` e `StringGet`.

O Redis armazena mais dados como cadeias de caracteres Redis, mas essas cadeias de caracteres podem conter muitos tipos de dados, incluindo dados binários serializados, que podem ser usados ao armazenar objetos .NET no cache.

Execute o seguinte comando na janela de comando para compilar o aplicativo:

```
dotnet build
```

Em seguida, execute o aplicativo com o seguinte comando:

```
dotnet run
```

No exemplo abaixo, você pode ver que a chave `Message` já tinha um valor armazenado em cache, que foi definido por meio do Console do Redis no portal do Azure. O aplicativo atualizou esse valor armazenado em cache. O aplicativo também executou os comandos `PING` e `CLIENT LIST`.

![Aplicativo de console parcial](./media/cache-dotnet-core-quickstart/cache-console-app-partial.png)


## <a name="work-with-net-objects-in-the-cache"></a>Trabalhar com objetos .NET no cache

O Cache do Azure para Redis pode armazenar objetos .NET e tipos de dados primitivos em cache, mas antes que um objeto .NET seja armazenado em cache, ele deve ser serializado. Essa serialização de objetos .NET é de responsabilidade do desenvolvedor do aplicativo e proporciona ao desenvolvedor a flexibilidade na escolha do serializador.

Uma maneira simples de serializar objetos é usar os métodos de serialização do `JsonConvert` no [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) e serializar para e do JSON. Nesta seção, você adicionará um objeto .NET ao cache.

Execute o seguinte comando para adicionar o pacote *Newtonsoft.json* ao aplicativo:

```
dotnet add package Newtonsoft.json
```

Adicione a seguinte instrução `using` à parte superior do *Program.cs*:

```csharp
using Newtonsoft.Json;
```

Adicione a seguinte definição de classe `Employee` ao *Program.cs*:

```csharp
class Employee
{
    public string Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }

    public Employee(string employeeId, string name, int age)
    {
        Id = employeeId;
        Name = name;
        Age = age;
    }
}
```

Na parte inferior do procedimento `Main()` no *Program.cs* e antes da chamada para `CloseConnection()`, adicione as seguintes linhas de código para o cache e recupere um objeto serializado do .NET:

```csharp
    // Store .NET object to cache
    Employee e007 = new Employee("007", "Davide Columbo", 100);
    Console.WriteLine("Cache response from storing Employee .NET object : " + 
    cache.StringSet("e007", JsonConvert.SerializeObject(e007)));

    // Retrieve .NET object from cache
    Employee e007FromCache = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e007"));
    Console.WriteLine("Deserialized Employee .NET object :\n");
    Console.WriteLine("\tEmployee.Name : " + e007FromCache.Name);
    Console.WriteLine("\tEmployee.Id   : " + e007FromCache.Id);
    Console.WriteLine("\tEmployee.Age  : " + e007FromCache.Age + "\n");
```

Salve *Program.cs* e recompile o aplicativo com o seguinte comando:

```
dotnet build
```

Execute o aplicativo com o seguinte comando para testar a serialização de objetos do .NET:

```
dotnet run
```

![Aplicativo de console concluído](./media/cache-dotnet-core-quickstart/cache-console-app-complete.png)


## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende continuar para o próximo tutorial, mantenha os recursos criados neste início rápido e reutilize-os.

Caso contrário, se você não for mais usar o aplicativo de exemplo do início rápido, exclua os recursos do Azure criados neste início rápido para evitar encargos. 

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível, e o grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente o grupo de recursos ou os recursos incorretos. Se tiver criado os recursos para hospedar este exemplo dentro de um grupo de recursos existente que contém recursos que você quer manter, exclua cada recurso individualmente de suas respectivas folhas, em vez de excluir o grupo de recursos.
>

Entre no [portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

Na caixa de texto **Filtrar por nome...** , digite o nome do seu grupo de recursos. As instruções deste artigo usaram um grupo de recursos chamado *TestResources*. Em seu grupo de recursos, na lista de resultados, clique em **...** , depois em **Excluir grupo de recursos**.

![Excluir](./media/cache-dotnet-core-quickstart/cache-delete-resource-group.png)

Você receberá uma solicitação para confirmar a exclusão do grupo de recursos. Digite o nome do grupo de recursos para confirmar e clique em **Excluir**.

Após alguns instantes, o grupo de recursos, e todos os recursos contidos nele, serão excluídos.



<a name="next-steps"></a>

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a usar o Cache do Azure para Redis de um aplicativo .NET Core. Continue para o próximo início rápido usar o Cache do Azure para Redis com um aplicativo Web ASP.NET.

> [!div class="nextstepaction"]
> [Crie um aplicativo Web ASP.NET que usa um Cache do Azure para Redis.](./cache-web-app-howto.md)

Deseja otimizar e reduzir seus gastos com a nuvem?

> [!div class="nextstepaction"]
> [Comece a analisar os custos com o Gerenciamento de Custos](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)