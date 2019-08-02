---
title: 'Início Rápido: Ponto de extremidade de previsão da consulta do SDK do C#'
titleSuffix: Azure Cognitive Services
description: Use o SDK do C# para enviar um enunciado de usuário ao LUIS e receber uma previsão.
author: diberry
manager: nitinme
ms.service: cognitive-services
services: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: af20d555a83e8d229ed5d83d3b1d3f242de1e4a8
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275801"
---
# <a name="quickstart-query-prediction-endpoint-with-c-net-sdk"></a>Início Rápido: Consultar o ponto de extremidade de previsão com o SDK do C# do .NET

Use o SDK do .NET, encontrado no [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/), para enviar um enunciado de usuário ao LUIS (Reconhecimento vocal) e receber uma previsão da intenção do usuário. 

Este Início Rápido envia um enunciado de usuário, como `turn on the bedroom light`, para um aplicativo público de Reconhecimento vocal e, em seguida, recebe a previsão e exibe a intenção `HomeAutomation.TurnOn` com a pontuação mais alta e a entidade `HomeAutomation.Room` encontrada no enunciado. 

## <a name="prerequisites"></a>Pré-requisitos

* [Edição do Visual Studio Community 2017](https://visualstudio.microsoft.com/vs/community/)
* Linguagem de programação C# (incluída com o VS Community 2017)
* ID do aplicativo público: df67dcdb-c37d-46af-88e1-8b97951ca1c2

> [!Note]
> A solução completa está disponível no repositório GitHub [cognitive-services-language-understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/sdk-quickstarts/c%23/UsePredictionRuntime).

Procurando mais documentação?

 * [Documentação de referência do SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)


## <a name="get-cognitive-services-or-language-understanding-key"></a>Obter a chave dos Serviços Cognitivos ou do Reconhecimento vocal

Para usar o aplicativo público para automação residencial, você precisará de uma chave válida para previsões de ponto de extremidade. Use uma chave dos Serviços Cognitivos (criada abaixo com a CLI do Azure), que é válida para muitos serviços cognitivos, ou uma chave `Language Understanding`. 

Use o seguinte [comando da CLI do Azure para criar uma chave do Serviço Cognitivo](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create):

```azurecli-interactive
az cognitiveservices account create \
    -n my-cog-serv-resource \
    -g my-cog-serv-resource-group \
    --kind CognitiveServices \
    --sku S0 \
    -l WestEurope \ 
    --yes
```

## <a name="create-net-core-project"></a>Criar um projeto do .NET Core

Crie um projeto de console do .NET Core no Visual Studio Community 2017.

1. Abra o Visual Studio Community 2017.
1. Crie um projeto por meio da seção **Visual C#** escolhendo **Aplicativo de Console (.NET Core)** .
1. Insira o nome do projeto `QueryPrediction`, deixe os valores padrão restantes e selecione **OK**.
    Isso criará um projeto simples com o arquivo de código primário chamado **Program.cs**.

## <a name="add-sdk-with-nuget"></a>Adicionar o SDK com o NuGet

1. No **Gerenciador de Soluções**, selecione o Projeto no modo de exibição de árvore chamado **QueryPrediction** e, em seguida, clique com o botão direito do mouse nele. No menu, selecione **Gerenciar Pacotes NuGet...** .
1. Selecione **Procurar** e, em seguida, insira `Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime`. Quando as informações do pacote forem exibidas, selecione **Instalar** para instalar o pacote no projeto. 
1. Adicione as instruções _using_ a seguir à parte superior de **Program.cs**. Não remova a instrução _using_ existente de `System`. 

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
```

## <a name="create-a-new-method-for-the-prediction"></a>Criar um método para a previsão

Crie um método `GetPrediction` para enviar a consulta ao ponto de extremidade de previsão da consulta. O método criará e configurará todos os objetos necessários e, em seguida, retornará uma `Task` com os resultados da previsão [`LuisResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.luisresult?view=azure-dotnet). 

```csharp
static async  Task<LuisResult> GetPrediction() {
}
```

## <a name="create-credentials-object"></a>Criar um objeto de credenciais

Adicione o código a seguir ao método `GetPrediction` para criar credenciais de cliente com sua chave do Serviço Cognitivo.

Substitua `<REPLACE-WITH-YOUR-KEY>` pela região da chave do Serviço Cognitivo. A chave está localizada no [portal do Azure](https://portal.azure.com) na página Chaves desse recurso.

```csharp
// Use Language Understanding or Cognitive Services key
// to create authentication credentials
var endpointPredictionkey = "<REPLACE-WITH-YOUR-KEY>";
var credentials = new ApiKeyServiceClientCredentials(endpointPredictionkey);
```

## <a name="create-language-understanding-client"></a>Criar um cliente de Reconhecimento vocal

No método `GetPrediction`, após o código anterior, adicione o código a seguir para usar as novas credenciais, criando um objeto de cliente [`LUISRuntimeClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient.-ctor?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Runtime_LUISRuntimeClient__ctor_Microsoft_Rest_ServiceClientCredentials_System_Net_Http_DelegatingHandler___). 

Substitua `<REPLACE-WITH-YOUR-KEY-REGION>` pela região da chave, como `westus`. A região da chave está localizada no [portal do Azure](https://portal.azure.com) na página Visão Geral desse recurso.

```csharp
// Create Luis client and set endpoint
// region of endpoint must match key's region, for example `westus`
var luisClient = new LUISRuntimeClient(credentials, new System.Net.Http.DelegatingHandler[] { });
luisClient.Endpoint = "https://<REPLACE-WITH-YOUR-KEY-REGION>.api.cognitive.microsoft.com";
```

## <a name="set-query-parameters"></a>Definir os parâmetros de consulta

No método `GetPrediction`, após o código anterior, adicione o código a seguir para definir os parâmetros de consulta.

```csharp
// public Language Understanding Home Automation app
var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

// query specific to home automation app
var query = "turn on the bedroom light";

// common settings for remaining parameters
Double? timezoneOffset = null;
var verbose = true;
var staging = false;
var spellCheck = false;
String bingSpellCheckKey = null;
var log = false;
```

## <a name="query-prediction-endpoint"></a>Ponto de extremidade de previsão da consulta

No método `GetPrediction`, após o código anterior, adicione o seguinte código para definir os parâmetros de consulta:

```csharp
// Create prediction client
var prediction = new Prediction(luisClient);

// get prediction
return await prediction.ResolveAsync(appId, query, timezoneOffset, verbose, staging, spellCheck, bingSpellCheckKey, log, CancellationToken.None);
```

## <a name="display-prediction-results"></a>Exibir os resultados da previsão

Altere o método **Main** para chamar o novo método `GetPrediction` e retornar os resultados da previsão:

```csharp
static void Main(string[] args)
{

    var luisResult = GetPrediction().Result;

    // Display query
    Console.WriteLine("Query:'{0}'", luisResult.Query);

    // Display most common properties of query result
    Console.WriteLine("Top intent is '{0}' with score {1}", luisResult.TopScoringIntent.Intent,luisResult.TopScoringIntent.Score);

    // Display all entities detected in query utterance
    foreach (var entity in luisResult.Entities)
    {
        Console.WriteLine("{0}:'{1}' begins at position {2} and ends at position {3}", entity.Type, entity.Entity, entity.StartIndex, entity.EndIndex);
    }

    Console.Write("done");

}
```

## <a name="run-the-project"></a>Executar o projeto

Crie o projeto no Studio e execute o projeto para ver a saída da consulta:

```console
Query:'turn on the bedroom light'
Top intent is 'HomeAutomation.TurnOn' with score 0.809439957
HomeAutomation.Room:'bedroom' begins at position 12 and ends at position 18
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [SDK do .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) e a [documentação de referência do .NET](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet). 

> [!div class="nextstepaction"] 
> [Tutorial: Criar um aplicativo LUIS para determinar as intenções do usuário](luis-quickstart-intents-only.md) 