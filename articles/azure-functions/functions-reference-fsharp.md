---
title: Referência do desenvolvedor em F# do Azure Functions | Microsoft Docs
description: Entenda como desenvolver Azure Functions usando script F#.
services: functions
documentationcenter: fsharp
author: sylvanc
manager: jbronsk
keywords: azure functions, functions, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor, F#
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.service: azure-functions
ms.devlang: fsharp
ms.topic: reference
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: 981ffce34c56f4becee2ed0c72da72baa220e395
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61020238"
---
# <a name="azure-functions-f-developer-reference"></a>Referência do desenvolvedor em F# do Azure Functions

F# para Azure Functions é uma solução para executar facilmente pequenos trechos de código, ou "funções", na nuvem. Fluxos de dados em sua função F# por meio de argumentos de função. Os nomes de argumentos são especificados em `function.json`e há nomes predefinidos para acessar itens como a função logger e os tokens de cancelamento. 

>[!IMPORTANT]
>O script F# (.fsx) só tem suporte da [versão 1.x](functions-versions.md#creating-1x-apps) do tempo de execução do Azure Functions. Se você desejar usar o F# com o tempo de execução da versão 2.x, deverá usar um projeto de biblioteca de classes F# pré-compilado (.fs). Crie, gerencie e publique um projeto de biblioteca de classe F# usando o Visual Studio como faria com um projeto de biblioteca de classe [C#](functions-dotnet-class-library.md). Para saber mais sobre as versões do Functions, confira [Visão geral de versões de tempo de execução do Azure Functions](functions-versions.md).

Este artigo pressupõe que você já tenha lido a [referência do desenvolvedor do Azure Functions](functions-reference.md).

## <a name="how-fsx-works"></a>Como funciona o .fsx
Um arquivo `.fsx` é um script de F#. Ele pode ser considerado um projeto em F# contido em um único arquivo. O arquivo contém o código para seu programa (nesse caso, o Azure Function) e diretivas para gerenciar as dependências.

Quando você usa um `.fsx` para um Azure Function, os assemblies normalmente exibidos são incluídos automaticamente, permitindo que você se concentre na função em vez do código "clichê".

## <a name="folder-structure"></a>Estrutura de pastas

A estrutura de pastas para um projeto de script F# é semelhante à seguinte:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Há um arquivo [host.json](functions-host-json.md) compartilhado que pode ser usado para configurar o aplicativo de funções. Cada função possui seu próprio arquivo de código (.fsx) e arquivo de configuração de associação (function.json).

As extensões de associação necessárias na [versão 2.x](functions-versions.md) do tempo de execução do Functions são definidas no arquivo `extensions.csproj`, com os arquivos de biblioteca reais na pasta `bin`. Ao desenvolver localmente, você precisa [registrar as extensões de associação](./functions-bindings-register.md#local-development-azure-functions-core-tools). Ao desenvolver funções no portal do Azure, esse registro é feito para você.

## <a name="binding-to-arguments"></a>Binding para argumentos
Cada associação oferece suporte a um conjunto de argumentos, conforme detalhado na [Referências de gatilhos e de associações do Azure Functions para desenvolvedores](functions-triggers-bindings.md). Por exemplo, uma das associações de argumento com suporte de um gatilho de blob é um POCO, que pode ser expresso usando um registro em F#. Por exemplo: 

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

O Azure Function em F# usará um ou mais argumentos. Quando falamos sobre os argumentos do Azure Functions, nos referimos a argumentos de *entrada* e argumentos de *saída*. Um argumento de entrada é exatamente o que parece: uma entrada para o Azure Function em F#. Um argumento de *saída* são dados mutáveis ou um argumento `byref<>` que serve como uma maneira de passar dados de volta *saindo* de sua função.

No exemplo acima, `blob` é um argumento de entrada e `output` é um argumento de saída. Observe que utilizamos `byref<>` para `output` (não é necessário adicionar a anotação `[<Out>]`). O uso de um tipo `byref<>` permite que sua função altere o registro ou o objeto ao qual o argumento se refere.

Quando um registro em F# é usado como um tipo de entrada, a definição de registro deve ser marcada com `[<CLIMutable>]` para permitir que a estrutura do Azure Functions defina os campos adequadamente antes de passar o registro para a função. Nos bastidores, `[<CLIMutable>]` gera setters para as propriedades de registro. Por exemplo: 

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Uma classe de F# também pode ser usada para ambos os argumentos. Para uma classe, as propriedades geralmente exigirão getters e setters. Por exemplo: 

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Registro em log
Para registrar a saída em seus [logs de streaming](../app-service/troubleshoot-diagnostic-logs.md) em F#, sua função deve usar um argumento do tipo [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Para manter a consistência, recomendamos que esse argumento seja denominado `log`. Por exemplo: 

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Assíncrono
O fluxo de trabalho de `async` pode ser usado, mas o resultado precisa retornar um `Task`. Isso pode ser feito com `Async.StartAsTask`, por exemplo:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Token de cancelamento
Se a sua função precisar manipular o desligamento com cuidado, atribua um argumento [`CancellationToken`](/dotnet/api/system.threading.cancellationtoken) . Isso pode ser combinado com `async`, por exemplo:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Importando namespaces
É possível abrir os namespaces como de costume:

```fsharp
open System.Net
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Os namespaces a seguir são abertos automaticamente:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Referenciando Assemblies Externos
Da mesma forma, referências ao assembly da estrutura podem ser adicionadas com a diretiva `#r "AssemblyName"`.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Os seguintes assemblies são adicionados automaticamente pelo ambiente de hospedagem do Azure Functions:

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Além disso, os seguintes assemblies têm regras de maiúsculas e minúsculas especiais e podem ser referenciados por simplename (por exemplo, `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Se precisar fazer referência a um assembly particular, carregue o arquivo do assembly em uma pasta `bin` relativa à sua função e faça referência a ela usando o nome do arquivo (por exemplo, `#r "MyAssembly.dll"`). Para obter informações sobre como carregar arquivos na pasta da função, consulte a seção a seguir sobre gerenciamento de pacotes.

## <a name="editor-prelude"></a>Prelúdio do editor
Um editor que oferece suporte aos Serviços de compilador em F# não estará ciente dos namespaces e assemblies que o Azure Functions inclui automaticamente. Dessa forma, pode ser útil incluir um prelúdio que ajuda o editor a encontrar os assemblies que você está usando e a abrir explicitamente os namespaces. Por exemplo: 

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open System
open Microsoft.Azure.WebJobs.Host
open Microsoft.Extensions.Logging

let Run(blob: string, output: byref<string>, log: ILogger) =
    ...
```

Quando o Azure Functions executa seu código, ele processa o código-fonte com `COMPILED` definido, e o prelúdio do editor é ignorado.

<a name="package"></a>

## <a name="package-management"></a>Gerenciamento de pacote
Para usar os pacotes do NuGet em uma função F#, adicione um arquivo `project.json` à pasta da função, no sistema de arquivos do aplicativo de funções. Veja um exemplo de arquivo `project.json` que adiciona uma referência do pacote NuGet ao `Microsoft.ProjectOxford.Face` versão 1.1.0:

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

Somente o .NET Framework 4.6 tem suporte. Desse modo, tenha certeza de que o arquivo `project.json` especifica `net46`, como mostrado aqui.

Quando você carrega um arquivo `project.json` , o tempo de execução obtém os pacotes e adiciona referências automaticamente aos assemblies do pacote. Você não precisa adicionar diretivas `#r "AssemblyName"` . Basta adicionar as instruções `open` necessárias ao seu arquivo `.fsx`.

Talvez você queira colocar automaticamente assemblies de referência no prelúdio de seu editor, para melhorar a interação do editor com os Serviços de compilação em F#.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Como adicionar um arquivo `project.json` ao Azure Function
1. Comece verificando se o aplicativo está em execução, o que pode ser feito abrindo a função no portal do Azure. Isso também permite acessar os logs de streaming nos quais a saída da instalação do pacote será exibida.
2. Para carregar um arquivo `project.json` , use um dos métodos descritos em [Como atualizar os arquivos de aplicativo de funções](functions-reference.md#fileupdate). Se você estiver usando a [Implantação contínua para o Azure Functions](functions-continuous-deployment.md), adicione um arquivo `project.json` à sua ramificação de preparo a fim de testar antes de adicioná-lo à sua ramificação de implantação.
3. Depois que o arquivo `project.json` for adicionado, você verá uma saída semelhante ao exemplo a seguir em seu log de streaming da função:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Variáveis de ambiente
Para obter uma variável de ambiente ou um valor de configuração do aplicativo, use `System.Environment.GetEnvironmentVariable`, por exemplo:

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Reutilizar o código .fsx
Você pode usar código de outros arquivos `.fsx` usando uma diretiva `#load`. Por exemplo: 

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: ILogger) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: ILogger, text: string) =
    log.LogInformation(text);
```

Os caminhos fornecidos para a diretiva `#load` são relativos ao local de seu arquivo `.fsx`.

* `#load "logger.fsx"` carrega um arquivo localizado na pasta de função.
* `#load "package\logger.fsx"` carrega um arquivo localizado na pasta `package`na pasta da função.
* `#load "..\shared\mylogger.fsx"` carrega um arquivo localizado em uma pasta `shared`no mesmo nível que a pasta de função, ou seja, diretamente em `wwwroot`.

A diretiva `#load` só funciona com arquivos `.fsx` (script em F#) e não com arquivos `.fs`.

## <a name="next-steps"></a>Próximas etapas
Para saber mais, consulte os recursos a seguir:

* [Guia de F#](/dotnet/articles/fsharp/index)
* [Práticas recomendadas para o Azure Functions](functions-best-practices.md)
* [Referência do desenvolvedor do Azure Functions](functions-reference.md)
* [Gatilhos e associações de Azure Functions](functions-triggers-bindings.md)
* [Teste do Azure Functions](functions-test-a-function.md)
* [Dimensionamento do Azure Functions](functions-scale.md)

