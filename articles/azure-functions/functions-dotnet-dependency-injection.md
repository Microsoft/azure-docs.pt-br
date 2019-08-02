---
title: Usar injeção de dependência no .NET do Azure Functions
description: Saiba como usar a injeção de dependência para registrar e usar serviços em funções do .NET
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: azure functions, funções, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/28/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: 1dbbb6e7bd88e08520225515c422529dc260e1b2
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377377"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Usar injeção de dependência no .NET do Azure Functions

Azure Functions dá suporte ao padrão de design de software injeção de dependência (DI), que é uma técnica para obter [inversão de controle (IOC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.

Azure Functions baseia-se na parte superior dos recursos de injeção de dependência de ASP.NET Core. É recomendável reconhecer os serviços, os tempos de vida e os padrões de design de [ASP.NET Core injeção de dependência](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) antes de usar os recursos de di em um aplicativo Azure functions.

O suporte para injeção de dependência começa com Azure Functions 2. x.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder usar a injeção de dependência, você deve instalar os seguintes pacotes NuGet:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Pacote Microsoft. net. Sdk. Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) versão 1.0.28 ou posterior

- Opcional: [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) necessário apenas para registrar HttpClient na inicialização

## <a name="register-services"></a>Serviços de registro

Para registrar serviços, você pode criar um método para configurar e adicionar componentes a uma `IFunctionsHostBuilder` instância do.  O host Azure Functions cria uma instância do `IFunctionsHostBuilder` e a transmite diretamente para o seu método.

Para registrar o método, adicione o `FunctionsStartup` atributo de assembly que especifica o nome de tipo usado durante a inicialização. O código também faz referência a um pré-lançamento de [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) no NuGet.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
            builder.Services.AddSingleton((s) => {
                return new CosmosClient(Environment.GetEnvironmentVariable("COSMOSDB_CONNECTIONSTRING"));
            });
            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

## <a name="use-injected-dependencies"></a>Usar dependências injetadas

ASP.NET Core usa injeção de construtor para tornar suas dependências disponíveis para sua função. O exemplo a seguir demonstra como `IMyService` as `HttpClient` dependências e são injetadas em uma função disparada por http.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

O uso da injeção de Construtor significa que você não deve usar funções estáticas se quiser aproveitar a injeção de dependência.

## <a name="service-lifetimes"></a>Tempos de vida do serviço

Azure Functions aplicativos fornecem os mesmos tempos de vida de serviço como [injeção de dependência ASP.net](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes): transitório, com escopo e singleton.

Em um aplicativo do functions, um tempo de vida do serviço com escopo corresponde a um tempo de vida de execução da função. Os serviços com escopo são criados uma vez por execução. Solicitações posteriores para esse serviço durante a execução reutilizam a instância de serviço existente. Um tempo de vida do serviço singleton corresponde ao tempo de vida do host e é reutilizado em execuções de função nessa instância.

Os serviços de vida útil singleton são recomendados para conexões e clientes `SqlConnection`, `CloudBlobClient`por exemplo `HttpClient` , ou instâncias.

Exiba ou baixe uma [amostra de tempos de vida de serviço diferentes](https://aka.ms/functions/di-sample) no github.

## <a name="logging-services"></a>Serviços de log

Se você precisar de seu próprio provedor de log, a maneira recomendada é registrar `ILoggerProvider` uma instância. Application Insights é adicionado por Azure Functions automaticamente.

> [!WARNING]
> Não adicione `AddApplicationInsightsTelemetry()` à coleção de serviços, pois ele registra os serviços que entram em conflito com os serviços fornecidos pelo ambiente.

## <a name="function-app-provided-services"></a>Serviços fornecidos pelo aplicativo de funções

O host de função registra vários serviços. Os seguintes serviços são seguros para serem adotados como uma dependência em seu aplicativo:

|Tipo de Serviço|Tempo de vida|Descrição|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Único|Configuração de tempo de execução|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Único|Responsável por fornecer a ID da instância do host|

Se houver outros serviços nos quais você deseja assumir uma dependência, [crie um problema e proponha-os no GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Substituindo serviços de host

Atualmente, não há suporte para a substituição de serviços fornecidos pelo host.  Se houver serviços que você deseja substituir, [crie um problema e proponha-os no GitHub](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes recursos:

- [Como monitorar seu aplicativo de funções](functions-monitoring.md)
- [Práticas recomendadas para funções](functions-best-practices.md)
