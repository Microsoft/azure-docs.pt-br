---
title: 'Início Rápido: Biblioteca de clientes da Análise de Texto para C# | Microsoft Docs'
description: Introdução à biblioteca de clientes da Análise de Texto para C#
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 12/11/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 805ee7f5b210a09335b2177b83777c5caa805858
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98147451"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Versão 3.1, versão prévia](#tab/version-3-1)

[Documentação de referência da v3.1](/dotnet/api/azure.ai.textanalytics?preserve-view=true&view=azure-dotnet-previews) | [Código-fonte da biblioteca v3.1](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics) | [Pacote da v3.1 (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.1.0-beta.3) | [Amostras da v3.1](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

[Documentação de referência da v3](/dotnet/api/azure.ai.textanalytics) | [Código-fonte da biblioteca v3](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics) | [Pacote da v3 (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [Amostras da v3](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

# <a name="version-21"></a>[Versão 2.1](#tab/version-2)

[Documentação de referência da v2](/dotnet/api/overview/azure/cognitiveservices/client?view=azure-dotnet) | [Código-fonte da biblioteca v2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [Pacote da v2 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [Amostras da v2](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* O [IDE do Visual Studio](https://visualstudio.microsoft.com/vs/)
* Após ter sua assinatura do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Crie um recurso de Análise de Texto"  target="_blank">criar um recurso de Análise de Texto <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter sua chave e seu ponto de extremidade.  Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso que você criar para conectar seu aplicativo à API de Análise de Texto. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.
* Para usar o recurso Analisar, você precisará de um recurso de Análise de Texto com o tipo de preço S (Standard).

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-net-core-application"></a>Criar um aplicativo .NET Core

Usando o IDE do Visual Studio, crie um aplicativo de console do .NET Core. Isso criará um projeto "Olá, Mundo" com um arquivo de origem C#: *program.cs*.

# <a name="version-31-preview"></a>[Versão 3.1, versão prévia](#tab/version-3-1)

Instale a biblioteca de cliente clicando com o botão direito do mouse na solução no **Gerenciador de Soluções** e selecionando **Gerenciar Pacotes do NuGet**. No gerenciador de pacotes que é aberto, selecione **Procurar** e pesquise por `Azure.AI.TextAnalytics`. Marque a caixa **Incluir pré-lançamento**, selecione a versão `5.1.0-beta.3` e clique em **Instalar**. Você também pode usar o [Console do Gerenciador de Pacotes](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Instale a biblioteca de cliente clicando com o botão direito do mouse na solução no **Gerenciador de Soluções** e selecionando **Gerenciar Pacotes do NuGet**. No gerenciador de pacotes que é aberto, selecione **Procurar** e pesquise por `Azure.AI.TextAnalytics`. Selecione a versão `5.0.0` e, em seguida, **Instalar**. Você também pode usar o [Console do Gerenciador de Pacotes](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).


> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo [no GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs), que contém os exemplos de código neste início rápido. 

# <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Instale a biblioteca de cliente clicando com o botão direito do mouse na solução no **Gerenciador de Soluções** e selecionando **Gerenciar Pacotes do NuGet**. No gerenciador de pacotes que é aberto, selecione **Procurar** e pesquise `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Clique nele e então em **Instalar**. Você também pode usar o [Console do Gerenciador de Pacotes](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo [no GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs), que contém os exemplos de código neste início rápido. 

---

# <a name="version-31-preview"></a>[Versão 3.1, versão prévia](#tab/version-3-1)

Abra o arquivo *program.cs* e adicione as seguintes diretivas `using`:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Na classe `Program` do aplicativo, crie variáveis para a chave e o ponto de extremidade do recurso.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Substitua o método `Main` do aplicativo. Você definirá os métodos chamados aqui mais adiante.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    SentimentAnalysisWithOpinionMiningExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    RecognizePIIExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Abra o arquivo *program.cs* e adicione as seguintes diretivas `using`:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Na classe `Program` do aplicativo, crie variáveis para a chave e o ponto de extremidade do recurso.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Substitua o método `Main` do aplicativo. Você definirá os métodos chamados aqui mais adiante.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

# <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Abra o arquivo *program.cs* e adicione as seguintes diretivas `using`:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

Na classe `Program` do aplicativo, crie variáveis para a chave e o ponto de extremidade do recurso. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Substitua o método `Main` do aplicativo. Você definirá os métodos chamados aqui mais adiante.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Modelo de objeto

O cliente de Análise de Texto é um objeto `TextAnalyticsClient` que se autentica no Azure usando sua chave e fornece funções para aceitar texto como cadeias de caracteres únicas ou como um lote. Você pode enviar texto para a API de forma síncrona ou assíncrona. O objeto de resposta conterá as informações de análise para cada documento enviado. 

Se estiver usando a versão `3.x` do serviço, use uma instância `TextAnalyticsClientOptions` opcional para inicializar o cliente com várias configurações padrão (por exemplo, idioma padrão ou dica de país/região). Autentique-se também usando um token do Azure Active Directory. 

## <a name="code-examples"></a>Exemplos de código

* [Análise de sentimento](#sentiment-analysis)
* [Mineração de opiniões](#opinion-mining)
* [Detecção de idioma](#language-detection)
* [Reconhecimento de entidade nomeada](#named-entity-recognition-ner)
* [Vinculação de entidade](#entity-linking)
* [Extração de frases-chave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticar o cliente

# <a name="version-31-preview"></a>[Versão 3.1, versão prévia](#tab/version-3-1)

Verifique se o método principal indicado anteriormente cria um objeto de cliente com o ponto de extremidade e as credenciais.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Verifique se o método principal indicado anteriormente cria um objeto de cliente com o ponto de extremidade e as credenciais.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

# <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie uma nova classe `ApiKeyServiceClientCredentials` para armazenar as credenciais e adicione-as às solicitações do cliente. Nela, crie uma substituição para `ProcessHttpRequestAsync()` que adicione sua chave ao cabeçalho `Ocp-Apim-Subscription-Key`.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Crie um método para criar uma instância do objeto [TextAnalyticsClient](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient) com o ponto de extremidade e um objeto `ApiKeyServiceClientCredentials` que contenha a chave.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Análise de sentimento

# <a name="version-31-preview"></a>[Versão 3.1, versão prévia](#tab/version-3-1)

Crie uma função chamada `SentimentAnalysisExample()` que use o cliente que você criou anteriormente e chame sua função `AnalyzeSentiment()`. O objeto `Response<DocumentSentiment>` retornado conterá o rótulo e a pontuação de sentimento de todo o documento de entrada, assim como uma análise de sentimento para cada frase se for bem-sucedido. Se houver um erro, ele gerará uma `RequestFailedException`.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Saída

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

### <a name="opinion-mining"></a>Mineração de opinião

Crie uma função chamada `SentimentAnalysisWithOpinionMiningExample()` que use o cliente que você criou anteriormente e chame sua função `AnalyzeSentimentBatch()` com a opção `AdditionalSentimentAnalyses.OpinionMining`. O objeto `AnalyzeSentimentResultCollection` retornado conterá a coleção de `AnalyzeSentimentResult` que representa `Response<DocumentSentiment>`. A diferença entre `SentimentAnalysis()` e `SentimentAnalysisWithOpinionMiningExample()` é que este conterá `MinedOpinion` em cada sentença, que mostra um aspecto analisado e as opiniões relacionadas. Se houver um erro, ele gerará uma `RequestFailedException`.

```csharp
static void SentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
{
    var documents = new List<string>
    {
        "The food and service were unacceptable, but the concierge were nice."
    };

    AnalyzeSentimentResultCollection reviews = client.AnalyzeSentimentBatch(documents, options: new AnalyzeSentimentOptions()
    {
        IncludeOpinionMining = true
    });

    foreach (AnalyzeSentimentResult review in reviews)
    {
        Console.WriteLine($"Document sentiment: {review.DocumentSentiment.Sentiment}\n");
        Console.WriteLine($"\tPositive score: {review.DocumentSentiment.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {review.DocumentSentiment.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {review.DocumentSentiment.ConfidenceScores.Neutral:0.00}\n");
        foreach (SentenceSentiment sentence in review.DocumentSentiment.Sentences)
        {
            Console.WriteLine($"\tText: \"{sentence.Text}\"");
            Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
            Console.WriteLine($"\tSentence positive score: {sentence.ConfidenceScores.Positive:0.00}");
            Console.WriteLine($"\tSentence negative score: {sentence.ConfidenceScores.Negative:0.00}");
            Console.WriteLine($"\tSentence neutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");

            foreach (MinedOpinion minedOpinion in sentence.MinedOpinions)
            {
                Console.WriteLine($"\tAspect: {minedOpinion.Aspect.Text}, Value: {minedOpinion.Aspect.Sentiment}");
                Console.WriteLine($"\tAspect positive score: {minedOpinion.Aspect.ConfidenceScores.Positive:0.00}");
                Console.WriteLine($"\tAspect negative score: {minedOpinion.Aspect.ConfidenceScores.Negative:0.00}");
                foreach (OpinionSentiment opinion in minedOpinion.Opinions)
                {
                    Console.WriteLine($"\t\tRelated Opinion: {opinion.Text}, Value: {opinion.Sentiment}");
                    Console.WriteLine($"\t\tRelated Opinion positive score: {opinion.ConfidenceScores.Positive:0.00}");
                    Console.WriteLine($"\t\tRelated Opinion negative score: {opinion.ConfidenceScores.Negative:0.00}");
                }
            }
        }
        Console.WriteLine($"\n");
    }
}
```

### <a name="output"></a>Saída

```console
Document sentiment: Positive

        Positive score: 0.84
        Negative score: 0.16
        Neutral score: 0.00

        Text: "The food and service were unacceptable, but the concierge were nice."
        Sentence sentiment: Positive
        Sentence positive score: 0.84
        Sentence negative score: 0.16
        Sentence neutral score: 0.00

        Aspect: food, Value: Negative
        Aspect positive score: 0.01
        Aspect negative score: 0.99
                Related Opinion: unacceptable, Value: Negative
                Related Opinion positive score: 0.01
                Related Opinion negative score: 0.99
        Aspect: service, Value: Negative
        Aspect positive score: 0.01
        Aspect negative score: 0.99
                Related Opinion: unacceptable, Value: Negative
                Related Opinion positive score: 0.01
                Related Opinion negative score: 0.99
        Aspect: concierge, Value: Positive
        Aspect positive score: 1.00
        Aspect negative score: 0.00
                Related Opinion: nice, Value: Positive
                Related Opinion positive score: 1.00
                Related Opinion negative score: 0.00


Press any key to exit.
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Crie uma função chamada `SentimentAnalysisExample()` que use o cliente que você criou anteriormente e chame sua função `AnalyzeSentiment()`. O objeto `Response<DocumentSentiment>` retornado conterá o rótulo e a pontuação de sentimento de todo o documento de entrada, assim como uma análise de sentimento para cada frase se for bem-sucedido. Se houver um erro, ele gerará uma `RequestFailedException`.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Saída

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

# <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie uma nova função chamada `SentimentAnalysisExample()` que use o cliente que você criou anteriormente e chame sua função [Sentiment()](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment). O objeto [SentimentResult](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult) retornado conterá o sentimento `Score` se for bem-sucedido e um `errorMessage` se não. 

Uma pontuação mais próxima de 0 indica um sentimento negativo, enquanto uma pontuação mais próxima de 1 indica um sentimento positivo.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Detecção de idioma

# <a name="version-31-preview"></a>[Versão 3.1, versão prévia](#tab/version-3-1)


Crie uma função chamada `LanguageDetectionExample()` que use o cliente que você criou anteriormente e chame sua função `DetectLanguage()`. O objeto `Response<DetectedLanguage>` retornado conterá o idioma detectado junto com o nome e o código ISO-6391. Se houver um erro, ele gerará uma `RequestFailedException`.

> [!Tip]
> Em alguns casos, pode ser difícil eliminar a ambiguidade de idiomas com base na entrada. Você pode usar o parâmetro `countryHint` para especificar um código de duas letras de país/região. Por padrão, a API está usando o "US" como o countryHint padrão; para remover esse comportamento, você pode redefinir esse parâmetro configurando esse valor como uma cadeia de caracteres vazia `countryHint = ""`. Para definir um padrão diferente, defina a propriedade `TextAnalyticsClientOptions.DefaultCountryHint` e passe-a durante a inicialização do cliente.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Saída

```console
Language:
        French, ISO-6391: fr
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)


Crie uma função chamada `LanguageDetectionExample()` que use o cliente que você criou anteriormente e chame sua função `DetectLanguage()`. O objeto `Response<DetectedLanguage>` retornado conterá o idioma detectado junto com o nome e o código ISO-6391. Se houver um erro, ele gerará uma `RequestFailedException`.

> [!Tip]
> Em alguns casos, pode ser difícil eliminar a ambiguidade de idiomas com base na entrada. Você pode usar o parâmetro `countryHint` para especificar um código de duas letras de país/região. Por padrão, a API está usando o "US" como o countryHint padrão; para remover esse comportamento, você pode redefinir esse parâmetro configurando esse valor como uma cadeia de caracteres vazia `countryHint = ""`. Para definir um padrão diferente, defina a propriedade `TextAnalyticsClientOptions.DefaultCountryHint` e passe-a durante a inicialização do cliente.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Saída

```console
Language:
        French, ISO-6391: fr
```

# <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie uma nova função chamada `languageDetectionExample()` que use o cliente que você criou anteriormente e chame sua função [DetectLanguage()](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). O objeto [LanguageResult](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult) retornado conterá a lista de idiomas detectados em `DetectedLanguages` se for bem-sucedido e um `errorMessage` se não. Imprima o primeiro idioma retornado.

> [!Tip]
> Em alguns casos, pode ser difícil eliminar a ambiguidade de idiomas com base na entrada. Você pode usar o parâmetro `countryHint` para especificar um código de duas letras de país/região. Por padrão, a API está usando o "US" como o countryHint padrão, para remover esse comportamento, você pode redefinir esse parâmetro configurando esse valor como uma cadeia de caracteres vazia `countryHint = ""`.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Saída

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>NER (Reconhecimento de Entidade Nomeada)

# <a name="version-31-preview"></a>[Versão 3.1, versão prévia](#tab/version-3-1)


Crie uma função chamada `EntityRecognitionExample()` que use o cliente que você criou anteriormente, chame sua função `RecognizeEntities()` e itere os resultados. O objeto `Response<CategorizedEntityCollection>` retornado conterá a coleção de entidades `CategorizedEntity` detectadas. Se houver um erro, ele gerará uma `RequestFailedException`.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2},\tLength: {entity.Length},\tOffset: {entity.Offset}\n");
    }
}
```

### <a name="output"></a>Saída

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61,    Length: 4,      Offset: 18

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82,    Length: 7,      Offset: 26

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80,    Length: 9,      Offset: 34
```

### <a name="entity-linking"></a>Vinculação de entidade

Crie uma função chamada `EntityLinkingExample()` que use o cliente que você criou anteriormente, chame sua função `RecognizeLinkedEntities()` e itere os resultados. O objeto `Response<LinkedEntityCollection>` retornado conterá a coleção de entidades `LinkedEntity` detectadas. Se houver um erro, ele gerará uma `RequestFailedException`. Como as entidades vinculadas são unicamente identificadas, as ocorrências da mesma entidade são agrupadas em um objeto `LinkedEntity` como uma lista de objetos `LinkedEntityMatch`.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}");
            Console.WriteLine($"\t\tLength: {match.Length}");
            Console.WriteLine($"\t\tOffset: {match.Offset}\n");
        }
    }
}
```

### <a name="output"></a>Saída

```console
Linked Entities:
        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 0

                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 150

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63
                Length: 10
                Offset: 25

                Text: Gates
                Score: 0.63
                Length: 5
                Offset: 161

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60
                Length: 10
                Offset: 40

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32
                Length: 7
                Offset: 54

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
                Length: 5
                Offset: 89

        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88
                Length: 11
                Offset: 116
```

### <a name="personally-identifiable-information-recognition"></a>Reconhecimento de Informações de Identificação Pessoal

Crie uma função chamada `RecognizePIIExample()` que use o cliente que você criou anteriormente, chame sua função `RecognizePiiEntities()` e itere os resultados. O `PiiEntityCollection` retornado representa a lista de entidades de PII detectadas. Se houver um erro, ele gerará uma `RequestFailedException`.

```csharp
static void RecognizePIIExample(TextAnalyticsClient client)
{
    string document = "A developer with SSN 859-98-0987 whose phone number is 800-102-1100 is building tools with our APIs.";

    PiiEntityCollection entities = client.RecognizePiiEntities(document).Value;

    Console.WriteLine($"Redacted Text: {entities.RedactedText}");
    if (entities.Count > 0)
    {
        Console.WriteLine($"Recognized {entities.Count} PII entit{(entities.Count > 1 ? "ies" : "y")}:");
        foreach (PiiEntity entity in entities)
        {
            Console.WriteLine($"Text: {entity.Text}, Category: {entity.Category}, SubCategory: {entity.SubCategory}, Confidence score: {entity.ConfidenceScore}");
        }
    }
    else
    {
        Console.WriteLine("No entities were found.");
    }
}
```

### <a name="output"></a>Saída

```console
Redacted Text: A developer with SSN *********** whose phone number is ************ is building tools with our APIs.
Recognized 2 PII entities:
Text: 859-98-0987, Category: U.S. Social Security Number (SSN), SubCategory: , Confidence score: 0.65
Text: 800-102-1100, Category: Phone Number, SubCategory: , Confidence score: 0.8
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)


> [!NOTE]
> Novidade da versão `3.0`:
> * A vinculação de entidade agora está separada do reconhecimento de entidade.


Crie uma função chamada `EntityRecognitionExample()` que use o cliente que você criou anteriormente, chame sua função `RecognizeEntities()` e itere os resultados. O objeto `Response<IReadOnlyCollection<CategorizedEntity>>` retornado conterá a lista de entidades detectadas. Se houver um erro, ele gerará uma `RequestFailedException`.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Saída

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80
```

### <a name="entity-linking"></a>Vinculação de entidade

Crie uma função chamada `EntityLinkingExample()` que use o cliente que você criou anteriormente, chame sua função `RecognizeLinkedEntities()` e itere os resultados. O `Response<IReadOnlyCollection<LinkedEntity>>` retornado representa a lista de entidades detectadas. Se houver um erro, ele gerará uma `RequestFailedException`. Como as entidades vinculadas são unicamente identificadas, as ocorrências da mesma entidade são agrupadas em um objeto `LinkedEntity` como uma lista de objetos `LinkedEntityMatch`.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}\n");
        }
    }
}
```

### <a name="output"></a>Saída

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63

                Text: Gates
                Score: 0.63

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55

                Text: Microsoft
                Score: 0.55

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
```

# <a name="version-21"></a>[Versão 2.1](#tab/version-2)

> [!NOTE]
> Na versão 2.1, a vinculação de entidade está incluída na resposta do NER.

Crie uma nova função chamada `RecognizeEntitiesExample()` que use o cliente que você criou anteriormente e chame sua função [Entities()](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). Itere pelos resultados. O objeto [EntitiesResult](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult) retornado conterá a lista de entidades detectadas em `Entities` se for bem-sucedido e um `errorMessage` se não. Para cada entidade detectada, imprima seu tipo, subtipo, nome da Wikipédia (se houver), bem como os locais do texto original.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


### <a name="key-phrase-extraction"></a>Extração de frases-chave

# <a name="version-31-preview"></a>[Versão 3.1, versão prévia](#tab/version-3-1)

Crie uma função chamada `KeyPhraseExtractionExample()` que use o cliente que você criou anteriormente e chame sua função `ExtractKeyPhrases()`. O objeto `<Response<KeyPhraseCollection>` retornado conterá a lista das frases-chave detectadas. Se houver um erro, ele gerará uma `RequestFailedException`.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Saída

```console
Key phrases:
    cat
    veterinarian
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Crie uma função chamada `KeyPhraseExtractionExample()` que use o cliente que você criou anteriormente e chame sua função `ExtractKeyPhrases()`. O objeto `<Response<IReadOnlyCollection<string>>` retornado conterá a lista das frases-chave detectadas. Se houver um erro, ele gerará uma `RequestFailedException`.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Saída

```console
Key phrases:
    cat
    veterinarian
```

# <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie uma nova função chamada `KeyPhraseExtractionExample()` que use o cliente que você criou anteriormente e chame sua função [KeyPhrases()](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). O resultado conterá a lista de frases-chave detectadas em `KeyPhrases` se for bem sucedido e um `errorMessage` se não. Imprima todas as frases-chave detectadas.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Saída

```console
Key phrases:
    cat
    veterinarian
```

---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Usar a API de modo assíncrono com a operação Analisar

# <a name="version-31-preview"></a>[Versão 3.1, versão prévia](#tab/version-3-1)

> [!CAUTION]
> Para usar a operação Analisar, verifique se o recurso do Azure está usando um tipo de preço Standard.

Crie uma função chamada `AnalyzeOperationExample()` que use o cliente que você criou anteriormente e chame sua função `StartAnalyzeOperationBatch()`. O objeto `AnalyzeOperation` retornado conterá o objeto de interface `Operation` para `AnalyzeOperationResult`. Como se trata de uma operação de execução prolongada, `await` em `operation.WaitForCompletionAsync()` para que o valor seja atualizado. Quando `WaitForCompletionAsync()` for concluído, a coleção deverá ser atualizada em `operation.Value`. Se houver um erro, ele gerará uma `RequestFailedException`.


```csharp
static async Task AnalyzeOperationExample(TextAnalyticsClient client)
{
    string inputText = "Microsoft was founded by Bill Gates and Paul Allen.";

    var batchDocuments = new List<string> { inputText };

    AnalyzeOperationOptions operationOptions = new AnalyzeOperationOptions()
    {
        EntitiesTaskParameters = new EntitiesTaskParameters(),
        DisplayName = "Analyze Operation Quick Start Example"
    };

    AnalyzeOperation operation = client.StartAnalyzeOperationBatch(batchDocuments, operationOptions, "en");

    await operation.WaitForCompletionAsync();

    AnalyzeOperationResult resultCollection = operation.Value;

    RecognizeEntitiesResultCollection entitiesResult = resultCollection.Tasks.EntityRecognitionTasks[0].Results;

    Console.WriteLine("Analyze Operation Request Details");
    Console.WriteLine($"    Status: {resultCollection.Status}");
    Console.WriteLine($"    DisplayName: {resultCollection.DisplayName}");
    Console.WriteLine("");

    Console.WriteLine("Recognized Entities");

    foreach (RecognizeEntitiesResult result in entitiesResult)
    {
        Console.WriteLine($"    Recognized the following {result.Entities.Count} entities:");

        foreach (CategorizedEntity entity in result.Entities)
        {
            Console.WriteLine($"    Entity: {entity.Text}");
            Console.WriteLine($"    Category: {entity.Category}");
            Console.WriteLine($"    Offset: {entity.Offset}");
            Console.WriteLine($"    ConfidenceScore: {entity.ConfidenceScore}");
            Console.WriteLine($"    SubCategory: {entity.SubCategory}");
        }
        Console.WriteLine("");
    }
}
```

Depois de adicionar este exemplo ao seu aplicativo, chame o método `main()` usando `await`.

```csharp
await AnalyzeOperationExample(client).ConfigureAwait(false);
```
### <a name="output"></a>Saída

```console
Analyze Operation Request Details
    Status: succeeded
    DisplayName: Analyze Operation Quick Start Example

Recognized Entities
    Recognized the following 3 entities:
    Entity: Microsoft
    Category: Organization
    Offset: 0
    ConfidenceScore: 0.83
    SubCategory: 
    Entity: Bill Gates
    Category: Person
    Offset: 25
    ConfidenceScore: 0.85
    SubCategory: 
    Entity: Paul Allen
    Category: Person
    Offset: 40
    ConfidenceScore: 0.9
    SubCategory: 
```

Você também pode usar a operação Analisar para detectar PII e a extração de frases chave. Confira o [Exemplo do Analisar](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples/Sample_AnalyzeOperation.md) no GitHub.

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Este recurso não está disponível na versão 3.0.

# <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Este recurso não está disponível na versão 2.1.

---
