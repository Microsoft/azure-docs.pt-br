---
title: Desenvolver funções de biblioteca de classes C# usando Azure Functions
description: Entenda como usar o C# para desenvolver e publicar código como bibliotecas de classes que são executadas em processo com o tempo de execução de Azure Functions.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 07/24/2020
ms.openlocfilehash: c7d14599ec1ebbcb94e0c0f3985a3b857f9353dc
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563873"
---
# <a name="develop-c-class-library-functions-using-azure-functions"></a>Desenvolver funções de biblioteca de classes C# usando Azure Functions

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Este artigo é uma introdução ao desenvolvimento do Azure Functions usando o script C# em biblioteca de classes .NET.

>[!IMPORTANT]
>Este artigo dá suporte a funções de biblioteca de classes do .NET que são executadas em processo com o tempo de execução. O Functions também dá suporte ao .NET 5. x, executando suas funções C# fora do processo e isolada do tempo de execução. Para saber mais, consulte [funções de processo isolado do .net](dotnet-isolated-process-guide.md).

Como desenvolvedor de C#, você também pode estar interessado em um dos seguintes artigos:

| Introdução | Conceitos| Aprendizado guiado/amostras |
|--| -- |--| 
| <ul><li>[Usando o Visual Studio](functions-create-your-first-function-visual-studio.md)</li><li>[Usando Visual Studio Code](create-first-function-vs-code-csharp.md)</li><li>[Usando ferramentas de linha de comando](create-first-function-cli-csharp.md)</li></ul> | <ul><li>[Opções de hospedagem](functions-scale.md)</li><li>[&nbsp;Considerações sobre desempenho](functions-best-practices.md)</li><li>[Desenvolvimento do Visual Studio](functions-develop-vs.md)</li><li>[Injeção de dependência](functions-dotnet-dependency-injection.md)</li></ul> | <ul><li>[Criar aplicativos sem servidor](/learn/paths/create-serverless-applications/)</li><li>[Exemplos do C#](/samples/browse/?products=azure-functions&languages=csharp)</li></ul> |

O Azure Functions oferece suporte às linguagens de programação C# e script C#. Se estiver procurando diretrizes sobre [como usar C# no portal do Azure](functions-create-function-app-portal.md), consulte [Referência do desenvolvedor de script C# (.csx)](functions-reference-csharp.md).

## <a name="supported-versions"></a>Versões com suporte

As versões do tempo de execução do Functions funcionam com versões específicas do .NET. Para saber mais sobre as versões do functions, consulte [visão geral das versões do Azure Functions Runtime](functions-versions.md)

A tabela a seguir mostra o nível mais alto de .NET Core ou .NET Framework que pode ser usado com uma versão específica do functions. 

| Versão de tempo de execução do Functions | Versão máxima do .NET |
| ---- | ---- |
| Funções 3. x | .NET Core 3.1<br/>.NET 5,0<sup>1</sup> |
| Funções 2.x | .NET Core 2,2<sup>2</sup> |
| Funções 1.x | .NET Framework 4.7 |

<sup>1</sup> deve executar [fora do processo](dotnet-isolated-process-guide.md).  
<sup>2</sup> para obter detalhes, consulte [Considerações sobre funções v2. x](#functions-v2x-considerations).   

Para obter as notícias mais recentes sobre as versões de Azure Functions, incluindo a remoção de versões secundárias específicas mais antigas, monitore os [comunicados de serviço Azure app](https://github.com/Azure/app-service-announcements/issues).

### <a name="functions-v2x-considerations"></a>Considerações sobre funções v2. x

Os aplicativos de funções direcionados à versão 2. x mais recente ( `~2` ) são atualizados automaticamente para execução no .NET Core 3,1. Devido a alterações significativas entre as versões do .NET Core, nem todos os aplicativos desenvolvidos e compilados no .NET Core 2,2 podem ser atualizados com segurança para o .NET Core 3,1. Você pode recusar essa atualização fixando seu aplicativo de funções ao `~2.0` . O Functions também detecta APIs incompatíveis e pode fixar seu aplicativo no `~2.0` para evitar a execução incorreta no .NET Core 3,1. 

>[!NOTE]
>Se seu aplicativo de funções for fixado `~2.0` e você alterar esse destino de versão para `~2` , seu aplicativo de funções poderá ser interrompido. Se você implantar o usando modelos ARM, verifique a versão em seus modelos. Se isso ocorrer, altere sua versão de volta para destino `~2.0` e corrija os problemas de compatibilidade. 

Os aplicativos de funções direcionados `~2.0` continuam a ser executados no .NET Core 2,2. Esta versão do .NET Core não recebe mais segurança e outras atualizações de manutenção. Para saber mais, confira [esta página de anúncio](https://github.com/Azure/app-service-announcements/issues/266). 

Você deve trabalhar para tornar suas funções compatíveis com o .NET Core 3,1 assim que possível. Depois de resolver esses problemas, altere sua versão de volta `~2` ou atualize para o `~3` . Para saber mais sobre como direcionar versões do tempo de execução do functions, confira [como direcionar Azure Functions versões de tempo de execução](set-runtime-version.md).

Ao ser executado no Linux em um plano Premium ou dedicado (serviço de aplicativo), você fixa sua versão, em vez de direcionar a uma imagem específica, definindo a `linuxFxVersion` configuração do site como `DOCKER|mcr.microsoft.com/azure-functions/dotnet:2.0.14786-appservice` para saber como definir `linuxFxVersion` , consulte [atualizações de versão manual no Linux](set-runtime-version.md#manual-version-updates-on-linux).

## <a name="functions-class-library-project"></a>Projeto de biblioteca de classes de funções

No Visual Studio, o modelo de projeto do **Azure Functions** cria um projeto de biblioteca de classes do C# que contém os seguintes arquivos:

* [host.json](functions-host-json.md) – armazena conjuntos de configurações que afetam todas as funções no projeto quando estão em execução localmente ou no Azure.
* [local.settings.json](functions-run-local.md#local-settings-file) – armazena as configurações de aplicativo e as cadeias de conexão que são usadas quando estão em execução localmente. Este arquivo contém segredos e não é publicado no aplicativo de funções no Azure. Em vez disso, [adicione configurações de aplicativo ao seu aplicativo de funções](functions-develop-vs.md#function-app-settings).

Quando você cria o projeto, uma estrutura de pastas parecida com o exemplo a seguir é gerada no diretório de saída da compilação:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

Esse é o diretório implantado no aplicativo de funções no Azure. As extensões de associação necessárias na [versão 2.x](functions-versions.md) do runtime das Funções são [adicionadas ao projeto como pacotes do NuGet](./functions-bindings-register.md#vs).

> [!IMPORTANT]
> O processo de compilação cria um arquivo *function.json* para cada função. Esse arquivo *function.json* não deve ser editado diretamente. Você não pode alterar a configuração de associação ou desabilitar a função por meio da edição desse arquivo. Para aprender como desabilitar uma função, consulte [Como desabilitar funções](disable-function.md).


## <a name="methods-recognized-as-functions"></a>Métodos reconhecidos como funções

Em uma biblioteca de classe, uma função é um método estático com um `FunctionName` e um atributo de gatilho, conforme mostrado no exemplo a seguir:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

O atributo `FunctionName` marca o método como um ponto de entrada da função. O nome deve ser exclusivo em um projeto, começar com uma letra e conter apenas letras, números, `_` e `-` até 127 caracteres de comprimento. Modelos de projeto geralmente criam um método chamado `Run`, mas o nome do método pode ser qualquer nome de método C# válido.

O atributo de gatilho especifica o tipo de gatilho e associa dados de entrada a um parâmetro de método. A função de exemplo é disparada por uma mensagem de fila, a qual é transmitida para o método no parâmetro `myQueueItem`.

## <a name="method-signature-parameters"></a>Parâmetros de assinatura do método

A assinatura do método pode conter parâmetros diferentes daquela usada com o atributo de gatilho. Aqui estão alguns dos outros parâmetros que você pode incluir:

* [Associações de entrada e saída](functions-triggers-bindings.md) marcadas como tal, decorando-os com atributos.  
* Um `ILogger` ou `TraceWriter` ([versão 1. x somente](functions-versions.md#creating-1x-apps)) parâmetro para [log](#logging).
* Um parâmetro `CancellationToken` para [desligamento normal](#cancellation-tokens).
* Parâmetros de [expressões de associação](./functions-bindings-expressions-patterns.md) para obter metadados de gatilho.

A ordem dos parâmetros na assinatura da função não importa. Por exemplo, você pode inserir os parâmetros de gatilho antes ou depois de outras associações e inserir o parâmetro do agente antes ou depois dos parâmetros de gatilho ou associação.

### <a name="output-bindings"></a>Associações de saída

Uma função pode ter zero ou uma associação de saída definida usando parâmetros de saída. 

O exemplo a seguir modifica o anterior, adicionando uma associação de fila de saída chamada `myQueueItemCopy` . A função grava o conteúdo da mensagem que dispara a função para uma nova mensagem em uma fila diferente.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        ILogger log)
    {
        log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

Os valores atribuídos às associações de saída são gravados quando a função é encerrada. Você pode usar mais de uma associação de saída em uma função simplesmente atribuindo valores a vários parâmetros de saída. 

Os artigos de referência de associação ([Filas de armazenamento](functions-bindings-storage-queue.md), por exemplo) explicam quais tipos de parâmetro você pode usar com os atributos de associação de gatilho, entrada ou saída.

### <a name="binding-expressions-example"></a>Exemplo de expressões de associação

O código a seguir obtém o nome da fila para monitorar a partir de uma configuração de aplicativo, e ele obtém a hora de criação da mensagem da fila no parâmetro `insertionTime`.

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        ILogger log)
    {
        log.LogInformation($"Message content: {myQueueItem}");
        log.LogInformation($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>function.json gerado automaticamente

O processo de compilação cria um arquivo *function.json* em uma pasta de função na pasta de compilação. Conforme observado anteriormente, esse arquivo não deve ser editado diretamente. Você não pode alterar a configuração de associação ou desabilitar a função por meio da edição desse arquivo. 

A finalidade desse arquivo é fornecer informações para o controlador de escala a ser usado para as [decisões de dimensionamento no plano de consumo](event-driven-scaling.md). Por esse motivo, o arquivo tem apenas informações de gatilho, não associações de entrada/saída.

O arquivo *function.json* gerado inclui uma propriedade `configurationSource` que indica o runtime a ser usado em atributos .NET para associações, em vez da configuração do *function.json*. Aqui está um exemplo:

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

## <a name="microsoftnetsdkfunctions"></a>Microsoft.NET.Sdk.Functions

A geração do arquivo *function.json* é realizada pelo pacote NuGet [Microsoft\.NET\.Sdk\.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

O mesmo pacote é usado para a versão 1.x e 2.x do runtime do Functions. A estrutura de destino é o que diferencia um projeto de 1.x de um projeto de 2.x. Aqui estão as partes relevantes de arquivos *. csproj* , mostrando estruturas de destino diferentes com o mesmo `Sdk` pacote:

# <a name="v2x"></a>[v2. x +](#tab/v2)

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

# <a name="v1x"></a>[v1. x](#tab/v1)

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```
---


Entre as dependências do pacote `Sdk` estão os gatilhos e associações. Um projeto 1. x refere-se a gatilhos e associações de 1. x porque esses gatilhos e associações se destinam ao .NET Framework, enquanto os gatilhos de 2. x e associações são direcionadas ao .NET Core.

O pacote `Sdk` também depende do [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) e, indiretamente, do [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage). Essas dependências garantem que seu projeto use as versões desses pacotes que funcionam com a versão de runtime do Functions para a qual o projeto é direcionado. Por exemplo, o `Newtonsoft.Json` tem a versão 11 para o .NET Framework 4.6.1, mas o runtime do Functions direcionado para o .NET Framework 4.6.1 só é compatível com o `Newtonsoft.Json` 9.0.1. Portanto, o código de sua função nesse projeto também tem que usar `Newtonsoft.Json` 9.0.1.

O código-fonte para `Microsoft.NET.Sdk.Functions`está disponível no repositório GitHub [azure\-functions\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Versão de runtime

O Visual Studio usa as [Ferramentas Essenciais do Azure Functions](functions-run-local.md#install-the-azure-functions-core-tools) para executar projetos do Functions. As Ferramentas Essenciais são uma interface de linha de comando para o runtime do Functions.

Se você instalar as Ferramentas Essenciais usando npm, isso não afetará a versão das Ferramentas Essenciais usada pelo Visual Studio. Para a versão de runtime do Functions 1.x, o Visual Studio armazena as versões das Ferramentas Essenciais em *%USERPROFILE%\AppData\Local\Azure.Functions.Cli* e usa a versão mais recente armazenada ali. Para o Functions 2.x, as Ferramentas Essenciais serão incluídas na extensão **Azure Functions e Ferramentas de Trabalhos Web**. Para 1.x e 2.x, você pode ver qual versão está sendo usado na saída do console ao executar um projeto do Functions:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="readytorun"></a>ReadyToRun

Você pode compilar seu aplicativo de funções como [binários ReadyToRun](/dotnet/core/whats-new/dotnet-core-3-0#readytorun-images). ReadyToRun é uma forma de compilação antecipada que pode melhorar o desempenho de inicialização para ajudar a reduzir o impacto do [início frio](event-driven-scaling.md#cold-start) durante a execução em um [plano de consumo](consumption-plan.md).

O ReadyToRun está disponível no .NET 3,0 e requer [a versão 3,0 do tempo de execução de Azure Functions](functions-versions.md).

Para compilar seu projeto como ReadyToRun, atualize o arquivo de projeto adicionando os `<PublishReadyToRun>` `<RuntimeIdentifier>` elementos e. Veja a seguir a configuração para a publicação em um aplicativo de funções de bits do Windows 32.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.1</TargetFramework>
  <AzureFunctionsVersion>v3</AzureFunctionsVersion>
  <PublishReadyToRun>true</PublishReadyToRun>
  <RuntimeIdentifier>win-x86</RuntimeIdentifier>
</PropertyGroup>
```

> [!IMPORTANT]
> Atualmente, o ReadyToRun não dá suporte à compilação cruzada. Você deve compilar seu aplicativo na mesma plataforma que o destino de implantação. Além disso, preste atenção ao "meio de bits" configurado em seu aplicativo de funções. Por exemplo, se seu aplicativo de funções no Azure for Windows 64-bit, você deverá compilar seu aplicativo no Windows com `win-x64` o como o [identificador de tempo de execução](/dotnet/core/rid-catalog).

Você também pode criar seu aplicativo com o ReadyToRun na linha de comando. Para obter mais informações, consulte a `-p:PublishReadyToRun=true` opção em [`dotnet publish`](/dotnet/core/tools/dotnet-publish) .

## <a name="supported-types-for-bindings"></a>Tipos com suporte para associações

Cada associação tem seus próprios tipos com suporte. Por exemplo, um atributo de gatilho de blob pode ser aplicado a um parâmetro de cadeia de caracteres, um parâmetro POCO, um parâmetro `CloudBlockBlob` ou qualquer um dos vários outros tipos com suporte. O [artigo de referência de associação para associações de blob](functions-bindings-storage-blob-trigger.md#usage) lista todos os tipos de parâmetro com suporte. Para obter mais informações, consulte [Gatilhos e associações](functions-triggers-bindings.md) e os [documentos de referência de associação para cada tipo de associação](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Associando ao valor de retorno do método

Você pode usar um valor de retorno do método para uma associação de saída, aplicando o atributo ao valor de retorno do método. Para obter exemplos, consulte [Gatilhos e associações](./functions-bindings-return-value.md). 

Use o valor retornado apenas se uma execução de função com êxito sempre resultar em um valor retornado a ser passado para a associação de saída. Caso contrário, use `ICollector` ou `IAsyncCollector`, conforme mostrado na seção a seguir.

## <a name="writing-multiple-output-values"></a>Gravando vários valores de saída

Para gravar vários valores em uma associação de saída ou se uma invocação de função com êxito não resultar em nada a ser passado para a associação de saída, use os tipos [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs). Esses tipos são coleções somente gravação que são gravadas na associação de saída quando o método é concluído.

Este exemplo grava várias mensagens de fila na mesma fila usando `ICollector`:

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="async"></a>Assíncrono

Para tornar uma função [assíncrona](/dotnet/csharp/programming-guide/concepts/async/), use a palavra-chave `async` e retorne um objeto `Task`.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        ILogger log)
    {
        log.LogInformation($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

Não é possível usar parâmetros `out` em funções assíncronas. Para associações de saída, use o [valor de retorno de função](#binding-to-method-return-value) ou um [objeto coletor](#writing-multiple-output-values).

## <a name="cancellation-tokens"></a>Tokens de cancelamento

Uma função pode aceitar um parâmetro [CancellationToken](/dotnet/api/system.threading.cancellationtoken) que permite ao sistema operacional notificar seu código quando a função está prestes a ser encerrada. Você pode usar essa notificação para certificar-se de que a função não finalize inesperadamente de uma maneira que os dados fiquem em um estado inconsistente.

O exemplo a seguir mostra como verificar o encerramento da função iminente.

```csharp
public static class CancellationTokenExample
{
    public static void Run(
        [QueueTrigger("inputqueue")] string inputText,
        TextWriter logger,
        CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(5000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }
}
```

## <a name="logging"></a>Registrando em log

No seu código de função, você pode gravar a saída em logs que aparecem como rastreamentos no Application Insights. A maneira recomendada para gravar nos logs é incluir um parâmetro do tipo [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger), que normalmente é nomeado `log` . Versão 1. x do tempo de execução do Functions usado `TraceWriter` , que também grava em Application insights, mas não dá suporte a logs estruturados. Não use `Console.Write` o para gravar seus logs, pois esses dados não são capturados pelo Application insights. 

### <a name="ilogger"></a>ILogger

Em sua definição de função, inclua um parâmetro [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) , que oferece suporte a [logs estruturados](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Com um objeto `ILogger`, você chama os `Log<level>` [ métodos de extensão no ILogger](/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) para criar logs. O código a seguir grava `Information` logs com a categoria `Function.<YOUR_FUNCTION_NAME>.User.` :

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

Para saber mais sobre como as funções implementam `ILogger` , consulte [coletando dados de telemetria](functions-monitoring.md#collecting-telemetry-data). As categorias prefixadas com `Function` pressupõem que você está usando uma `ILogger` instância. Se você optar por usar um `ILogger<T>` , o nome da categoria poderá, em vez disso, ser baseado em `T` .  

### <a name="structured-logging"></a>Registro em log estruturado

A ordem dos espaços reservados, não seus nomes, determina quais parâmetros são usados na mensagem de log. Suponha que você tenha o seguinte código:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Se você mantiver a mesma cadeia de caracteres de mensagem e inverter a ordem dos parâmetros, o texto da mensagem resultante terá os valores nos locais errados.

Os espaços reservados são tratados dessa forma para que você possa fazer registro em log estruturado. O Application Insights armazena os pares nome-valor do parâmetro e a cadeia de caracteres de mensagem. O resultado é que os argumentos da mensagem tornam-se campos que você pode consultar.

Se sua chamada de método do agente for semelhante ao exemplo anterior, você pode consultar o campo `customDimensions.prop__rowKey`. O prefixo `prop__` é adicionado para garantir que não haja colisões entre campos que o runtime adiciona e campos que o código de função adiciona.

Você também pode consultar a cadeia de caracteres da mensagem original referenciando o campo `customDimensions.prop__{OriginalFormat}`.  

Aqui está uma representação JSON de exemplo de dados `customDimensions`:

```json
{
  "customDimensions": {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="log-custom-telemetry"></a><a name="log-custom-telemetry-in-c-functions"></a>Registrar telemetria personalizada

Há uma versão do SDK do Application Insights específica de Functions, que você pode usar para enviar dados de telemetria personalizada das suas funções para Application Insights: [Microsoft.Azure.WebJobs.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights). Use o comando a seguir, no prompt de comando, para instalar esse pacote:

# <a name="command"></a>[Comando](#tab/cmd)

```cmd
dotnet add package Microsoft.Azure.WebJobs.Logging.ApplicationInsights --version <VERSION>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <VERSION>
```

---

Neste comando, substitua `<VERSION>` por uma versão desse pacote que dê suporte à versão instalada do [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/). 

Os exemplos de C# a seguir usam a [API de telemetria personalizada](../azure-monitor/app/api-custom-events-metrics.md). O exemplo é para uma biblioteca de classes do .NET, mas o código do Application Insights é o mesmo para o script C#.

# <a name="v2x"></a>[v2. x +](#tab/v2)

A versão de runtime 2.x e posteriores usam recursos mais recentes no Application Insights para correlacionar automaticamente a telemetria com a operação atual. Não é necessário definir manualmente os campos da operação `Id`, `ParentId`, ou `Name`.

```cs
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using System.Linq;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Write an event to the customEvents table.
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Generate a custom metric, in this case let's use ContentLength.
            this.telemetryClient.GetMetric("contentLength").TrackValue(req.ContentLength);

            // Log a custom dependency in the dependencies table.
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

Neste exemplo, os dados de métrica personalizados são agregados pelo host antes de serem enviados para a tabela customMetrics. Para saber mais, confira a documentação do [getmetric](../azure-monitor/app/api-custom-events-metrics.md#getmetric) no Application insights. 

Ao executar localmente, você deve adicionar a `APPINSIGHTS_INSTRUMENTATIONKEY` configuração, com a chave de Application insights, ao [local.settings.jsno](functions-run-local.md#local-settings-file) arquivo.


# <a name="v1x"></a>[v1. x](#tab/v1)

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```
---

Não chame `TrackRequest` nem `StartOperation<RequestTelemetry>`, pois você verá solicitações duplicadas de uma invocação de função.  O runtime do Functions controla automaticamente as solicitações.

Não definir `telemetryClient.Context.Operation.Id`. Essa configuração global causa correlação incorreta quando muitas funções são executadas simultaneamente. Em vez disso, crie uma nova instância de telemetria (`DependencyTelemetry`, `EventTelemetry`) e modifique a propriedade `Context`. Em seguida, passe na instância de telemetria para o método `Track` correspondente em `TelemetryClient` (`TrackDependency()`, `TrackEvent()`, `TrackMetric()`). Esse método garante que a telemetria tenha os detalhes de correlação corretos para a invocação de função atual.


## <a name="environment-variables"></a>Variáveis de ambiente

Para obter uma variável de ambiente ou um valor de configuração do aplicativo, use `System.Environment.GetEnvironmentVariable`, conforme mostrado no exemplo de código a seguir:

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
    {
        log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
        log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
        log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    }

    private static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

As configurações do aplicativo podem ser lidas de variáveis de ambiente ao desenvolver localmente e ao executar no Azure. Ao desenvolver localmente, as configurações do aplicativo são provenientes da coleção `Values` no arquivo *local.settings.json*. Em ambos os ambientes, local e do Azure, `GetEnvironmentVariable("<app setting name>")` recupera o valor da configuração de aplicativo nomeada. Por exemplo, quando você estivesse executando localmente, "Nome do Meu Site" seria retornado se o arquivo *local.settings.json* contivesse `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`.

A propriedade [System.Configuration.ConfigurationManager.AppSettings](/dotnet/api/system.configuration.configurationmanager.appsettings) é uma API alternativa para obter os valores de configuração do aplicativo, mas é recomendável que você use `GetEnvironmentVariable` conforme mostrado aqui.

## <a name="binding-at-runtime"></a>Associando no runtime

No C# e em outras linguagens .NET, é possível usar um padrão de associação [obrigatório](https://en.wikipedia.org/wiki/Imperative_programming) em vez de associações [*declarativas*](https://en.wikipedia.org/wiki/Declarative_programming) em atributos. A associação obrigatória é útil quando os parâmetros de associação precisam ser calculado no runtime, em vez do tempo de design. Com esse padrão, é possível se vincular a associações de entrada e saída com suporte instantaneamente no código da função.

Defina uma associação obrigatória da seguinte maneira:

- **Não** inclua um atributo em na assinatura de função para as associações obrigatórias desejadas.
- Passe um parâmetro de entrada [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) ou [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Use o padrão de C# a seguir para realizar a associação de dados.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` é o atributo do .NET que define a associação, e `T` é um tipo de entrada ou saída com suporte nesse tipo de associação. `T` não pode ser um tipo de parâmetro `out` (como `out JObject`). Por exemplo, a associação de saída de tabela dos Aplicativos Móveis dá suporte a [seis tipos de saída](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), mas você só pode usar [ICollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou [IAsyncCollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) com a associação imperativa.

### <a name="single-attribute-example"></a>Exemplo de atributo único

O código de exemplo a seguir cria uma [associação de saída do Armazenamento de Blobs](functions-bindings-storage-blob-output.md) com o caminho do blob definido em tempo de execução e grava uma cadeia de caracteres no blob.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        ILogger log)
    {
        log.LogInformation($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) define a associação de entrada ou saída do [Armazenamento de Blobs](functions-bindings-storage-blob.md) e [TextWriter](/dotnet/api/system.io.textwriter) é um tipo de associação de saída com suporte.

### <a name="multiple-attributes-example"></a>Exemplo de vários atributos

O exemplo anterior obtém a configuração do aplicativo para a cadeia de conexão da conta de armazenamento principal do aplicativo de funções (que é `AzureWebJobsStorage`). É possível especificar uma configuração de aplicativo personalizada a ser usada para a conta de armazenamento adicionando [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) e passando a matriz de atributos para `BindAsync<T>()`. Use um parâmetro `Binder`, não `IBinder`.  Por exemplo:

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            ILogger log)
    {
        log.LogInformation($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>Gatilhos e associações 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre gatilhos e associações](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre práticas recomendadas do Azure Functions](functions-best-practices.md)
