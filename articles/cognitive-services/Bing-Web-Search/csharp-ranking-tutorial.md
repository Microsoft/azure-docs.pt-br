---
title: Usar classificação para exibir resultados da pesquisa
titleSuffix: Azure Cognitive Services
description: Mostra como usar a resposta RankingResponse do Bing para exibir os resultados da pesquisa em ordem de classificação.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: aahi
ms.openlocfilehash: cfe198352cae95227a76535544d55a6be0c66abc
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203246"
---
# <a name="build-a-console-app-search-client-in-c"></a>Criar um cliente de pesquisa do aplicativo de console no C#

Este tutorial mostra como criar um aplicativo de console .NET Core simples que permite aos usuários consultar a API de Pesquisa na Web do Bing e exibir resultados classificados.

Este tutorial mostra como:

- Fazer uma consulta simples na API de Pesquisa na Web do Bing
- Exibir os resultados da consulta em ordem de classificação

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar o tutorial, você precisa:

- Visual Studio. Caso não o tenha, [baixe e instale o Visual Studio 2017 Community Edition gratuito](https://www.visualstudio.com/downloads/).
- Uma chave de assinatura para a API de Pesquisa na Web do Bing. Caso não tenha uma, [inscreva-se em uma avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

## <a name="create-a-new-console-app-project"></a>Criar um projeto de Aplicativo de Console

No Visual Studio, crie um projeto com `Ctrl`+`Shift`+`N`.

Na caixa de diálogo **Novo Projeto**, selecione **Visual C# > Área de Trabalho Clássica do Windows > Aplicativo de Console (.NET Framework)** .

Nomeie o aplicativo **MyConsoleSearchApp** e, em seguida, clique em **OK**.

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>Adicionar o pacote NuGet JSON.net ao projeto

O JSON.net permite que você trabalhe com as respostas JSON retornadas pela API. Adicione o pacote NuGet ao projeto:

- No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Gerenciar Pacotes NuGet...** .
- Na guia **Procurar**, pesquise `Newtonsoft.Json`. Selecione a última versão e, em seguida, clique em **Instalar**.
- Clique no botão **OK** na janela **Examinar Alterações**.
- Feche a guia do Visual Studio intitulada **NuGet: MyConsoleSearchApp**.

## <a name="add-a-reference-to-systemweb"></a>Adicionar uma referência a System.Web

Este tutorial se baseia no assembly `System.Web`. Adicione uma referência a esse assembly ao projeto:

- No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Referências** e selecione **Adicionar Referência...**
- Selecione **Assemblies > Framework** e, em seguida, role a tela para baixo e marque **System.Web**
- Selecione **OK**

## <a name="add-some-necessary-using-statements"></a>Adicionar algumas instruções using necessárias

O código deste tutorial exige três instruções using adicionais. Adicione estas instruções abaixo das instruções `using` existentes na parte superior de **Program.cs**:

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>Solicitar uma consulta ao usuário

No **Gerenciador de Soluções**, abra **Program.cs**. Atualize o método `Main()`:

```csharp
static void Main()
{
    // Get the user's query
    Console.Write("Enter Bing query: ");
    string userQuery = Console.ReadLine();
    Console.WriteLine();

    // Run the query and display the results
    RunQueryAndDisplayResults(userQuery);

    // Prevent the console window from closing immediately
    Console.WriteLine("\nHit ENTER to exit...");
    Console.ReadLine();
}
```

Este método:

- Solicita uma consulta ao usuário
- Chama `RunQueryAndDisplayResults(userQuery)` para executar a consulta e exibir os resultados
- Aguarda a entrada do usuário para impedir o fechamento imediato da janela do console.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Pesquisar os resultados da consulta usando a API de Pesquisa na Web do Bing

Em seguida, adicione um método que consulta a API e exibe os resultados:

```csharp
static void RunQueryAndDisplayResults(string userQuery)
{
    try
    {
        // Create a query
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<YOUR_SUBSCRIPTION_KEY_GOES_HERE>");
        var queryString = HttpUtility.ParseQueryString(string.Empty);
        queryString["q"] = userQuery;
        var query = "https://api.cognitive.microsoft.com/bing/v7.0/search?" + queryString;

        // Run the query
        HttpResponseMessage httpResponseMessage = client.GetAsync(query).Result;

        // Deserialize the response content
        var responseContentString = httpResponseMessage.Content.ReadAsStringAsync().Result;
        Newtonsoft.Json.Linq.JObject responseObjects = Newtonsoft.Json.Linq.JObject.Parse(responseContentString);

        // Handle success and error codes
        if (httpResponseMessage.IsSuccessStatusCode)
        {
            DisplayAllRankedResults(responseObjects);
        }
        else
        {
            Console.WriteLine($"HTTP error status code: {httpResponseMessage.StatusCode.ToString()}");
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(e.Message);
    }
}
```

Este método:

- Cria um `HttpClient` para consultar a API de Pesquisa na Web
- Define o cabeçalho HTTP `Ocp-Apim-Subscription-Key`, que usa o Bing para autenticar a solicitação
- Executa a solicitação e usa JSON.net para desserializar os resultados
- Chama `DisplayAllRankedResults(responseObjects)` para exibir todos os resultados em ordem de classificação

Lembre-se de definir o valor de `Ocp-Apim-Subscription-Key` com sua chave de assinatura.

## <a name="display-ranked-results"></a>Exibir resultados classificados

Antes de mostrar como exibir os resultados em ordem de classificação, veja um exemplo de resposta de pesquisa na Web:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346...",
        "totalEstimatedMatches" : 982000,
        "value" : [{
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
            "name" : "Contoso Sailing Club - Seattle",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/contososailingsea...",
            "snippet" : "Come sail with Contoso in Seattle...",
            "dateLastCrawled" : "2017-04-07T02:25:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#WebPages.6",
            "name" : "Contoso Sailing Lessons - Official Site",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/www.constososailinglessonsseat...",
            "snippet" : "Contoso sailing lessons in Seattle...",
            "dateLastCrawled" : "2017-04-09T14:30:00"
        },

        ...

        ],
        "someResultsRemoved" : true
    },
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#RelatedSearches",
        "value" : [{
            "text" : "sailing lessons",
            "displayText" : "sailing lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346E..."
        }

        ...

        ]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            }

            ...

            ]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}
```

O objeto JSON `rankingResponse` ([documentação](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse)) descreve a ordem de exibição apropriada para os resultados da pesquisa. Ele inclui um ou mais dos seguintes grupos priorizados:

- `pole`: Os resultados da pesquisa para obter o tratamento mais visível (por exemplo, exibidos acima da linha principal e da barra lateral).
- `mainline`: Os resultados da pesquisa a serem exibidos na linha principal.
- `sidebar`: Os resultados da pesquisa a serem exibidos na barra lateral. Se não houver nenhuma barra lateral, essa opção exibirá os resultados abaixo da linha principal.

A resposta JSON de classificação pode incluir um ou mais desses grupos.

Em **Program.cs**, adicione o seguinte método para exibir os resultados na ordem de classificação correta:

```csharp
static void DisplayAllRankedResults(Newtonsoft.Json.Linq.JObject responseObjects)
{
    string[] rankingGroups = new string[] { "pole", "mainline", "sidebar" };

    // Loop through the ranking groups in priority order
    foreach (string rankingName in rankingGroups)
    {
        Newtonsoft.Json.Linq.JToken rankingResponseItems = responseObjects.SelectToken($"rankingResponse.{rankingName}.items");
        if (rankingResponseItems != null)
        {
            foreach (Newtonsoft.Json.Linq.JObject rankingResponseItem in rankingResponseItems)
            {
                Newtonsoft.Json.Linq.JToken resultIndex;
                rankingResponseItem.TryGetValue("resultIndex", out resultIndex);
                var answerType = rankingResponseItem.Value<string>("answerType");
                switch (answerType)
                {
                    case "WebPages":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("webPages.value"), "WebPage", "name", "url", "displayUrl", "snippet");
                        break;
                    case "News":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("news.value"), "News", "name", "url", "description");
                        break;
                    case "Images":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("images.value"), "Image", "thumbnailUrl");
                        break;
                    case "Videos":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("videos.value"), "Video", "embedHtml");
                        break;
                    case "RelatedSearches":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("relatedSearches.value"), "RelatedSearch", "displayText", "webSearchUrl");
                        break;
                }
            }
        }
    }
}
```

Este método:

- Executa um loop nos grupos `rankingResponse` contidos pela resposta
- Exibe os itens de cada grupo chamando `DisplaySpecificResults(...)`

Em **Program.cs**, adicione os dois seguintes métodos:

```csharp
static void DisplaySpecificResults(Newtonsoft.Json.Linq.JToken resultIndex, Newtonsoft.Json.Linq.JToken items, string title, params string[] fields)
{
    if (resultIndex == null)
    {
        foreach (Newtonsoft.Json.Linq.JToken item in items)
        {
            DisplayItem(item, title, fields);
        }
    }
    else
    {
        DisplayItem(items.ElementAt((int)resultIndex), title, fields);
    }
}

static void DisplayItem(Newtonsoft.Json.Linq.JToken item, string title, string[] fields)
{
    Console.WriteLine($"{title}: ");
    foreach( string field in fields )
    {
        Console.WriteLine($"- {field}: {item[field]}");
    }
    Console.WriteLine();
}
```

Esses métodos funcionam em conjunto para gerar os resultados da pesquisa no console.

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo. O resultado deve ser semelhante ao seguinte:

```
Enter Bing query: sailing lessons seattle

WebPage:
- name: Contoso Sailing Club - Seattle
- url: https://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://contososailingsea....
- snippet: Come sail with Contoso in Seattle...

WebPage:
- name: Contoso Sailing Lessons Seattle - Official Site
- url: http://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://www.constososailinglessonsseat...
- snippet: Contoso sailing lessons in Seattle...

...
```

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre [como usar a classificação para exibir os resultados](rank-results.md).
