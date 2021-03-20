---
title: Criar análises de transcrições de vídeo usando .NET – Content Moderator
titleSuffix: Azure Cognitive Services
description: Saiba como criar análises de transcrição de vídeo usando o SDK de Content Moderator de serviços cognitivas do Azure para .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 326fc2cc162a2ab54b40888250fbeef55ad8800a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96853451"
---
# <a name="create-video-transcript-reviews-using-net"></a>Criar análises de transcrições de vídeo usando .NET

Este artigo fornece informações e exemplos de código para ajudá-lo a começar rapidamente a usar o [SDK do Content Moderator com C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) para:

- Criar uma análise de vídeo para moderadores humanos
- Adicionar a transcrição moderada à análise
- Publicar a análise

## <a name="prerequisites"></a>Pré-requisitos

- Entre ou crie uma conta no site da [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) de Content moderator, se você ainda não tiver feito isso.
- Este artigo pressupõe que você tenha [moderado o vídeo](video-moderation-api.md) e [criado a análise de vídeo](video-reviews-quickstart-dotnet.md) na ferramenta de análise de tomada de decisões humana. Convém agora adicionar transcrições de vídeo moderadas à ferramenta de análise.

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>Verifique se a chave de API pode chamar a API (criação de trabalho)

Se você tiver iniciado no portal do Azure, depois de concluir as etapas anteriores, talvez haja duas chaves do Content Moderator.

Se você planeja usar a chave de API fornecida pelo Azure em seu exemplo do SDK, siga as etapas mencionadas na seção [Usando a chave do Azure com a API de revisão](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) para permitir que seu aplicativo chame a API de revisão e crie revisões.

Se você usar a chave de avaliação gratuita gerada pela ferramenta de revisão, sua conta da ferramenta de revisão já reconhecerá chave e, portanto, não será necessária nenhuma etapa adicional.

## <a name="prepare-your-video-for-review"></a>Prepare seu vídeo para análise

Adicione a transcrição a uma análise de vídeo. O vídeo deve ser publicado online. Você precisa do ponto de extremidade de streaming. O ponto de extremidade de streaming permite que o player de vídeo da ferramenta de análise reproduza o vídeo.

![Miniatura do vídeo de demonstração](images/ams-video-demo-view.PNG)

- Copie a **URL** nesta página de [demonstração dos Serviços de Mídia do Microsoft Azure](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) para a URL do manifesto.

## <a name="create-your-visual-studio-project"></a>Criar seu projeto do Visual Studio

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
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>Adicione propriedades privadas

Adicione as propriedades particulares a seguir ao namespace **VideoTranscriptReviews**, classe **Program**. Atualize os `AzureEndpoint` `CMSubscriptionKey` campos e com os valores de sua URL de ponto de extremidade e chave de assinatura. Você pode encontrá-los na guia **início rápido** do recurso na portal do Azure.

```csharp
namespace VideoReviews
{
    class Program
    {
        // NOTE: Enter a valid endpoint URL
        /// <summary>
        /// The endpoint URL of your subscription
        /// </summary>
        private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

        // NOTE: Enter a valid subscription key.
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
        Endpoint = AzureEndpoint
    };
}
```

## <a name="create-a-video-review"></a>Criar uma análise de vídeo

Crie uma análise de vídeo com **ContentModeratorClient.Reviews.CreateVideoReviews**. Para obter mais informações, confira a [referência da API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** tem os seguintes parâmetros necessários:
1. Uma cadeia de caracteres que contém um tipo MIME, que deve ser "application/json". 
1. O nome da sua equipe do Content Moderator.
1. Um objeto **IList\<CreateVideoReviewsBodyItem>**. Cada objeto **CreateVideoReviewsBodyItem** representa uma análise de vídeo. Este guia de início rápido cria uma análise de cada vez.

**CreateVideoReviewsBodyItem** tem várias propriedades. Defina, no mínimo, as propriedades a seguir:
- **Conteúdo**. A URL do vídeo a ser analisado.
- **ContentId**. Uma ID para atribuir à análise de vídeo.
- **Status**. Defina o valor como "Não publicado." Se você não defini-lo, o padrão será "Pendente", o que significa que a análise de vídeo será publicada e a análise humana ficará como pendente. Após uma análise de vídeo ser publicada, você já não poderá adicionar quadros de vídeo, uma transcrição ou um resultado de moderação da transcrição a ela.

> [!NOTE]
> **CreateVideoReviews** retorna um objeto IList\<string>. Cada uma dessas cadeias de caracteres contém uma ID de uma análise de vídeo. Esses IDs são GUIDs e não são iguais ao valor da propriedade **ContentId**.

Adicione a seguinte definição de método ao namespace VideoReviews, classe Programa.

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

A transcrição deve estar no formato WebVTT. Para mais informações, confira [WebVTT: o formato de faixas de texto de vídeo na Web](https://www.w3.org/TR/webvtt1/).

> [!NOTE]
> O programa usa uma transcrição de exemplo no formato VTT. Em uma solução real, você usaria o serviço Azure Media Indexer para [gerar uma transcrição](../../media-services/previous/media-services-index-content.md) a partir de um vídeo.

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

Além de adicionar uma transcrição a uma análise de vídeo, também é possível adicionar o resultado da moderação da transcrição. Isso é feito com **ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult**. Para obter mais informações, confira a [referência da API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**AddVideoTranscriptModerationResult** tem os seguintes parâmetros necessários:
1. Uma cadeia de caracteres que contém um tipo MIME, que deve ser "application/json". 
1. O nome da sua equipe do Content Moderator.
1. A ID da análise de vídeo retornada por **CreateVideoReviews**.
1. Um objeto IList\<TranscriptModerationBodyItem>. Um **TranscriptModerationBodyItem** tem as seguintes propriedades:
1. **Termos**. Um objeto IList\<TranscriptModerationBodyItemTermsItem>. Um **TranscriptModerationBodyItemTermsItem** tem as seguintes propriedades:
1. **Índice**. Índice baseado em zero do termo.
1. **Termo**. Uma cadeia de caracteres que contém o termo.
1. **Carimbo de data/hora**. Uma cadeia de caracteres que contém, em segundos, o tempo na transcrição onde os termos estão localizados.

A transcrição deve estar no formato WebVTT. Para mais informações, confira [WebVTT: o formato de faixas de texto de vídeo na Web](https://www.w3.org/TR/webvtt1/).

Adicione a seguinte definição de método ao namespace VideoTranscriptReviews, classe Program. Esse método envia uma transcrição ao método **ContentModeratorClient.TextModeration.ScreenText**. Ele também converte o resultado em um objeto IList\<TranscriptModerationBodyItem> e envia a **AddVideoTranscriptModerationResult**.

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
> O programa usa uma transcrição de exemplo no formato VTT. Em uma solução real, você usaria o serviço Azure Media Indexer para [gerar uma transcrição](../../media-services/previous/media-services-index-content.md) a partir de um vídeo.

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
        First line with a negative word in a transcript.

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

## <a name="run-the-program-and-review-the-output"></a>Executar o programa e analisar a saída

Ao executar o aplicativo, você verá uma saída nas seguintes linhas:

```console
Creating a video review.
Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="navigate-to-your-video-transcript-review"></a>Navegue até sua análise da transcrição de vídeo

Vá para a revisão de transcrição de vídeo em sua ferramenta de revisão de Content moderator na tela **revisar** >  > **transcrição** de vídeo.

Você verá os seguintes recursos:
- As duas linhas da transcrição que você adicionou
- O termo de profanação encontrado e destacado pelo serviço de moderação de texto
- A marcação do texto de uma transcrição iniciará o vídeo a partir do carimbo de data/hora

![Análise da transcrição de vídeo para moderadores humanos](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Próximas etapas

Obtenha o [SDK do .NET do Content Moderator](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) e a [solução do Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) para este e outros inícios rápidos do Content Moderator para .NET.

Saiba como gerar [análises de vídeo](video-reviews-quickstart-dotnet.md) na ferramenta de análise.
