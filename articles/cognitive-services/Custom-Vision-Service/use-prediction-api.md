---
title: Usar o ponto de extremidade de previsão para testar imagens programaticamente com um classificador – Visão Personalizada
titlesuffix: Azure Cognitive Services
description: Saiba como usar a API para testar programaticamente imagens com seu classificador de Serviço de Visão Personalizada.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 04/02/2019
ms.author: anroth
ms.openlocfilehash: 1ee6edbf49bbcd2014afcf29ed3b737168a3b5bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60816700"
---
# <a name="use-your-model-with-the-prediction-api"></a>Usar o modelo com a API de previsão

Após treinar em seu modelo, você pode testar imagens por meio de programação, enviando-os para o ponto de extremidade de API de previsão.

> [!NOTE]
> Este documento demonstra como usar o C# para enviar uma imagem para a API de Previsão. Para obter mais informações e exemplos, consulte o [referência da API de previsão](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Publicar sua iteração treinada

Na [página da Web Visão Personalizada](https://customvision.ai), selecione o projeto e então selecione a guia __Desempenho__.

Para enviar imagens para a API de previsão, você primeiro precisará publicar sua iteração para previsão, que pode ser feito selecionando __publicar__ e especificar um nome para a iteração publicado. Isso tornará seu modelo acessível para a API de previsão do seu recurso do Azure de visão personalizada.

![É mostrada na guia desempenho, com um retângulo vermelho ao redor do botão Publicar.](./media/use-prediction-api/unpublished-iteration.png)

Depois que seu modelo tiver sido publicado com êxito, você verá um rótulo "Publicado" aparecer ao lado de sua iteração na barra lateral esquerda, e seu nome será exibido na descrição da iteração.

![É mostrada na guia desempenho, com um retângulo vermelho em torno do rótulo publicado e o nome da iteração publicado.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Obter a chave de URL e previsão

Depois que seu modelo tiver sido publicado, você pode recuperar as informações necessárias, selecionando __URL de previsão__. Isso abrirá uma caixa de diálogo com informações sobre como usar a API de previsão, incluindo o __URL de previsão__ e __chave de previsão__.

![A guia de desempenho é mostrada com um retângulo vermelho em torno do botão URL de previsão.](./media/use-prediction-api/published-iteration-prediction-url.png)

![A guia de desempenho é mostrada com um retângulo vermelho em torno do valor da URL de previsão para usar um arquivo de imagem e o valor de chave de previsão.](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> Sua __chave de previsão__ também podem ser encontradas na [portal do Azure](https://portal.azure.com) página para o recurso do Azure de visão personalizada associados ao seu projeto, na __chaves__ folha.

Neste guia, você usar uma imagem local, portanto, copie a URL sob **se você tiver um arquivo de imagem** para um local temporário. Copiar correspondente __chave de previsão__ valor também.

## <a name="create-the-application"></a>Criar o aplicativo

1. No Visual Studio, crie um novo C# aplicativo de console.

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
   * Defina o `namespace` campo para o nome do seu projeto.
   * Substitua o espaço reservado `<Your prediction key>` com o valor de chave que você recuperou anteriormente.
   * Substitua o espaço reservado `<Your prediction URL>` com a URL que você recuperou anteriormente.

## <a name="run-the-application"></a>Executar o aplicativo

Quando você executa o aplicativo, você precisará inserir um caminho para um arquivo de imagem no console do. A imagem, em seguida, é enviada para a API de previsão e os resultados da previsão são retornados como uma cadeia de caracteres formatada em JSON. A seguir está um exemplo de resposta.

```json
{
    "Id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "Project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "Iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "Created":"2019-03-20T16:47:31.322Z",
    "Predictions":[
        {"TagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","TagName":"cat", "Probability":1.0},
        {"TagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","TagName":"dog", "Probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu a enviar imagens para o personalizado/detector de classificador de imagem e recebem uma resposta por meio de programação com o C# SDK. Em seguida, saiba como concluir cenários de ponta a ponta com o C#, ou começar a usar um idioma diferente do SDK.

* [Início rápido: SDK do .NET](csharp-tutorial.md)
* [Início Rápido: SDK do Python](python-tutorial.md)
* [Início Rápido: Java SDK](java-tutorial.md)
* [Início Rápido: SDK do Node](node-tutorial.md)
* [Início Rápido: Acesse o SDK](go-tutorial.md)
