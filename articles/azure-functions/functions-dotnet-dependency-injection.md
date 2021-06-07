---
title: Usar injeção de dependência no .NET do Azure Functions
description: Saiba como usar a injeção de dependência para registrar e usar serviços em funções do .NET
author: ggailey777
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 03/24/2021
ms.author: glenga
ms.reviewer: jehollan
ms.openlocfilehash: 32cd2760eadc94466cdf55883611c78ac0cf24e6
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608112"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Usar injeção de dependência no .NET do Azure Functions

O Azure Functions dá suporte ao padrão de design de software injeção de dependência (DI), que é uma técnica para executar a [Inversão de Controle (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.

- A injeção de dependência no Azure Functions é desenvolvida usando como base os recursos de injeção de dependência do .NET Core. Recomendamos conhecer bem a injeção de dependência do [.NET Core](/aspnet/core/fundamentals/dependency-injection). Há diferenças em como substituir dependências e como os valores de configuração são lidos com o Azure Functions no plano de consumo.

- O suporte para injeção de dependência começa com Azure Functions 2.x.

- Padrões de injeção de dependência diferem dependendo se suas funções C# são executadas [em processo](functions-dotnet-class-library.md) ou [fora de processo](dotnet-isolated-process-guide.md).  

> [!IMPORTANT]
> As diretrizes neste artigo se aplicam somente a [funções de biblioteca de classes C#](functions-dotnet-class-library.md), que são executadas em processo com o tempo de execução. Este modelo de injeção de dependência personalizado não se aplica a [funções isoladas do .net](dotnet-isolated-process-guide.md), o que permite executar as funções do .NET 5,0 fora do processo. O modelo de processo isolado do .NET depende de padrões de injeção de dependência ASP.NET Core regulares. Para saber mais, confira [injeção de dependência](dotnet-isolated-process-guide.md#dependency-injection) no guia de processo isolado do .net.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder usar a injeção de dependência, você precisa instalar os seguintes pacotes NuGet:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- Pacote [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) versão 1.0.28 ou posterior

- [Microsoft. Extensions. DependencyInjection](https://www.nuget.org/packages/Microsoft.Extensions.DependencyInjection/) (atualmente, somente a versão 3. x e versões anteriores têm suporte)

## <a name="register-services"></a>Serviços de registro

Para registrar serviços, crie um método para configurar e adicionar componentes a uma instância `IFunctionsHostBuilder`.  O host do Azure Functions cria uma instância `IFunctionsHostBuilder` e a transmite diretamente para o seu método.

Para registrar o método, adicione o atributo de assembly `FunctionsStartup` que especifica o nome de tipo usado durante a inicialização.

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton<IMyService>((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

Este exemplo usa o pacote [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) necessário para registrar um `HttpClient` na inicialização.

### <a name="caveats"></a>Advertências

Uma série de etapas de registro executada antes e depois que o runtime processa a classe de inicialização. Assim, tenha em mente os seguintes itens:

- *A classe de inicialização destina-se apenas à instalação e ao registro.* Evite usar serviços registrados na inicialização durante o processo de inicialização. Por exemplo, não tente registrar uma mensagem em um agente que está sendo registrado durante a inicialização. Nesse ponto do processo de registro, os serviços ainda não estão disponíveis para uso. Depois que o método `Configure` é executado, o runtime do Azure Functions continua registrando dependências adicionais, o que pode afetar o funcionamento dos serviços.

- *O contêiner de injeção de dependência só mantém tipos explicitamente registrados*. Os únicos serviços disponíveis como tipos injetáveis são os que são configurados no método `Configure`. Como resultado, tipos específicos do Functions, como `BindingContext` e `ExecutionContext`, não estão disponíveis durante a instalação ou como tipos injetáveis.

## <a name="use-injected-dependencies"></a>Usar dependências injetadas

A injeção de construtor é usada para disponibilizar suas dependências em uma função. O uso de injeção de construtor requer que você não use classes estáticas para serviços injetados ou para suas classes de função.

O exemplo a seguir demonstra como as dependências `IMyService` e `HttpClient` são injetadas em uma função disparada por HTTP.

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;
using System.Threading.Tasks;

namespace MyNamespace
{
    public class MyHttpTrigger
    {
        private readonly HttpClient _client;
        private readonly IMyService _service;

        public MyHttpTrigger(HttpClient httpClient, IMyService service)
        {
            this._client = httpClient;
            this._service = service;
        }

        [FunctionName("MyHttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            var response = await _client.GetAsync("https://microsoft.com");
            var message = _service.GetMessage();

            return new OkObjectResult("Response from function with injected dependencies.");
        }
    }
}
```

Este exemplo usa o pacote [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) necessário para registrar um `HttpClient` na inicialização.

## <a name="service-lifetimes"></a>Tempos de vida do serviço

Os aplicativos do Azure Functions oferecem os mesmos tempos de vida de serviço que a [injeção de dependência do ASP.NET](/aspnet/core/fundamentals/dependency-injection#service-lifetimes). Para um aplicativo do Functions, tempos de vida de serviço diferentes se comportam da seguinte maneira:

- **Transitório**: os serviços transitórios são criados após cada resolução do serviço.
- **Com escopo**: O tempo de vida do serviço com escopo corresponde a um tempo de vida de execução de função. Os serviços com escopo são criados uma vez por execução de função. Solicitações posteriores para esse serviço durante a execução reutilizam a instância de serviço existente.
- **Singleton**: O tempo de vida do serviço singleton corresponde ao tempo de vida do host e é reutilizado em execuções de função nessa instância. Os serviços de vida útil singleton são recomendados para conexões e clientes, por exemplo `DocumentClient` ou instâncias `HttpClient`.

Veja ou baixe um [exemplo de tempos de vida de serviço diferentes](https://github.com/Azure/azure-functions-dotnet-extensions/tree/main/src/samples/DependencyInjection/Scopes) no GitHub.

## <a name="logging-services"></a>Serviços de registro em log

Se você precisar de seu próprio provedor de log, registre um tipo personalizado como uma instância do [`ILoggerProvider`](/dotnet/api/microsoft.extensions.logging.iloggerfactory) , que está disponível por meio do pacote NuGet [Microsoft. Extensions. Logging. abstrações](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) .

O Application Insights é adicionado automaticamente pelo Azure Functions.

> [!WARNING]
> - Não adicione `AddApplicationInsightsTelemetry()` à coleção de serviços, que registra serviços que entram em conflito com os serviços fornecidos pelo ambiente.
> - Não Registre seu próprio `TelemetryConfiguration` ou `TelemetryClient` se você estiver usando a funcionalidade interna de Application insights. Se você precisar configurar sua própria `TelemetryClient` instância, crie uma por meio do injetado `TelemetryConfiguration` conforme mostrado em [log telemetria personalizada em funções C#](functions-dotnet-class-library.md?tabs=v2%2Ccmd#log-custom-telemetry-in-c-functions).

### <a name="iloggert-and-iloggerfactory"></a>ILogger<T> e ILoggerFactory

O host injeta `ILogger<T>` e `ILoggerFactory` serviços em construtores.  No entanto, por padrão, esses novos filtros de log são filtrados dos logs de função.  Você precisa modificar o `host.json` arquivo para aceitar filtros e categorias adicionais.

O exemplo a seguir demonstra como adicionar um `ILogger<HttpTrigger>` com logs que são expostos ao host.

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly ILogger<HttpTrigger> _log;

        public HttpTrigger(ILogger<HttpTrigger> log)
        {
            _log = log;
        }

        [FunctionName("HttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req)
        {
            _log.LogInformation("C# HTTP trigger function processed a request.");

            // ...
    }
}
```

O arquivo de exemplo a seguir `host.json` adiciona o filtro de log.

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingSettings": {
                "isEnabled": true,
                "excludedTypes": "Request"
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

Para obter mais informações sobre os níveis de log, consulte [configurar níveis de log](configure-monitoring.md#configure-log-levels).

## <a name="function-app-provided-services"></a>Serviços oferecidos pelo aplicativo de funções

O host de função registra vários serviços. Os serviços a seguir podem ser dependências no seu aplicativo com segurança:

|Tipo de Serviço|Tempo de vida|Descrição|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Configuração de runtime|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Responsável por fornecer a ID da instância do host|

Se houver outros serviços em que você queira usar uma dependência, [crie um problema e faça uma proposta no GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Substituição dos serviços de host

No momento, não há serviços suportados de substituição pelo host.  Se houver serviços que você queira substituir, [crie um problema e faça uma proposta no GitHub](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Trabalhando com opções e configurações

Os valores definidos nas [configurações do aplicativo](./functions-how-to-use-azure-function-app-settings.md#settings) estão disponíveis em uma instância `IConfiguration`, que permite que você leia os valores das configurações do aplicativo na classe de inicialização.

Você pode extrair valores da instância `IConfiguration` para um tipo personalizado. Copiar os valores das configurações do aplicativo para um tipo personalizado facilita o teste dos seus serviços, tornando esses valores injetáveis. As configurações lidas na instância de configuração devem ser pares simples de chave/valor.

Considere a seguinte classe que inclui uma propriedade nomeada de acordo com uma configuração de aplicativo:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

E um arquivo `local.settings.json` que possa estruturar a configuração personalizada da seguinte maneira:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

De dentro do método `Startup.Configure`, você pode extrair valores da instância `IConfiguration` para seu tipo personalizado usando o seguinte código:

```csharp
builder.Services.AddOptions<MyOptions>()
    .Configure<IConfiguration>((settings, configuration) =>
    {
        configuration.GetSection("MyOptions").Bind(settings);
    });
```

Chamar `Bind` copia os valores da configuração, que têm nomes de propriedade correspondentes, na instância personalizada. A instância das opções agora está disponível no contêiner IoC para ser injetada em uma função.

O objeto das opções é injetado na função como uma instância da interface `IOptions` genérica. Use a propriedade `Value` para acessar os valores encontrados na sua configuração.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

Consulte o [padrão de opções no ASP.NET Core](/aspnet/core/fundamentals/configuration/options) para mais informações com relação ao trabalho com opções.

## <a name="using-aspnet-core-user-secrets"></a>Usando os segredos do usuário ASP.NET Core

Ao desenvolver localmente, ASP.NET Core fornece uma [ferramenta de Gerenciador de segredo](/aspnet/core/security/app-secrets#secret-manager) que permite armazenar informações secretas fora da raiz do projeto. Isso torna menos provável que os segredos sejam acidentalmente confirmados no controle do código-fonte. Azure Functions Core Tools (versão 3.0.3233 ou posterior) lê automaticamente os segredos criados pelo ASP.NET Core Gerenciador de segredo.

Para configurar um projeto do .NET Azure Functions para usar os segredos do usuário, execute o seguinte comando na raiz do projeto.

```bash
dotnet user-secrets init
```

Em seguida, use o `dotnet user-secrets set` comando para criar ou atualizar segredos.

```bash
dotnet user-secrets set MySecret "my secret value"
```

Para acessar valores de segredos de usuário no código do aplicativo de funções, use `IConfiguration` ou `IOptions` .

## <a name="customizing-configuration-sources"></a>Personalizando fontes de configuração

> [!NOTE]
> A personalização da fonte de configuração está disponível a partir do Azure Functions versões do host 2.0.14192.0 e 3.0.14191.0.

Para especificar fontes de configuração adicionais, substitua o `ConfigureAppConfiguration` método na classe do aplicativo de funções `StartUp` .

O exemplo a seguir adiciona valores de configuração de uma base e de um arquivo de configurações de aplicativo específico ao ambiente opcional.

```csharp
using System.IO;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
        {
            FunctionsHostBuilderContext context = builder.GetContext();

            builder.ConfigurationBuilder
                .AddJsonFile(Path.Combine(context.ApplicationRootPath, "appsettings.json"), optional: true, reloadOnChange: false)
                .AddJsonFile(Path.Combine(context.ApplicationRootPath, $"appsettings.{context.EnvironmentName}.json"), optional: true, reloadOnChange: false)
                .AddEnvironmentVariables();
        }
    }
}
```

Adicione provedores de configuração à `ConfigurationBuilder` propriedade de `IFunctionsConfigurationBuilder` . Para obter mais informações sobre como usar provedores de configuração, consulte [Configuration in ASP.NET Core](/aspnet/core/fundamentals/configuration/#configuration-providers).

Um `FunctionsHostBuilderContext` é obtido do `IFunctionsConfigurationBuilder.GetContext()` . Use este contexto para recuperar o nome do ambiente atual e resolver o local dos arquivos de configuração em sua pasta de aplicativo de funções.

Por padrão, os arquivos de configuração, como *appsettings.jsem* , não são copiados automaticamente para a pasta de saída do aplicativo de funções. Atualize seu arquivo *. csproj* para corresponder ao exemplo a seguir para garantir que os arquivos sejam copiados.

```xml
<None Update="appsettings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>      
</None>
<None Update="appsettings.Development.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
</None>
```

> [!IMPORTANT]
> Para aplicativos de funções em execução nos planos de consumo ou Premium, as modificações nos valores de configuração usados em gatilhos podem causar erros de dimensionamento. Qualquer alteração nessas propriedades pela `FunctionsStartup` classe resulta em um erro de inicialização do aplicativo de funções.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

- [Como monitorar seu aplicativo de funções](functions-monitoring.md)
- [Melhores práticas para funções](functions-best-practices.md)
