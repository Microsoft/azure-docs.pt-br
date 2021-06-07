---
title: 'Guia de início rápido: API REST de reconhecimento óptico de caracteres'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, comece a usar a API REST de reconhecimento óptico de caracteres.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 01149eed5cc4195ca501507e7fe1d66fffecb84d
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327775"
---
Use a API REST de reconhecimento óptico de caracteres para ler textos impressos e manuscritos.

> [!NOTE]
> Este guia de início rápido usa comandos cURL para chamar a API REST. Você também pode chamar a API REST usando uma linguagem de programação. Confira as amostras do GitHub para obter exemplos em [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/REST), [Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/ComputerVision/REST), [Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/ComputerVision/REST), [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/ComputerVision/REST) e [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/ComputerVision/REST).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/) 
* Depois de obter sua assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Crie um recurso da Pesquisa Visual Computacional"  target="_blank">crie um recurso da Pesquisa Visual Computacional </a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
  * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo ao serviço de Pesquisa Visual Computacional. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
  * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.
* [cURL](https://curl.haxx.se/) instalado



## <a name="read-printed-and-handwritten-text"></a>Ler texto impresso e manuscrito

O serviço de OCR pode ler um texto visível em uma imagem e convertê-lo em um fluxo de caracteres. Para obter mais informações sobre o reconhecimento de texto, confira a [Visão geral do OCR (reconhecimento óptico de caracteres)](../overview-ocr.md).

Para criar e executar o exemplo, siga estas etapas:

1. Copie o seguinte comando em um editor de texto.
1. Faça as alterações a seguir no comando quando necessário:
    1. Substitua o valor de `<subscriptionKey>` pela sua chave de assinatura.
    1. Substitua a primeira parte da URL de solicitação (`westcentralus`) pelo texto em sua URL de ponto de extremidade.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Outra opção é alterar a URL da imagem no corpo da solicitação (`https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\`) para uma URL de uma imagem diferente a ser analisada.
1. Abra una janela de prompt de comando.
1. Cole o comando do editor de texto na janela do prompt de comando e, em seguida, execute-o.

```bash
curl -v -X POST "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/read/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\"}"
```

A resposta incluirá um cabeçalho `Operation-Location`, cujo valor é uma URL exclusiva. Você usa essa URL para consultar os resultados da operação de Leitura. Uma URL expira em 48 horas.

1. Copie o comando a seguir no seu editor de texto.
1. Substitua a URL pelo valor `Operation-Location` copiado na etapa anterior.
1. Faça as alterações a seguir no comando quando necessário:
    1. Substitua o valor de `<subscriptionKey>` pela sua chave de assinatura.
1. Abra una janela de prompt de comando.
1. Cole o comando do editor de texto na janela do prompt de comando e, em seguida, execute-o.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/read/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{body}" 
```

### <a name="examine-the-response"></a>Examinar a resposta

Uma resposta com êxito é retornada em JSON. O aplicativo de exemplo analisa e exibe uma resposta bem-sucedida na janela do prompt de comando, semelhante ao exemplo a seguir:

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-04-08T21:56:17.6819115+00:00",
  "lastUpdatedDateTime": "2021-04-08T21:56:18.4161316+00:00",
  "analyzeResult": {
    "version": "3.2",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 338,
        "height": 479,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              25,
              14,
              318,
              14,
              318,
              59,
              25,
              59
            ],
            "text": "NOTHING",
            "appearance": {
              "style": {
                "name": "other",
                "confidence": 0.971
              }
            },
            "words": [
              {
                "boundingBox": [
                  27,
                  15,
                  294,
                  15,
                  294,
                  60,
                  27,
                  60
                ],
                "text": "NOTHING",
                "confidence": 0.994
              }
            ]
          }
        ]
      }
    ]
  }
}

```



## <a name="next-steps"></a>Próximas etapas

Explore a API de OCR mais detalhadamente. Para testar rapidamente a API de Análise de Imagem, experimente [Abrir o console de teste da API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005/console).

> [!div class="nextstepaction"]
> [Explorar a API de OCR](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)
