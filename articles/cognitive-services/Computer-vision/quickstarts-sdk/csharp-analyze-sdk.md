---
title: 'Início Rápido: Analisar uma imagem – SDK, C#'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você analisa uma imagem usando a biblioteca de clientes C# do Windows da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 48c96ac55ca2192506f9190a9dd9be9c1ee0058f
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603470"
---
# <a name="quickstart-analyze-an-image-using-the-computer-vision-sdk-and-c"></a>Início Rápido: Analisar uma imagem usando o SDK da Pesquisa Visual Computacional e C#

Neste Início Rápido, você analisará uma imagem local e uma imagem remota para extrair características visuais usando a biblioteca de clientes da Pesquisa Visual Computacional para C#. Se desejar, baixe o código neste guia como um aplicativo de exemplo completo no repositório [Pesquisa Visual do CSharp dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

* Uma chave de assinatura da Pesquisa Visual Computacional. É possível obter uma chave de assinatura de avaliação gratuita em [Experimente os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Ou siga as instruções em [Criar uma conta dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar a Pesquisa Visual Computacional e obter sua chave.
* Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).
* O pacote NuGet da biblioteca de clientes [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision). Não é necessário baixar o pacote. As instruções de instalação são fornecidas abaixo.

## <a name="create-and-run-the-sample-application"></a>Criar e executar o aplicativo de exemplo

Para executar a amostra, siga estas etapas:

1. Crie um novo aplicativo de console do Visual C# no Visual Studio.
1. Instale o pacote NuGet da biblioteca de clientes de Pesquisa Visual Computacional.
    1. No menu, clique em **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** e, em seguida, **Gerenciar Pacotes NuGet para a Solução**.
    1. Clique na guia **Procurar** e, na caixa **Pesquisar**, digite "Microsoft.Azure.CognitiveServices.Vision.ComputerVision".
    1. Selecione **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** quando ela for exibida, então clique na caixa de seleção ao lado do nome do seu projeto e escolha **Instalar**.
1. Substitua os conteúdos do *Program.cs* pelo código a seguir. Os métodos `AnalyzeImageAsync` e `AnalyzeImageInStreamAsync` encapsulam a [API REST da Análise de Imagem](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) para imagens remotas e locais, respectivamente.

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading.Tasks;

    namespace ImageAnalyze
    {
        class Program
        {
            // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
            private const string subscriptionKey = "<SubscriptionKey>";

            // localImagePath = @"C:\Documents\LocalImage.jpg"
            private const string localImagePath = @"<LocalImage>";

            private const string remoteImageUrl =
                "https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg";

            // Specify the features to return
            private static readonly List<VisualFeatureTypes> features =
                new List<VisualFeatureTypes>()
            {
                VisualFeatureTypes.Categories, VisualFeatureTypes.Description,
                VisualFeatureTypes.Faces, VisualFeatureTypes.ImageType,
                VisualFeatureTypes.Tags
            };

            static void Main(string[] args)
            {
                ComputerVisionClient computerVision = new ComputerVisionClient(
                    new ApiKeyServiceClientCredentials(subscriptionKey),
                    new System.Net.Http.DelegatingHandler[] { });

                // You must use the same region as you used to get your subscription
                // keys. For example, if you got your subscription keys from westus,
                // replace "westcentralus" with "westus".
                //
                // Free trial subscription keys are generated in the "westus"
                // region. If you use a free trial subscription key, you shouldn't
                // need to change the region.

                // Specify the Azure region
                computerVision.Endpoint = "https://westcentralus.api.cognitive.microsoft.com";

                Console.WriteLine("Images being analyzed ...");
                var t1 = AnalyzeRemoteAsync(computerVision, remoteImageUrl);
                var t2 = AnalyzeLocalAsync(computerVision, localImagePath);

                Task.WhenAll(t1, t2).Wait(5000);
                Console.WriteLine("Press ENTER to exit");
                Console.ReadLine();
            }

            // Analyze a remote image
            private static async Task AnalyzeRemoteAsync(
                ComputerVisionClient computerVision, string imageUrl)
            {
                if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
                {
                    Console.WriteLine(
                        "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                    return;
                }

                ImageAnalysis analysis =
                    await computerVision.AnalyzeImageAsync(imageUrl, features);
                DisplayResults(analysis, imageUrl);
            }

            // Analyze a local image
            private static async Task AnalyzeLocalAsync(
                ComputerVisionClient computerVision, string imagePath)
            {
                if (!File.Exists(imagePath))
                {
                    Console.WriteLine(
                        "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                    return;
                }

                using (Stream imageStream = File.OpenRead(imagePath))
                {
                    ImageAnalysis analysis = await computerVision.AnalyzeImageInStreamAsync(
                        imageStream, features);
                    DisplayResults(analysis, imagePath);
                }
            }

            // Display the most relevant caption for the image
            private static void DisplayResults(ImageAnalysis analysis, string imageUri)
            {
                Console.WriteLine(imageUri);
                if (analysis.Description.Captions.Count != 0)
                {
                    Console.WriteLine(analysis.Description.Captions[0].Text + "\n");
                }
                else
                {
                    Console.WriteLine("No description generated.");
                }
            }
        }
    }
    ```

1. Substitua `<Subscription Key>` pela sua chave de assinatura válida.
1. Altere `computerVision.Endpoint` para a região do Azure associada às suas chaves de assinatura, se necessário.
1. Substitua `<LocalImage>` pelo nome de arquivo e caminho de uma imagem local.
1. Opcionalmente, defina `remoteImageUrl` como outra URL de imagem.
1. Execute o programa.

## <a name="examine-the-response"></a>Examinar a resposta

Uma resposta bem-sucedida exibe a legenda mais relevante para cada imagem. Você pode alterar o método `DisplayResults` para gerar diferentes dados de imagem. Confira o método [AnalyzeLocalAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions.analyzeimageinstreamasync?view=azure-dotnet) para saber mais.

Confira [Inícios Rápidos da API: Analisar uma imagem local com o C#](../QuickStarts/CSharp-analyze.md#examine-the-response) para obter um exemplo de uma saída JSON bruta.

```console
https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg
a large waterfall over a rocky cliff
```

## <a name="next-steps"></a>Próximas etapas

Explore as APIs de Pesquisa Visual Computacional usadas para analisar uma imagem, detectar celebridades e marcos, criar uma miniatura e extrair textos manuscritos e impressos.

> [!div class="nextstepaction"]
> [Explorar APIs de Pesquisa Visual Computacional](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
