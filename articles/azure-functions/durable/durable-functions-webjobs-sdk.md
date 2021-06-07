---
title: Como executar Durable Functions como trabalhos Web-Azure
description: Saiba como codificar e configurar funções durável para executar em trabalhos Web usando o SDK do WebJobs.
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 826e475eb71563b52d687903aeac4ec936e267f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009493"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Como executar Durable Functions como trabalhos Web

Por padrão, Durable Functions usa o tempo de execução Azure Functions para hospedar orquestrações. No entanto, pode haver alguns cenários em que você precisa de mais controle sobre o código que escuta eventos. Este artigo mostra como implementar sua orquestração usando o SDK de trabalhos Web. Para ver uma comparação mais detalhada entre funções e trabalhos Web, consulte [comparar funções e trabalhos](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)Web.

[Azure Functions](../functions-overview.md) e extensão de [Funções Duráveis](durable-functions-overview.md) baseiam-se no [SDK do WebJobs](../../app-service/webjobs-sdk-how-to.md). O host de trabalho no SDK de trabalhos Web é o tempo de execução no Azure Functions. Se você precisar controlar o comportamento de maneiras não possíveis no Azure Functions, você poderá desenvolver e executar Durable Functions usando o SDK de trabalhos Web por conta própria.

Na versão 3. x do SDK de trabalhos Web, o host é uma implementação de `IHost` e, na versão 2. x, você usa o `JobHost` objeto.

O exemplo de encadeamento de Durable Functions está disponível em uma versão do SDK do webjobs 2. x: baixar ou clonar o [repositório de durable Functions](https://github.com/azure/azure-functions-durable-extension/)e fazer check-out da ramificação *v1* e ir para a pasta de *webjobssdk de \\ \\ encadeamento de exemplos* .

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você está familiarizado com os conceitos básicos do SDK do WebJobs, desenvolvimento de bibliotecas de classe C# para funções do Azure Functions e as funções duráveis. Se você precisar obter uma introdução a esses tópicos, consulte os seguintes recursos:

* [Introdução ao WebJobs SDK](../../app-service/webjobs-sdk-get-started.md)
* [Criar sua primeira função usando o Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Durable Functions](durable-functions-sequence.md)

Para completar as etapas neste artigo:

* [Instale o Visual Studio 2019](/visualstudio/install/) com a carga de trabalho de **Desenvolvimento do Azure**.

  Se você já tiver o Visual Studio, mas não tiver essa carga de trabalho, adicione a carga de trabalho selecionando **ferramentas**  >  **obter ferramentas e recursos**.

  (Você pode usar o [código do Visual Studio](https://code.visualstudio.com/) em vez disso, mas algumas das instruções são específicas para o Visual Studio Code.)

* Instale e execute o [Emulador de Armazenamento do Azure](../../storage/common/storage-use-emulator.md) 5.2 ou posterior. Uma alternativa é atualizar o arquivo *App. config* com uma cadeia de caracteres de conexão de armazenamento do Microsoft Azure.

## <a name="webjobs-sdk-versions"></a>Versões do SDK do WebJobs

Este artigo explica como desenvolver um projeto de 2. x do SDK do WebJobs (equivalente a versão do Azure Functions 1. x). Para obter informações sobre versão 3. x, consulte [SDK do WebJobs 3. x](#webjobs-sdk-3x) posteriormente neste artigo.

## <a name="create-a-console-app"></a>Criar um aplicativo de console

Para executar Durable Functions como trabalhos Web, você deve primeiro criar um aplicativo de console. Um projeto do SDK do WebJobs é apenas um projeto de aplicativo de console com os pacotes do NuGet apropriados instalados.

Na caixa de diálogo **novo projeto** do Visual Studio, selecione aplicativo de console **da área de trabalho clássica do Windows**  >  **(.NET Framework)**. No arquivo de projeto, o `TargetFrameworkVersion` deve ser `v4.6.1`.

O Visual Studio também tem um modelo de projeto webjob, que pode ser usado selecionando **Cloud**  >  **Azure WebJob (.NET Framework)**. Este modelo instala muitos pacotes, alguns dos quais você talvez não precise.

## <a name="install-nuget-packages"></a>Instalar os pacotes NuGet

Você precisará dos pacotes do NuGet para o SDK do WebJobs, associações de núcleo, a estrutura do registro em log e a extensão de tarefa durável. Aqui estão os comandos do **console do Gerenciador de pacotes** para esses pacotes, com os números de versão mais recentes estáveis a partir da data em que este artigo foi escrito:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

Você também precisa de provedores de log. Os comandos a seguir instalam o provedor de informações do Aplicativo Azure e o `ConfigurationManager` . O `ConfigurationManager` permite que você obtenha a chave de instrumentação do Application Insights de configurações do aplicativo.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

O comando a seguir instala o provedor de console:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Código de JobHost

Depois de criar o aplicativo de console e instalar os pacotes NuGet necessários, você estará pronto para usar Durable Functions. Você faz isso usando o código JobHost.

Para usar a extensão de Funções Duráveis, chame `UseDurableTask` no `JobHostConfiguration` do objeto em seu `Main` método:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Para obter uma lista de propriedades que podem ser definidas no `DurableTaskExtension` de objeto, consulte [host.json](../functions-host-json.md#durabletask).

O `Main` método também é o local para configurar os provedores de log. O exemplo a seguir configura o console e os provedores de Application Insights.

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>Funções

Durable Functions no contexto de trabalhos Web difere um pouco de Durable Functions no contexto de Azure Functions. É importante estar ciente das diferenças à medida que você escreve seu código.

O SDK do WebJobs não oferece suporte para os seguintes recursos do Azure Functions:

* [Atributo FunctionName](#functionname-attribute)
* [Gatilho HTTP](#http-trigger)
* [API de gerenciamento de funções HTTP duráveis](#http-management-api)

### <a name="functionname-attribute"></a>Atributo FunctionName

Em um projeto do SDK do WebJobs, o nome do método de uma função é o nome da função. O `FunctionName` atributo é usado somente no Azure Functions.

### <a name="http-trigger"></a>Gatilho HTTP

O SDK do WebJobs não tem um gatilho HTTP. O cliente de orquestração do projeto de exemplo usa um gatilho de timer:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>API de Gerenciamento HTTP

Por não haver nenhum gatilho HTTP, o SDK do WebJobs não tem [API de gerenciamento HTTP](durable-functions-http-api.md).

Em um projeto do SDK de trabalhos Web, você pode chamar métodos no objeto de cliente Orchestration, em vez de enviar solicitações HTTP. Os métodos a seguir correspondem a três tarefas que você pode fazer com a API de gerenciamento HTTP:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

A função de cliente de orquestração no projeto de exemplo inicia a função de orquestrador e entra em um loop que chama a `GetStatusAsync` cada 2 segundos:

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>Execute o exemplo

Você tem Durable Functions configurado para ser executado como um WebJob, e agora você tem uma compreensão de como isso será diferente de executar Durable Functions como Azure Functions autônomo. Neste ponto, a exibição do trabalho em um exemplo pode ser útil.

Esta seção fornece uma visão geral de como executar o [projeto de exemplo](https://github.com/Azure/azure-functions-durable-extension/tree/v1/samples/webjobssdk/chaining). Para obter instruções detalhadas que explicam como executar um projeto do SDK do WebJobs localmente e implantá-lo em um Webjob do Azure, consulte [começar com o SDK do WebJobs](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Executar localmente

1. Verifique se o Emulador de armazenamento está em execução (consulte [pré-requisitos](#prerequisites)).

1. Se você quiser ver os logs no Application Insights ao executar o projeto localmente:

    a. Crie um recurso de Application Insights e use o tipo de aplicativo **geral** para ele.

    b. Salvar a chave de instrumentação no arquivo *App.config*.

1. Execute o projeto.

### <a name="run-in-azure"></a>Executar no Azure

1. Criar um aplicativo Web e uma conta de armazenamento.

1. No aplicativo Web, salve a cadeia de conexão de armazenamento em uma configuração de aplicativo chamada `AzureWebJobsStorage` .

1. Crie um recurso de Application Insights e use o tipo de aplicativo **geral** para ele.

1. Salve a chave de instrumentação em uma configuração de aplicativo chamada `APPINSIGHTS_INSTRUMENTATIONKEY` .

1. Implantar como um WebJob.

## <a name="webjobs-sdk-3x"></a>SDK de WebJobs 3.x

Este artigo explica como desenvolver um projeto do SDK de trabalhos Web 2. x. Se você estiver desenvolvendo um projeto do [SDK do webjobs 3. x](../../app-service/webjobs-sdk-get-started.md) , esta seção o ajudará a entender as diferenças.

A principal alteração introduzida é o uso do .NET Core em vez de .NET Framework. Para criar um projeto webjobs SDK 3. x, as instruções são as mesmas, com estas exceções:

1. Criar um aplicativo de console do aplicativo .NET Core. Na caixa de diálogo **novo projeto** do Visual Studio, selecione   >  **aplicativo de console do .NET Core (.NET Core)**. O arquivo de projeto especifica que `TargetFramework` é `netcoreapp2.x`.

1. Escolha a versão de lançamento SDK 3. x dos seguintes pacotes:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Defina a cadeia de conexão de armazenamento e a chave de instrumentação de Application Insights em um *appsettings.jsno* arquivo, usando a estrutura de configuração do .NET Core. Veja um exemplo:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Altere o `Main` código do método para fazer isso. Aqui está um exemplo:

   ```cs
   static void Main(string[] args)
   {
        var hostBuilder = new HostBuilder()
            .ConfigureWebJobs(config =>
            {
                config.AddAzureStorageCoreServices();
                config.AddAzureStorage();
                config.AddTimers();
                config.AddDurableTask(options =>
                {
                    options.HubName = "MyTaskHub";
                    options.AzureStorageConnectionStringName = "AzureWebJobsStorage";
                });
            })
            .ConfigureLogging((context, logging) =>
            {
                logging.AddConsole();
                logging.AddApplicationInsights(config =>
                {
                    config.InstrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                });
            })
            .UseConsoleLifetime();

        var host = hostBuilder.Build();

        using (host)
        {
            host.Run();
        }
   }
   ```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o SDK do WebJobs, consulte [como usar o SDK do WebJobs](../../app-service/webjobs-sdk-how-to.md).
