---
title: Criar revisões usando o .NET – Content Moderator
titlesuffix: Azure Cognitive Services
description: Como criar revisões usando o SDK do Azure Content Moderator para .NET.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/19/2019
ms.author: sajagtap
ms.openlocfilehash: 153d42bf4ce4322536d6837be3058d1f9bfb49a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607186"
---
# <a name="create-human-reviews-net"></a>Criar análises humanas (.NET)

Revisões de armazenam e exibem o conteúdo para moderadores humanos avaliar. Quando um usuário conclui uma revisão, os resultados são enviados para um ponto de extremidade de retorno de chamada especificados. Este guia fornece informações e exemplos de código para ajudar você a começar a usar o [SDK de moderador de conteúdo para .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) para:

- Criar um conjunto de análises para moderadores humanos
- Obter o status das análises existentes para moderadores humanos

## <a name="prerequisites"></a>Pré-requisitos

- Entrar ou criar uma conta no Content Moderator [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) site.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Verificar se a chave de API pode chamar a API de revisão para a criação de revisão

Se você tiver iniciado no portal do Azure, depois de concluir as etapas anteriores, talvez haja duas chaves do Content Moderator.

Se você planeja usar a chave de API fornecida pelo Azure em seu exemplo do SDK, siga as etapas mencionadas na seção [Usando a chave do Azure com a API de revisão](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) para permitir que seu aplicativo chame a API de revisão e crie revisões.

Se você usar a chave de avaliação gratuita gerada pela ferramenta de revisão, sua conta da ferramenta de revisão já reconhecerá chave e, portanto, não será necessária nenhuma etapa adicional.

## <a name="create-your-visual-studio-project"></a>Criar seu projeto do Visual Studio

1. Adicione um novo projeto de **Aplicativo do console (.NET Framework)** à solução.

   No código de exemplo, nomeie o projeto **CreateReviews**.

1. Escolha esse projeto como o único projeto de inicialização para a solução.

### <a name="install-required-packages"></a>Instalar os pacotes necessários

Instale os seguintes pacotes NuGet:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Atualizar as instruções de uso do programa

Modifique as instruções de uso do programa.


```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Criar o cliente do Content Moderator

Adicione o código a seguir para criar um cliente do Content Moderator para sua assinatura.

> [!IMPORTANT]
> Atualize os campos **AzureRegion** e **CMSubscriptionKey** com os valores de sua chave de assinatura e o identificador de região.

```csharp
/// <summary>
/// Wraps the creation and configuration of a Content Moderator client.
/// </summary>
/// <remarks>This class library contains insecure code. If you adapt this
/// code for use in production, use a secure method of storing and using
/// your Content Moderator subscription key.</remarks>
public static class Clients
{
    /// <summary>
    /// The region/location for your Content Moderator account,
    /// for example, westus.
    /// </summary>
    private static readonly string AzureRegion = "YOUR API REGION";

    /// <summary>
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureBaseURL =
        $"https://{AzureRegion}.api.cognitive.microsoft.com";

    /// <summary>
    /// Your Content Moderator subscription key.
    /// </summary>
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureBaseURL;
        return client;
    }
}
```

## <a name="create-a-class-to-associate-internal-content-information-with-a-review-id"></a>Criar uma classe para associar informações de conteúdo interno com uma ID de análise

Adicione a seguinte classe à classe **Programa**. Use essa classe para associar o código de análise à ID de conteúdo interno para o item.

```csharp
/// <summary>
    /// Associates the review ID (assigned by the service) to the internal
    /// content ID of the item.
    /// </summary>
    public class ReviewItem
    {
        /// <summary>
        /// The media type for the item to review.
        /// </summary>
        public string Type;

        /// <summary>
        /// The URL of the item to review.
        /// </summary>
        public string Url;

        /// <summary>
        /// The internal content ID for the item to review.
        /// </summary>
        public string ContentId;

        /// <summary>
        /// The ID that the service assigned to the review.
        /// </summary>
        public string ReviewId;
    }
```

### <a name="initialize-application-specific-settings"></a>Inicializar configurações específicas do aplicativo

> [!NOTE]
> A chave de serviço do Content Moderator tem um limite de taxa RPS (solicitações por segundo) e, se o limite exceder, o SDK lançará uma exceção com um código de erro 429.
>
> Uma chave de camada gratuita tem um limite de taxa de RPS.

#### <a name="add-the-following-constants-to-the-program-class-in-programcs"></a>Adicione as seguintes constantes para o **programa** classe em Program.cs

```csharp
/// <summary>
    /// The minimum amount of time, in milliseconds, to wait between calls
    /// to the Image List API.
    /// </summary>
    private const int throttleRate = 3000;

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const double latencyDelay = 45;

    /// <summary>
    /// The name of the log file to create.
    /// </summary>
    /// <remarks>Relative paths are relative to the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";
```

#### <a name="add-the-following-constants-and-static-fields-to-the-program-class-in-programcs"></a>Adicione as seguintes constantes e campos estáticos para o **programa** classe em Program.cs

Atualize esses valores para conter informações específicas da assinatura e equipe.

> [!NOTE]
> Você define a constante TeamName para o nome usado ao criar a assinatura da [ferramenta de análise do Content Moderator](https://contentmoderator.cognitive.microsoft.com/). Você recupera o TeamName da seção **Credenciais** no menu (engrenagem) **Configurações**.
>
> O nome da equipe é o valor do campo **Id** na seção **API**.

```csharp
/// <summary>
/// The name of the team to assign the review to.
/// </summary>
/// <remarks>This must be the team name you used to create your
/// Content Moderator account. You can retrieve your team name from
/// the Content Moderator web site. Your team name is the Id associated
/// with your subscription.</remarks>
private const string TeamName = "YOUR REVIEW TEAM ID";

/// <summary>
/// The optional name of the subteam to assign the review to.
/// </summary>
private const string Subteam = null;

/// <summary>
/// The callback endpoint for completed reviews.
/// </summary>
/// <remarks>Reviews show up for reviewers on your team. 
/// As reviewers complete reviews, results are sent to the
/// callback endpoint using an HTTP POST request.</remarks>
private const string CallbackEndpoint = "YOUR API ENDPOINT";

/// <summary>
/// The media type for the item to review.
/// </summary>
/// <remarks>Valid values are "image", "text", and "video".</remarks>
private const string MediaType = "image";

/// <summary>
/// The URLs of the images to create review jobs for.
/// </summary>
private static readonly string[] ImageUrls = new string[] {
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg"
    // add more if you want
};

/// <summary>
/// The metadata key to initially add to each review item.
/// </summary>
private const string MetadataKey = "sc";

/// <summary>
/// The metadata value to initially add to each review item.
/// </summary>
private const string MetadataValue = "true";
```

#### <a name="add-the-following-static-fields-to-the-program-class-in-programcs"></a>Adicione os seguintes campos estáticos para o **programa** classe em Program.cs

Use esses campos para acompanhar o estado do aplicativo.

```csharp
/// <summary>
/// A static reference to the text writer to use for logging.
/// </summary>
private static TextWriter writer;

/// <summary>
/// The cached review information, associating a local content ID
/// to the created review ID for each item.
/// </summary>
private static List<ReviewItem> reviewItems =
    new List<ReviewItem>();
```

## <a name="create-a-method-to-write-messages-to-the-log-file"></a>Criar um método para gravar mensagens no arquivo de log

Adicione o seguinte método à classe **Programa**.

```csharp
/// <summary>
/// Writes a message to the log file, and optionally to the console.
/// </summary>
/// <param name="message">The message.</param>
/// <param name="echo">if set to <c>true</c>, write the message to the console.</param>
private static void WriteLine(string message = null, bool echo = false)
{
    writer.WriteLine(message ?? String.Empty);

    if (echo)
    {
        Console.WriteLine(message ?? String.Empty);
    }
}
```

## <a name="create-a-method-to-create-a-set-of-reviews"></a>Criar um método para criar um conjunto de análises

Normalmente, há alguma lógica comercial para identificar imagens, textos ou vídeos recebidos que precisam ser analisados. No entanto, aqui apenas use uma lista fixa de imagens.

Adicione o seguinte método à classe **Programa**.

```csharp
/// <summary>
/// Create the reviews using the fixed list of images.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
private static void CreateReviews(ContentModeratorClient client)
{
    WriteLine(null, true);
    WriteLine("Creating reviews for the following images:", true);

    // Create the structure to hold the request body information.
    List<CreateReviewBodyItem> requestInfo =
        new List<CreateReviewBodyItem>();

    // Create some standard metadata to add to each item.
    List<CreateReviewBodyItemMetadataItem> metadata =
        new List<CreateReviewBodyItemMetadataItem>(
        new CreateReviewBodyItemMetadataItem[] {
            new CreateReviewBodyItemMetadataItem(
                MetadataKey, MetadataValue)
        });

    // Populate the request body information and the initial cached review information.
    for (int i = 0; i < ImageUrls.Length; i++)
    {
        // Cache the local information with which to create the review.
        var itemInfo = new ReviewItem()
        {
            Type = MediaType,
            ContentId = i.ToString(),
            Url = ImageUrls[i],
            ReviewId = null
        };

        WriteLine($" - {itemInfo.Url}; with id = {itemInfo.ContentId}.", true);

        // Add the item informaton to the request information.
        requestInfo.Add(new CreateReviewBodyItem(
            itemInfo.Type, itemInfo.Url, itemInfo.ContentId,
            CallbackEndpoint, metadata));

        // Cache the review creation information.
        reviewItems.Add(itemInfo);
    }

    var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync(
        "application/json", TeamName, requestInfo);

    // Update the local cache to associate the created review IDs with
    // the associated content.
    var reviewIds = reviewResponse.Result.Body;
    for (int i = 0; i < reviewIds.Count; i++)
    {
        Program.reviewItems[i].ReviewId = reviewIds[i];
    }

    WriteLine(JsonConvert.SerializeObject(
    reviewIds, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="create-a-method-to-get-the-status-of-existing-reviews"></a>Criar um método para obter o status das análises existentes

Adicione o seguinte método à classe **Programa**.

> [!Note]
> Na prática, você configuraria a URL de retorno de chamada `CallbackEndpoint` para o URL que receberia os resultados da análise manual (por meio de uma solicitação HTTP POST). É possível modificar esse método para verificar o status de análises pendentes.

```csharp
/// <summary>
    /// Gets the review details from the server.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    private static void GetReviewDetails(ContentModeratorClient client)
    {
        WriteLine(null, true);
        WriteLine("Getting review details:", true);
        foreach (var item in reviewItems)
        {
            var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(
                TeamName, item.ReviewId);

            WriteLine(
                $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                $"{reviewDetail.Result.Body.Status}.", true);
            WriteLine(JsonConvert.SerializeObject(
                reviewDetail.Result.Body, Formatting.Indented));

            Thread.Sleep(throttleRate);
        }
    }
```

## <a name="add-code-to-create-a-set-of-reviews-and-check-its-status"></a>Adicionar um código para criar um conjunto de análises e verificar o status

Adicione o código a seguir ao método **Principal**.

Esse código simula muitas operações que você executa na definição e gerenciamento da lista, bem como o uso da lista para as imagens da tela. Os recursos de log permitem que você veja os objetos de resposta gerados por chamadas SDK para o serviço mModerator conteúdo.

```csharp
using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
{
    writer = outputWriter;
    using (var client = Clients.NewClient())
    {
        CreateReviews(client);
        GetReviewDetails(client);

        Console.WriteLine();
        Console.WriteLine("Perform manual reviews on the Content Moderator site.");
        Console.WriteLine("Then, press any key to continue.");
        Console.ReadKey();

        Console.WriteLine();
        Console.WriteLine($"Waiting {latencyDelay} seconds for results to propigate.");
        Thread.Sleep(latencyDelay * 1000);

        GetReviewDetails(client);
    }

    writer = null;
    outputWriter.Flush();
    outputWriter.Close();
}

Console.WriteLine();
Console.WriteLine("Press any key to exit...");
Console.ReadKey();
```

## <a name="run-the-program-and-review-the-output"></a>Executar o programa e analisar a saída

Você vê a saída de exemplo a seguir:

```console
Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.
```

Entre na ferramenta de análise do Content Moderator para ver a análise de imagem pendente com o rótulo **sc** definido como **true**. Você também visualiza as marcas padrão **a** e **r** e todas as marcas personalizadas definidas na ferramenta de análise.

Use o botão **Avançar** para enviar.

![Análise de imagem para moderadores humanos](images/moderation-reviews-quickstart-dotnet.PNG)

Em seguida, pressione qualquer tecla para continuar.

```console
Waiting 45 seconds for results to propagate.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.

    Press any key to exit...
```

## <a name="check-out-the-following-output-in-the-log-file"></a>Confira a seguinte saída no arquivo de log.

> [!NOTE]
> No arquivo de saída, as cadeias de caracteres "\{teamname}" e "\{callbackUrl}" refletem os valores dos campos `TeamName` e `CallbackEndpoint`, respectivamente.

As IDs de análise e as URLs de conteúdo de imagem serão diferentes a cada vez que você executar o aplicativo e, quando uma análise for concluída, o campo `reviewerResultTags` refletirá como a análise marcou o item.

```json
Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.
    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.
    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Pending",
        "reviewerResultTags": [],
        "createdBy": "{teamname}",
        "metadata": [
        {
              "key": "sc",
              "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.
    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Complete",
        "reviewerResultTags": [
        {
              "key": "a",
              "value": "False"
        },
        {
              "key": "r",
              "value": "True"
        },
        {
              "key": "sc",
              "value": "True"
        }
        ],
        "createdBy": "{teamname}",
        "metadata": [
        {
              "key": "sc",
              "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }
```

## <a name="your-callback-url-if-provided-receives-this-response"></a>A URL de retorno de chamada, se fornecida, receberá essa resposta

Você visualiza uma resposta como o exemplo a seguir:

```json
{
    "ReviewId": "201801i48a2937e679a41c7966e838c92f5e649",
    "ModifiedOn": "2018-01-06T05:04:40.5525865Z",
    "ModifiedBy": "yourusername",
    "CallBackType": "Review",
    "ContentId": "0",
    "ContentType": "Image",
    "Metadata": {
        "sc": "true"
        },
    "ReviewerResultTags": {
        "a": "False",
        "r": "False",
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Obter o [do SDK do .NET do Content Moderator](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) e baixe a [solução do Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) para esse e outros guias de início rápido do Content Moderator para .NET e começar a trabalhar em seu processo de integração.