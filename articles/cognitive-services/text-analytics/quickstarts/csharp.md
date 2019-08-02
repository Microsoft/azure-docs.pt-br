---
title: 'Início Rápido: Chamar o serviço de Análise de Texto usando o SDK do Azure para .NET e C#'
titleSuffix: Azure Cognitive Services
description: Informações e exemplos de códigos para ajudá-lo a começar a usar o serviço de Análise de Texto e C#.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/18/2019
ms.author: assafi
ms.openlocfilehash: 09713528f51675f6e9d7f3073b6c81b095d23631
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356969"
---
# <a name="quickstart-use-the-net-sdk-and-c-to-call-the-text-analytics-service"></a>Início Rápido: Usar o SDK do .NET e C# para chamar o serviço de Análise de Texto
<a name="HOLTop"></a>

Este início rápido ajuda a começar a usar o SDK do Azure para .NET e C# para analisar a linguagem. Embora a API REST da [Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759711) seja compatível com a maioria das linguagens de programação, o SDK fornece uma maneira fácil de integrar o serviço aos aplicativos.

> [!NOTE]
> O código-fonte deste exemplo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics).

Para obter detalhes técnicos, confira a [referência da Análise de Texto](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet) do SDK para .NET.

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [visual studio 2017 ou posterior]
* O [SDK de Análise de Texto para .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Também há a necessidade do [ponto de extremidade e da chave de acesso](../How-tos/text-analytics-how-to-access-key.md) que foram gerados para você durante a inscrição.

## <a name="create-the-visual-studio-solution-and-install-the-sdk"></a>Criar a solução do Visual Studio e instalar o SDK

1. Crie um novo projeto de aplicativo de console (.NET Core). [Acesse o Visual Studio](https://visualstudio.microsoft.com/vs/).
1. Clique com o botão direito do mouse na solução e selecione **Gerenciar Pacotes NuGet para a solução**.
1. Selecione a guia **Procurar**. Pesquise **Microsoft.Azure.CognitiveServices.Language.TextAnalytics**.

## <a name="authenticate-your-credentials"></a>Autenticar suas credenciais

1. Adicione a instrução `using` a seguir ao arquivo de classe principal (que é o Program.cs por padrão).

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Net.Http;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Rest;
    ```

2. Crie uma nova classe `ApiKeyServiceClientCredentials` para armazenar as credenciais e adicione-as a cada solicitação.

    ```csharp
    class ApiKeyServiceClientCredentials : ServiceClientCredentials
    {
        private readonly string apiKey;

        public ApiKeyServiceClientCredentials(string apiKey)
        {
            this.apiKey = apiKey;
        }

        public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            if (request == null)
            {
                throw new ArgumentNullException("request");
            }
            request.Headers.Add("Ocp-Apim-Subscription-Key", this.apiKey);
            return base.ProcessHttpRequestAsync(request, cancellationToken);
        }
    }
    ```

3. Atualize a classe `Program`. Adicione um membro constante à sua chave de API de Análise de Texto e outro ao ponto de extremidade de serviço. Lembre-se de usar o local do Azure correto para o seu recurso de Análise de Texto.

    ```csharp
    //Enter your Text Analytics (TA) API Key (available in Azure Portal -> your TA resource -> Keys)
    private const string ApiKey = "enter-your-textanalytics-api-key-here";
    //You can get the resource location from Azure Portal -> your TA resource -> Overview
    private const string Endpoint = "enter-your-service-endpoint-here"; // For example: "https://<your-location>.api.cognitive.microsoft.com";
    ```
> [!Tip]
> Para melhorar a segurança de segredos em sistemas de produção, recomendamos usar o [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

## <a name="create-a-text-analytics-client"></a>Criar um cliente de Análise de Texto

Na função `Main` de seu projeto, chame o método de exemplo que você deseja invocar. Passe os parâmetros `Endpoint` e `ApiKey` que você definiu.

```csharp
    public static void Main(string[] args)
    {
        var credentials = new ApiKeyServiceClientCredentials(ApiKey);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = Endpoint
        };

        // Change the console encoding to display non-ASCII characters.
        Console.OutputEncoding = System.Text.Encoding.UTF8;
        SentimentAnalysisExample(client).Wait();
        // DetectLanguageExample(client).Wait();
        // RecognizeEntitiesExample(client).Wait();
        // KeyPhraseExtractionExample(client).Wait();
        Console.ReadLine();
    }
```

As seções a seguir descrevem como chamar cada recurso de serviço.

## <a name="perform-sentiment-analysis"></a>Executar análise de sentimento

1. Crie a nova função `SentimentAnalysisExample()` que usa o cliente criado anteriormente.
2. Gere a lista de objetos `MultiLanguageInput` contendo os documentos que você deseja analisar.

    ```csharp
    public static async Task SentimentAnalysisExample(TextAnalyticsClient client)
    {
        // The documents to be analyzed. Add the language of the document. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "I had the best day of my life.")
            });
        //...
    }
    ```

3. Na mesma função, chame `client.SentimentAsync()` e obtenha o resultado. Em seguida, itere pelos resultados. Imprima a pontuação de sentimento e da ID de cada documento. Uma pontuação mais próxima de 0 indica um sentimento negativo, enquanto uma pontuação mais próxima de 1 indica um sentimento positivo.

    ```csharp
    var result = await client.SentimentAsync(false, inputDocuments);

    // Printing sentiment results
    foreach (var document in result.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Sentiment Score: {document.Score:0.00}");
    }
    ```

### <a name="output"></a>Saída

```console
Document ID: 1 , Sentiment Score: 0.87
```

## <a name="perform-language-detection"></a>Executar a detecção de idioma

1. Crie a nova função `DetectLanguageExample()` que usa o cliente criado anteriormente.
2. Gere uma lista de objetos `LanguageInput` contendo seus documentos.

    ```csharp
    public static async Task DetectLanguageExample(TextAnalyticsClient client)
    {

        // The documents to be submitted for language detection. The ID can be any value.
        var inputDocuments = new LanguageBatchInput(
                new List<LanguageInput>
                    {
                        new LanguageInput(id: "1", text: "This is a document written in English.")
                    });
        //...
    }
    ```

3. Na mesma função, chame `client.DetectLanguageAsync()` e obtenha o resultado. Em seguida, itere pelos resultados. Imprima a ID de cada documento e o primeiro idioma retornado.

    ```csharp
    var langResults = await client.DetectLanguageAsync(false, inputDocuments);

    // Printing detected languages
    foreach (var document in langResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Language: {document.DetectedLanguages[0].Name}");
    }
    ```

### <a name="output"></a>Saída

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
```

## <a name="perform-entity-recognition"></a>Executar reconhecimento de entidade

1. Crie a nova função `RecognizeEntitiesExample()` que usa o cliente criado anteriormente.
2. Gere uma lista de objetos `MultiLanguageBatchInput` contendo seus documentos.

    ```csharp
    public static async Task RecognizeEntitiesExample(TextAnalyticsClient client)
    {
        // The documents to be submitted for entity recognition. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.")
            });
        //...
    }
    ```

3. Na mesma função, chame `client.EntitiesAsync()` e obtenha o resultado. Em seguida, itere pelos resultados. Imprima a ID de cada documento. Para cada entidade detectada, imprima o nome dela no Wikipédia, seu tipo e subtipos (se houver), além dos locais no texto original.

    ```csharp
    var entitiesResult = await client.EntitiesAsync(false, inputDocuments);

    // Printing recognized entities
    foreach (var document in entitiesResult.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} ");

        Console.WriteLine("\t Entities:");
        foreach (var entity in document.Entities)
        {
            Console.WriteLine($"\t\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
            foreach (var match in entity.Matches)
            {
                Console.WriteLine($"\t\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
            }
        }
    }
    ```

### <a name="output"></a>Saída

```console
Document ID: 1
         Entities:
                Name: Microsoft,        Type: Organization,     Sub-Type: N/A
                        Offset: 0,      Length: 9,      Score: 1.000
                Name: Bill Gates,       Type: Person,   Sub-Type: N/A
                        Offset: 25,     Length: 10,     Score: 1.000
                Name: Paul Allen,       Type: Person,   Sub-Type: N/A
                        Offset: 40,     Length: 10,     Score: 0.999
                Name: April 4,  Type: Other,    Sub-Type: N/A
                        Offset: 54,     Length: 7,      Score: 0.800
                Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
                        Offset: 54,     Length: 13,     Score: 0.800
                Name: BASIC,    Type: Other,    Sub-Type: N/A
                        Offset: 89,     Length: 5,      Score: 0.800
                Name: Altair 8800,      Type: Other,    Sub-Type: N/A
                        Offset: 116,    Length: 11,     Score: 0.800
```

## <a name="perform-key-phrase-extraction"></a>Executar uma extração de frases-chave

1. Crie uma nova função `KeyPhraseExtractionExample()` que usa o cliente criado anteriormente.
2. Gere uma lista de objetos `MultiLanguageBatchInput` contendo seus documentos.

    ```csharp
    public static async Task KeyPhraseExtractionExample(TextAnalyticsClient client)
    {
        var inputDocuments = new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>
                    {
                        new MultiLanguageInput("en", "1", "My cat might need to see a veterinarian.")
                    });
        //...
    }
    ```

3. Na mesma função, chame `client.KeyPhrasesAsync()` e obtenha o resultado. Em seguida, itere pelos resultados. Imprima a ID de cada documento e as frases-chave detectadas.

    ```csharp
    var kpResults = await client.KeyPhrasesAsync(false, inputDocuments);

    // Printing keyphrases
    foreach (var document in kpResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} ");

        Console.WriteLine("\t Key phrases:");

        foreach (string keyphrase in document.KeyPhrases)
        {
            Console.WriteLine($"\t\t{keyphrase}");
        }
    }
    ```

### <a name="output"></a>Saída

```console
Document ID: 1
         Key phrases:
                cat
                veterinarian
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Análise de Texto com o Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Visão geral da Análise de Texto](../overview.md)
* [Perguntas frequentes (FAQ)](../text-analytics-resource-faq.md)
