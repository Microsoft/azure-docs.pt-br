---
title: Usar trabalhos de moderação usando o .NET-Content Moderator
titleSuffix: Azure Cognitive Services
description: Use o SDK do .NET Content Moderator para iniciar trabalhos de moderação de conteúdo de ponta a ponta para conteúdo de imagem ou texto na Content Moderator do Azure.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: d98c88a6e1d6dfa23f53c32c85c013b4cf2c468d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565598"
---
# <a name="define-and-use-moderation-jobs-net"></a>Definir e usar trabalhos de moderação (.NET)

Um trabalho de moderação serve como um tipo de wrapper para a funcionalidade de moderação de conteúdo, fluxos de trabalho e revisões. Este guia fornece informações e exemplos de código para ajudá-lo a começar a usar o [SDK do Content moderator para .net](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) para:

- Iniciar um trabalho de moderação para verificar e criar análises para moderadores humanos
- Obter o status da análise pendente
- Controlar e obter o status final da revisão
- Enviar os resultados da revisão para a URL de retorno de chamada

## <a name="prerequisites"></a>Pré-requisitos

- Entre ou crie uma conta no site da [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) de Content Moderator.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Verificar se a chave de API pode chamar a API de revisão para a criação de revisão

Se você tiver iniciado no portal do Azure, depois de concluir as etapas anteriores, talvez haja duas chaves do Content Moderator.

Se você planeja usar a chave de API fornecida pelo Azure em seu exemplo do SDK, siga as etapas mencionadas na seção [Usando a chave do Azure com a API de revisão](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) para permitir que seu aplicativo chame a API de revisão e crie revisões.

Se você usar a chave de avaliação gratuita gerada pela ferramenta de revisão, sua conta da ferramenta de revisão já reconhecerá chave e, portanto, não será necessária nenhuma etapa adicional.

## <a name="define-a-custom-moderation-workflow"></a>Definir um fluxo de trabalho de moderação personalizado

Um trabalho de moderação examina o conteúdo usando as APIs e usa um **fluxo de trabalho** para determinar se deseja criar análises ou não.
Enquanto a ferramenta de análise contém um fluxo de trabalho padrão, vamos [definir um fluxo de trabalho personalizado](Review-Tool-User-Guide/Workflows.md) para este guia de início rápido.

Você pode usar o nome do fluxo de trabalho em seu código que inicia o trabalho de moderação.

## <a name="create-your-visual-studio-project"></a>Criar um projeto do Visual Studio

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

### <a name="initialize-application-specific-settings"></a>Inicialize as configurações específicas do aplicativo

Adicione as seguintes constantes e campos estáticos à classe **Programa** em Program.cs.

> [!NOTE]
> Você define a constante TeamName para o nome usado ao criar a assinatura do Content Moderator. Você pode recuperar o TeamName no [site do Content Moderator](https://westus.contentmoderator.cognitive.microsoft.com/).
> Depois de fazer logon, selecione **Credenciais** do menu **Configurações** (engrenagem).
>
> O nome da equipe é o valor do campo **Id** na seção **API**.

```csharp
/// <summary>
/// The moderation job will use this workflow that you defined earlier.
/// See the quickstart article to learn how to setup custom workflows.
/// </summary>
private const string WorkflowName = "OCR";

/// <summary>
/// The name of the team to assign the job to.
/// </summary>
/// <remarks>This must be the team name you used to create your
/// Content Moderator account. You can retrieve your team name from
/// the Content Moderator web site. Your team name is the Id associated
/// with your subscription.</remarks>
private const string TeamName = "***";

/// <summary>
/// The URL of the image to create a review job for.
/// </summary>
private const string ImageUrl =
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png";

/// <summary>
/// The name of the log file to create.
/// </summary>
/// <remarks>Relative paths are relative to the execution directory.</remarks>
private const string OutputFile = "OutputLog.txt";

/// <summary>
/// The number of seconds to delay after a review has finished before
/// getting the review results from the server.
/// </summary>
private const int latencyDelay = 45;

/// <summary>
/// The callback endpoint for completed reviews.
/// </summary>
/// <remarks>Reviews show up for reviewers on your team.
/// As reviewers complete reviews, results are sent to the
/// callback endpoint using an HTTP POST request.</remarks>
private const string CallbackEndpoint = "";
```

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>Adicionar código para moderar automaticamente, criar uma análise e obter os detalhes do trabalho

> [!Note]
> Na prática, você configura a URL de retorno de chamada **CallbackEndpoint** para a URL que recebe os resultados da análise manual (por meio de uma solicitação HTTP POST).

Comece adicionando o seguinte código ao método **principal**.

```csharp
using (TextWriter writer = new StreamWriter(OutputFile, false))
{
    using (var client = Clients.NewClient())
    {
        writer.WriteLine("Create review job for an image.");
        var content = new Content(ImageUrl);

        // The WorkflowName contains the name of the workflow defined in the online review tool.
        // See the quickstart article to learn more.
        var jobResult = client.Reviews.CreateJobWithHttpMessagesAsync(
                TeamName, "image", "contentID", WorkflowName, "application/json", content, CallbackEndpoint);

        // Record the job ID.
        var jobId = jobResult.Result.Body.JobIdProperty;

        // Log just the response body from the returned task.
        writer.WriteLine(JsonConvert.SerializeObject(
            jobResult.Result.Body, Formatting.Indented));

        Thread.Sleep(2000);
        writer.WriteLine();

        writer.WriteLine("Get review job status.");
        var jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
                TeamName, jobId);

        // Log just the response body from the returned task.
        writer.WriteLine(JsonConvert.SerializeObject(
                jobDetails.Result.Body, Formatting.Indented));

        Console.WriteLine();
        Console.WriteLine("Perform manual reviews on the Content Moderator site.");
        Console.WriteLine("Then, press any key to continue.");
        Console.ReadKey();

        Console.WriteLine();
        Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
        Thread.Sleep(latencyDelay * 1000);

        writer.WriteLine("Get review details.");
        jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
        TeamName, jobId);

        // Log just the response body from the returned task.
        writer.WriteLine(JsonConvert.SerializeObject(
        jobDetails.Result.Body, Formatting.Indented));
    }
    writer.Flush();
    writer.Close();
}
```

> [!NOTE]
> A chave de serviço do Content Moderator possui um limite de taxa de RPS (solicitações por segundo). Se exceder o limite, o SDK lançará uma exceção com um código de erro 429.
>
> Uma chave de camada gratuita tem um limite de taxa de RPS.

## <a name="run-the-program-and-review-the-output"></a>Executar o programa e analisar a saída

Você vê a saída de exemplo a seguir no console:

```console
Perform manual reviews on the Content Moderator site.
Then, press any key to continue.
```

Entre na ferramenta de análise do Content Moderator para ver a análise de imagem pendente.

Use o botão **Avançar** para enviar.

![Análise de imagem para moderadores humanos](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>Consulte a saída de exemplo no arquivo de log

> [!NOTE]
> Em seu arquivo de saída, as cadeias de caracteres **TeamName**, **ContentId**, **CallBackEndpoint**, e **WorkflowId** refletem os valores usados anteriormente.

```json
Create moderation job for an image.
{
    "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
}

Get review details.
{
    "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
    "TeamName": "some team name",
    "Status": "InProgress",
    "WorkflowId": "OCR",
    "Type": "Image",
    "CallBackEndpoint": "",
    "ReviewId": "",
    "ResultMetaData": [],
    "JobExecutionReport": [
    {
        "Ts": "2018-01-07T00:38:26.7714671",
        "Msg": "Successfully got hasText response from Moderator"
    },
    {
        "Ts": "2018-01-07T00:38:26.4181346",
        "Msg": "Getting hasText from Moderator"
    },
    {
        "Ts": "2018-01-07T00:38:25.5122828",
        "Msg": "Starting Execution - Try 1"
    }
    ]
}
```

## <a name="your-callback-url-if-provided-receives-this-response"></a>A URL de retorno de chamada, se fornecida, receberá essa resposta

Você visualiza uma resposta como o exemplo a seguir:

> [!NOTE]
> Na sua resposta de retorno de chamada, as cadeias de cadeia de caracteres **ContentId** e **WorkflowId** refletem os valores que você usou anteriormente.

```json
{
    "JobId": "2018014caceddebfe9446fab29056fd8d31ffe",
    "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
    "WorkFlowId": "OCR",
    "Status": "Complete",
    "ContentType": "Image",
    "CallBackType": "Job",
    "ContentId": "contentID",
    "Metadata": {
        "hastext": "True",
        "ocrtext": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
        "imagename": "contentID"
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Obtenha o [SDK do .NET do Content Moderator](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) e a [solução do Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) para este e outros inícios rápidos do Content Moderator para .NET e comece a trabalhar em seu processo de integração.
