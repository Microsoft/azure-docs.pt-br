---
title: Usar o ponto de extremidade de previsão para testar imagens programaticamente com um classificador – Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Saiba como usar a API para testar programaticamente imagens com seu classificador de Serviço de Visão Personalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 7f1939536e033d2cf964dd2f4ee562e4ee20061b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88934745"
---
# <a name="use-your-model-with-the-prediction-api"></a>Usar seu modelo com a API de previsão

Depois de treinar seu modelo, você pode testar imagens programaticamente enviando-as ao ponto de extremidade da API de previsão.

> [!NOTE]
> Este documento demonstra como usar o C# para enviar uma imagem para a API de Previsão. Para obter mais informações e exemplos, consulte a [referência da API de previsão](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Publicar sua iteração treinada

Na [página da Web Visão Personalizada](https://customvision.ai), selecione o projeto e então selecione a guia __Desempenho__.

Para enviar imagens para a API de previsão, primeiro você precisará publicar sua iteração para previsão, o que pode ser feito selecionando __publicar__ e especificando um nome para a iteração publicada. Isso tornará seu modelo acessível para a API de previsão de seu Visão Personalizada recurso do Azure.

![A guia desempenho é mostrada, com um retângulo vermelho ao redor do botão publicar.](./media/use-prediction-api/unpublished-iteration.png)

Depois que o modelo tiver sido publicado com êxito, você verá que um rótulo "publicado" aparece ao lado de sua iteração na barra lateral esquerda e seu nome aparecerá na descrição da iteração.

![A guia desempenho é mostrada, com um retângulo vermelho ao redor do rótulo publicado e o nome da iteração publicada.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Obter a chave de URL e previsão

Depois que o modelo tiver sido publicado, você poderá recuperar as informações necessárias selecionando __URL de previsão__. Isso abrirá uma caixa de diálogo com informações para usar a API de previsão, incluindo a __URL de previsão__ e a __chave de previsão__.

![A guia desempenho é mostrada com um retângulo vermelho ao redor do botão URL de previsão.](./media/use-prediction-api/published-iteration-prediction-url.png)

![A guia desempenho é mostrada com um retângulo vermelho ao redor do valor da URL de previsão para usar um arquivo de imagem e o valor Prediction-Key.](./media/use-prediction-api/prediction-api-info.png)


Neste guia, você usará uma imagem local, portanto, copie a URL em **se você tiver um arquivo de imagem** em um local temporário. Copie também o valor da __chave de previsão__ correspondente.

## <a name="create-the-application"></a>Criar o aplicativo

1. No Visual Studio, crie um novo aplicativo de console em C#.

1. Use o código a seguir como o corpo do arquivo __Program.cs__.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "<Your prediction key>");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "<Your prediction URL>";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

1. Altere as seguintes informações:
   * Defina o `namespace` campo como o nome do seu projeto.
   * Substitua o espaço reservado `<Your prediction key>` pelo valor de chave que você recuperou anteriormente.
   * Substitua o espaço reservado `<Your prediction URL>` pela URL que você recuperou anteriormente.

## <a name="run-the-application"></a>Executar o aplicativo

Ao executar o aplicativo, você será solicitado a inserir um caminho para um arquivo de imagem no console do. A imagem é então enviada para a API de previsão e os resultados da previsão são retornados como uma cadeia de caracteres formatada em JSON. Veja a seguir um exemplo de resposta.

```json
{
    "id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "created":"2019-03-20T16:47:31.322Z",
    "predictions":[
        {"tagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","tagName":"cat", "probability":1.0},
        {"tagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","tagName":"dog", "probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu como enviar imagens para o classificador/detector de imagem personalizada e receber uma resposta programaticamente com o SDK do C#. Em seguida, saiba como concluir cenários de ponta a ponta com o C# ou comece a usar um SDK de linguagem diferente.

* [Início rápido: SDK do Visão Personalizada](quickstarts/image-classification.md)
