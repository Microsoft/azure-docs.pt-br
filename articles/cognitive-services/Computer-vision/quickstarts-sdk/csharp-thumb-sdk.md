---
title: 'Início Rápido: Gerar uma miniatura – SDK, C#'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você gera uma miniatura de uma imagem usando a biblioteca de clientes C# do Windows da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 78ffd9628c7a65ae60d457bbff3631ea261649d5
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603448"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-sdk-and-c"></a>Início Rápido: Gerar uma miniatura usando C# e o SDK de Pesquisa Visual Computacional

Neste Início Rápido, você gerará uma miniatura de corte inteligente de uma imagem usando o SDK da Pesquisa Visual Computacional para C#. Se desejar, baixe o código neste guia como um aplicativo de exemplo completo no repositório [Pesquisa Visual do CSharp dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

* Uma chave de assinatura da Pesquisa Visual Computacional. É possível obter uma chave de avaliação gratuita em [Experimente os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Ou siga as instruções em [Criar uma conta dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar a Pesquisa Visual Computacional e obter sua chave.
* Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).
* O pacote NuGet da biblioteca de clientes [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision). Não é necessário baixar o pacote. As instruções de instalação são fornecidas abaixo.

## <a name="generatethumbnailasync-method"></a>Método GenerateThumbnailAsync

 Você pode usar esses métodos para gerar uma miniatura de uma imagem. Você especifica a altura e largura, que podem ser diferentes da proporção da imagem de entrada. A Pesquisa Visual Computacional usa o corte inteligente para identificar a área de interesse de modo inteligente e gerar as coordenadas de corte com base nessa região.

Para executar a amostra, siga estas etapas:

1. Crie um novo aplicativo de console do Visual C# no Visual Studio.
1. Instale o pacote NuGet da biblioteca de clientes de Pesquisa Visual Computacional.
    1. No menu, clique em **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** e, em seguida, **Gerenciar Pacotes NuGet para a Solução**.
    1. Clique na guia **Procurar** e, na caixa **Pesquisar**, digite "Microsoft.Azure.CognitiveServices.Vision.ComputerVision".
    1. Selecione **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** quando ela for exibida, então clique na caixa de seleção ao lado do nome do seu projeto e escolha **Instalar**.
1. Substitua `Program.cs` pelo código a seguir. Os métodos `GenerateThumbnailAsync` e `GenerateThumbnailInStreamAsync` encapsulam a [API de Obter Miniatura](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) para imagens locais e remotas, respectivamente. 

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;

    using System;
    using System.IO;
    using System.Threading.Tasks;

    namespace ImageThumbnail
    {
        class Program
        {
            private const bool writeThumbnailToDisk = false;

            // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
            private const string subscriptionKey = "<SubscriptionKey>";

            // localImagePath = @"C:\Documents\LocalImage.jpg"
            private const string localImagePath = @"<LocalImage>";

            private const string remoteImageUrl =
                "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg";

            private const int thumbnailWidth = 100;
            private const int thumbnailHeight = 100;

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

                Console.WriteLine("Images being analyzed ...\n");
                var t1 = GetRemoteThumbnailAsync(computerVision, remoteImageUrl);
                var t2 = GetLocalThumbnailAsnc(computerVision, localImagePath);

                Task.WhenAll(t1, t2).Wait(5000);
                Console.WriteLine("Press ENTER to exit");
                Console.ReadLine();
            }

            // Create a thumbnail from a remote image
            private static async Task GetRemoteThumbnailAsync(
                ComputerVisionClient computerVision, string imageUrl)
            {
                if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
                {
                    Console.WriteLine(
                        "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                    return;
                }

                Stream thumbnail = await computerVision.GenerateThumbnailAsync(
                    thumbnailWidth, thumbnailHeight, imageUrl, true);

                string path = Environment.CurrentDirectory;
                string imageName = imageUrl.Substring(imageUrl.LastIndexOf('/') + 1);
                string thumbnailFilePath =
                    path + "\\" + imageName.Insert(imageName.Length - 4, "_thumb");

                // Save the thumbnail to the current working directory,
                // using the original name with the suffix "_thumb".
                SaveThumbnail(thumbnail, thumbnailFilePath);
            }

            // Create a thumbnail from a local image
            private static async Task GetLocalThumbnailAsnc(
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
                    Stream thumbnail = await computerVision.GenerateThumbnailInStreamAsync(
                        thumbnailWidth, thumbnailHeight, imageStream, true);

                    string thumbnailFilePath =
                        localImagePath.Insert(localImagePath.Length - 4, "_thumb");

                    // Save the thumbnail to the same folder as the original image,
                    // using the original name with the suffix "_thumb".
                    SaveThumbnail(thumbnail, thumbnailFilePath);
                }
            }

            // Save the thumbnail locally.
            // NOTE: This will overwrite an existing file of the same name.
            private static void SaveThumbnail(Stream thumbnail, string thumbnailFilePath)
            {
                if (writeThumbnailToDisk)
                {
                    using (Stream file = File.Create(thumbnailFilePath))
                    {
                        thumbnail.CopyTo(file);
                    }
                }
                Console.WriteLine("Thumbnail {0} written to: {1}\n",
                    writeThumbnailToDisk ? "" : "NOT", thumbnailFilePath);
            }
        }
    }
    ```

1. Substitua `<Subscription Key>` pela sua chave de assinatura válida.
1. Altere `computerVision.Endpoint` para a região do Azure associada às suas chaves de assinatura, se necessário.
1. Opcionalmente, substitua `<LocalImage>` pelo nome de arquivo e caminho de uma imagem local (será ignorado se não for definido).
1. Opcionalmente, defina `remoteImageUrl` para uma imagem diferente.
1. Opcionalmente, defina `writeThumbnailToDisk` como `true` para salvar a miniatura em disco.
1. Execute o programa.


## <a name="examine-the-response"></a>Examinar a resposta

Uma resposta bem-sucedida salva a miniatura de cada imagem localmente e exibe o local da miniatura, por exemplo:

```console
Thumbnail written to: C:\Documents\LocalImage_thumb.jpg

Thumbnail written to: ...\bin\Debug\Bloodhound_Puppy_thumb.jpg
```

## <a name="next-steps"></a>Próximas etapas

Explore as APIs de Pesquisa Visual Computacional usadas para analisar uma imagem, detectar celebridades e marcos, criar uma miniatura e extrair textos manuscritos e impressos.

> [!div class="nextstepaction"]
> [Explorar APIs de Pesquisa Visual Computacional](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
