---
title: Criar análises de transcrições de vídeo usando .NET – Content Moderator
titlesuffix: Azure Cognitive Services
description: Crie análises de transcrições de vídeo usando o SDK do Content Moderator para .NET
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/19/2019
ms.author: sajagtap
ms.openlocfilehash: a3d362f08765cc80b65659b406a2fac3af71f167
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60605534"
---
# <a name="create-video-transcript-reviews-using-net"></a>Criar análises de transcrições de vídeo usando .NET

Este artigo fornece informações e exemplos de código para ajudá-lo a começar rapidamente a usar o [SDK do Content Moderator com C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) para:

- Criar uma análise de vídeo para moderadores humanos
- Adicionar a transcrição moderada à análise
- Publicar a análise

## <a name="prerequisites"></a>Pré-requisitos

- Entrar ou criar uma conta no Content Moderator [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) do site se você ainda não fez isso.
- Este artigo pressupõe que você tenha [moderado o vídeo](video-moderation-api.md) e [criado a análise de vídeo](video-reviews-quickstart-dotnet.md) na ferramenta de análise de tomada de decisões humana. Convém agora adicionar transcrições de vídeo moderadas à ferramenta de análise.

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>Verifique se a chave de API pode chamar a API (criação de trabalho)

Se você tiver iniciado no portal do Azure, depois de concluir as etapas anteriores, talvez haja duas chaves do Content Moderator.

Se você planeja usar a chave de API fornecida pelo Azure em seu exemplo do SDK, siga as etapas mencionadas na seção [Usando a chave do Azure com a API de revisão](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) para permitir que seu aplicativo chame a API de revisão e crie revisões.

Se você usar a chave de avaliação gratuita gerada pela ferramenta de revisão, sua conta da ferramenta de revisão já reconhecerá chave e, portanto, não será necessária nenhuma etapa adicional.

## <a name="prepare-your-video-for-review"></a>Prepare seu vídeo para análise

Adicione a transcrição a uma análise de vídeo. O vídeo deve ser publicado online. Você precisa do ponto de extremidade de streaming. O ponto de extremidade de streaming permite que o player de vídeo da ferramenta de análise reproduza o vídeo.

![Miniatura do vídeo de demonstração](images/ams-video-demo-view.PNG)

- Copie a **URL** da página de [demonstração dos Serviços de Mídia do Microsoft Azure](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) para a URL do manifesto.

## <a name="create-your-visual-studio-project"></a>Crie seu projeto do Visual Studio

1. Adicione um novo projeto do **Aplicativo do console (.NET Framework)** à solução.

1. Nomeie o projeto **VideoTranscriptReviews**.

1. Escolha esse projeto como o único projeto de inicialização para a solução.

### <a name="install-required-packages"></a>Instalar os pacotes necessários

Instale os seguintes pacotes NuGet no projeto TermLists.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Atualize o programa usando as instruções

Modifique o programa usando instruções da seguinte maneria.


```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>Adicione propriedades privadas

Adicione as seguintes propriedades privadas ao namespace VideoTranscriptReviews, classe Program.

Onde indicado, substitua os valores de exemplo dessas propriedades.

```csharp
namespace VideoReviews
{
    class Program
    {
        // NOTE: Replace this example location with the location for your Content Moderator account.
        /// <summary>
        /// The region/location for your Content Moderator account, 
        /// for example, westus.
        /// </summary>
        private static readonly string AzureRegion = "YOUR CONTENT MODERATOR REGION";

        // NOTE: Replace this example key with a valid subscription key.
        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR CONTENT MODERATOR KEY";

        // NOTE: Replace this example team name with your Content Moderator team name.
        /// <summary>
        /// The name of the team to assign the job to.
        /// </summary>
        /// <remarks>This must be the team name you used to create your 
        /// Content Moderator account. You can retrieve your team name from
        /// the Content Moderator web site. Your team name is the Id associated 
        /// with your subscription.</remarks>
        private const string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

        /// <summary>
        /// The base URL fragment for Content Moderator calls.
        /// </summary>
        private static readonly string AzureBaseURL =
            $"{AzureRegion}.api.cognitive.microsoft.com";

        /// <summary>
        /// The minimum amount of time, in milliseconds, to wait between calls
        /// to the Content Moderator APIs.
        /// </summary>
        private const int throttleRate = 2000;
```

### <a name="create-content-moderator-client-object"></a>Crie o objeto de cliente do Content Moderator

Adicione a seguinte definição de método ao namespace VideoTranscriptReviews, classe Program.

```csharp
/// <summary>
/// Returns a new Content Moderator client for your subscription.
/// </summary>
/// <returns>The new client.</returns>
/// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
/// When you have finished using the client,
/// you should dispose of it either directly or indirectly. </remarks>
public static ContentModeratorClient NewClient()
{
    return new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey))
    {
        Endpoint = AzureBaseURL
    };
}
```

## <a name="create-a-video-review"></a>Crie uma análise de vídeo

Crie uma análise de vídeo com **ContentModeratorClient.Reviews.CreateVideoReviews**. Para obter mais informações, confira a [referência da API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** tem os seguintes parâmetros necessários:
1. Uma cadeia de caracteres que contém um tipo MIME, que deve ser "application/json". 
1. O nome da sua equipe do Content Moderator.
1. Uma **IList\<CreateVideoReviewsBodyItem >** objeto. Cada objeto **CreateVideoReviewsBodyItem** representa uma análise de vídeo. Este guia de início rápido cria uma análise de cada vez.

**CreateVideoReviewsBodyItem** tem várias propriedades. Defina, no mínimo, as propriedades a seguir:
- **Content**. A URL do vídeo a ser analisado.
- **ContentId**. Uma ID para atribuir à análise de vídeo.
- **Status**. Defina o valor como "Não publicado." Se você não defini-lo, o padrão será "Pendente", o que significa que a análise de vídeo será publicada e a análise humana ficará como pendente. Após uma análise de vídeo ser publicada, você já não poderá adicionar quadros de vídeo, uma transcrição ou um resultado de moderação da transcrição a ela.

> [!NOTE]
> **CreateVideoReviews** retorna um objeto IList<string>. Cada uma dessas cadeias de caracteres contém uma ID de uma análise de vídeo. Esses IDs são GUIDs e não são iguais ao valor da propriedade **ContentId**.

Adicione a seguinte definição de método ao namespace VideoReviews, classe Program.

```csharp
/// <summary>
/// Create a video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="id">The ID to assign to the video review.</param>
/// <param name="content">The URL of the video to review.</param>
/// <returns>The ID of the video review.</returns>
private static string CreateReview(ContentModeratorClient client, string id, string content)
{
    Console.WriteLine("Creating a video review.");

    List<CreateVideoReviewsBodyItem> body = new List<CreateVideoReviewsBodyItem>() {
        new CreateVideoReviewsBodyItem
        {
            Content = content,
            ContentId = id,
            /* Note: to create a published review, set the Status to "Pending".
            However, you cannot add video frames or a transcript to a published review. */
            Status = "Unpublished",
        }
    };

    var result = client.Reviews.CreateVideoReviews("application/json", TeamName, body);

    Thread.Sleep(throttleRate);

    // We created only one review.
    return result[0];
}
```

> [!NOTE]
> A chave de serviço do Content Moderator possui um limite de taxa de RPS (solicitações por segundo). Se exceder o limite, o SDK lançará uma exceção com um código de erro 429.
>
> Uma chave de camada gratuita tem um limite de taxa de RPS.

## <a name="add-transcript-to-video-review"></a>Adicione transcrição à análise de vídeo

Adicione uma transcrição a uma análise de vídeo com **ContentModeratorClient.Reviews.AddVideoTranscript**. **AddVideoTranscript** tem os seguintes parâmetros necessários:
1. ID da sua equipe do Content Moderator.
1. A ID da análise de vídeo retornada por **CreateVideoReviews**.
1. Um objeto **Stream** que contém a transcrição.

A transcrição deve estar no formato WebVTT. Para obter mais informações, confira [WebVTT: O formato da Web Video Text Tracks](https://www.w3.org/TR/webvtt1/).

> [!NOTE]
> O programa usa uma transcrição de exemplo no formato VTT. Em uma solução real, você usaria o serviço Azure Media Indexer para [gerar uma transcrição](https://docs.microsoft.com/azure/media-services/media-services-index-content) a partir de um vídeo.

Adicione a seguinte definição de método ao namespace VideotranscriptReviews, classe Program.

```csharp
/// <summary>
/// Add a transcript to the indicated video review.
/// The transcript must be in the WebVTT format.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b8b2e7151f0b10d451fe
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscript(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript to the review with ID {0}.", review_id);
    client.Reviews.AddVideoTranscript(TeamName, review_id, new MemoryStream(System.Text.Encoding.UTF8.GetBytes(transcript)));
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Adicione o resultado de uma moderação de transcrição à análise de vídeo

Além de adicionar uma transcrição a uma análise de vídeo, também é possível adicionar o resultado da moderação da transcrição. Isso é feito com **ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult**. Para mais informações, confira a [API de referência](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**AddVideoTranscriptModerationResult** tem os seguintes parâmetros necessários:
1. Uma cadeia de caracteres que contém um tipo MIME, que deve ser "application/json". 
1. O nome da sua equipe do Content Moderator.
1. A ID da análise de vídeo retornada por **CreateVideoReviews**.
1. IList\<TranscriptModerationBodyItem >. Um **TranscriptModerationBodyItem** tem as seguintes propriedades:
1. **Terms**. IList\<TranscriptModerationBodyItemTermsItem >. Um **TranscriptModerationBodyItemTermsItem** tem as seguintes propriedades:
1. **Index**. Índice baseado em zero do termo.
1. **Term**. Uma cadeia de caracteres que contém o termo.
1. **Timestamp**. Uma cadeia de caracteres que contém, em segundos, o tempo na transcrição onde os termos estão localizados.

A transcrição deve estar no formato WebVTT. Para obter mais informações, confira [WebVTT: O formato da Web Video Text Tracks](https://www.w3.org/TR/webvtt1/).

Adicione a seguinte definição de método ao namespace VideoTranscriptReviews, classe Program. Esse método envia uma transcrição ao método **ContentModeratorClient.TextModeration.ScreenText**. Ele também converte o resultado em uma IList\<TranscriptModerationBodyItem > e o envia ao **AddVideoTranscriptModerationResult**.

```csharp
/// <summary>
/// Add the results of moderating a video transcript to the indicated video review.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscriptModerationResult(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript moderation result to the review with ID {0}.", review_id);

    // Screen the transcript using the Text Moderation API. For more information, see:
    // https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f
    Screen screen = client.TextModeration.ScreenText("eng", "text/plain", transcript);

    // Map the term list returned by ScreenText into a term list we can pass to AddVideoTranscriptModerationResult.
    List<TranscriptModerationBodyItemTermsItem> terms = new List<TranscriptModerationBodyItemTermsItem>();
    if (null != screen.Terms)
    {
        foreach (var term in screen.Terms)
        {
            if (term.Index.HasValue)
            {
                terms.Add(new TranscriptModerationBodyItemTermsItem(term.Index.Value, term.Term));
            }
        }
    }

    List<TranscriptModerationBodyItem> body = new List<TranscriptModerationBodyItem>()
    {
        new TranscriptModerationBodyItem()
        {
            Timestamp = "0",
            Terms = terms
        }
    };

    client.Reviews.AddVideoTranscriptModerationResult("application/json", TeamName, review_id, body);

    Thread.Sleep(throttleRate);
}
```

## <a name="publish-video-review"></a>Publicar a análise de vídeo

Você pode publicar uma análise de vídeo com **ContentModeratorClient.Reviews.PublishVideoReview**. **PublishVideoReview** tem os seguintes parâmetros necessários:
1. O nome da sua equipe do Content Moderator.
1. A ID da análise de vídeo retornada por **CreateVideoReviews**.

Adicione a seguinte definição de método ao namespace VideoReviews, classe Programa.

```csharp
/// <summary>
/// Publish the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7bb29e7151f0b10d45201
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void PublishReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Publishing the review with ID {0}.", review_id);
    client.Reviews.PublishVideoReview(TeamName, review_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="putting-it-all-together"></a>Juntando as peças

Adicione a definição de método **Main** ao namespace VideoTranscriptReviews, classe Program. Por fim, feche a classe Program e o namespace VideoTranscriptReviews.

> [!NOTE]
> O programa usa uma transcrição de exemplo no formato VTT. Em uma solução real, você usaria o serviço Azure Media Indexer para [gerar uma transcrição](https://docs.microsoft.com/azure/media-services/media-services-index-content) a partir de um vídeo.

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var transcript = @"WEBVTT

        01:01.000 --> 02:02.000
        First line with a crap word in a transcript.

        02:03.000 --> 02:25.000
        This is another line in the transcript.
        ";

        AddTranscript(client, review_id, transcript);

        AddTranscriptModerationResult(client, review_id, transcript);

        // Publish the review
        PublishReview(client, review_id);

        Console.WriteLine("Open your Content Moderator Dashboard and select Review > Video to see the review.");
        Console.WriteLine("Press any key to close the application.");
        Console.ReadKey();
    }
}
```

## <a name="run-the-program-and-review-the-output"></a>Execute o programa e analise a saída

Quando executar o aplicativo, deverá ver uma saída nas seguintes linhas:

```console
Creating a video review.
Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="navigate-to-your-video-transcript-review"></a>Navegue até sua análise da transcrição de vídeo

Acesse a análise da transcrição de vídeo na sua ferramenta de análise do Content Moderator na tela **Análise**>**Vídeo**>**Transcrição**.

Você verá os seguintes recursos:
- As duas linhas da transcrição que você adicionou
- O termo de profanação encontrado e destacado pelo serviço de moderação de texto
- A marcação do texto de uma transcrição iniciará o vídeo a partir do carimbo de data/hora

![Análise da transcrição de vídeo para moderadores humanos](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Próximas etapas

Obtenha o [SDK do .NET do Content Moderator](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) e a [solução do Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) para este e outros inícios rápidos do Content Moderator para .NET.

Saiba como gerar [análises de vídeo](video-reviews-quickstart-dotnet.md) na ferramenta de análise.

Confira o tutorial detalhado sobre como desenvolver uma [solução completa de moderação de vídeo](video-transcript-moderation-review-tutorial-dotnet.md).
