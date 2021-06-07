---
title: Como chamar a API de Leitura
titleSuffix: Azure Cognitive Services
description: Saiba como chamar a API de Leitura e configurar o comportamento dela em detalhes.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: pafarley
ms.openlocfilehash: 54ec669984488d5fe429050faefa0ff2d2e8eb9b
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106287469"
---
# <a name="call-the-read-api"></a>Chamar a API de Leitura

Neste guia, você aprenderá a chamar a API de Leitura para extrair texto de imagens. Você aprenderá as diversas maneiras de configurar o comportamento dessa API para atender às suas necessidades.

## <a name="submit-data-to-the-service"></a>Enviar dados ao serviço

A [Chamada de leitura](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) da API de Leitura usa uma imagem ou documento PDF como entrada e extrai o texto de maneira assíncrona.

`https://{endpoint}/vision/v3.2-preview.3/read/analyze[?language][&pages][&readingOrder]`

A chamada retorna com um campo de cabeçalho de resposta chamado `Operation-Location`. O valor `Operation-Location` é uma URL que contém a ID da Operação a ser usada na próxima etapa.

|Cabeçalho de resposta| Valor de exemplo |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Billing** 
>
> A página de [preços da Pesquisa Visual Computacional](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) inclui o tipo de preço para Leitura. Cada imagem ou página analisada é uma transação. Se você chamar a operação com um documento PDF ou TIFF contendo 100 páginas, a operação de Leitura fará a contagem de 100 transações e você será cobrado por 100 transações. Caso tenha feito 50 chamadas para a operação e cada chamada tiver enviado um documento com 100 páginas, você será cobrado por 50 X 100 = 5000 transações.

## <a name="determine-how-to-process-the-data"></a>Determinar como processar os dados

### <a name="language-specification"></a>Especificação do idioma

A chamada de [Leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) tem um parâmetro de solicitação opcional para o idioma. A Leitura dá suporte à identificação automática de idioma e aos documentos multilíngues. Portanto, forneça apenas um código de idioma se desejar forçar o processamento do documento nesse idioma específico.

### <a name="natural-reading-order-output-latin-languages-only"></a>Saída da ordem de leitura natural (somente idiomas latinos)
Com a [API de versão prévia de leitura 3.2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005), você pode especificar a ordem em que as linhas de texto são geradas com o parâmetro de consulta `readingOrder`. Use `natural` para uma saída de ordem de leitura mais amigável, conforme mostrado no exemplo a seguir. Esse recurso é compatível apenas com idiomas latinos.

:::image border type="content" source="../Images/ocr-reading-order-example.png" alt-text="Exemplo de ordem de Leitura de OCR":::



### <a name="select-pages-or-page-ranges-for-text-extraction"></a>Selecionar páginas ou intervalos de página para extração de texto
Com a [API de versão prévia de Leitura 3.2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005), para grandes documentos com diversas páginas, use o parâmetro de consulta `pages` para especificar números de página ou intervalos de página para extrair o texto apenas dessas páginas. O exemplo a seguir mostra um documento com 10 páginas, com texto extraído para ambos os casos – todas as páginas (1-10) e páginas selecionadas (3-6).

:::image border type="content" source="../Images/ocr-select-pages.png" alt-text="Saída de páginas selecionadas":::

## <a name="get-results-from-the-service"></a>Obter resultados do serviço

A segunda etapa é chamar a operação [Obter Resultados de Leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750). Essa operação usa como entrada a ID da operação que foi criada pela operação de Leitura. 

`https://{endpoint}/vision/v3.2-preview.3/read/analyzeResults/{operationId}`

Ela retorna uma resposta JSON que contém um campo de **status** com os valores possíveis a seguir. 

|Valor | Significado |
|:-----|:----|
| `notStarted`| A operação não foi iniciada. |
| `running`| A operação está sendo processada. |
| `failed`| A operação falhou. |
| `succeeded`| Êxito na operação. |

Execute uma chamada a essa operação de modo iterativo até que ela retorne o valor **succeeded**. Use um intervalo de um a dois segundos para evitar que a taxa de RPS (solicitações por segundo) seja excedida.

> [!NOTE]
> A camada gratuita limita a taxa de solicitação a 20 chamadas por minuto. A camada paga permite 10 RPS (solicitações por segundo) que podem ser aumentadas mediante solicitação. Use o canal de suporte do Azure ou a equipe de conta para solicitar uma taxa mais alta de RPS (solicitação por segundo).

Quando o campo **status** tiver o valor `succeeded`, a resposta JSON conterá o conteúdo de texto extraído da imagem ou do documento. A resposta JSON mantém os agrupamentos de linhas originais de palavras reconhecidas. Isso inclui as linhas de texto extraídas e as respectivas coordenadas da caixa delimitadora. Cada linha de texto inclui todas as palavras extraídas com as respectivas coordenadas e pontuações de confiança.

> [!NOTE]
> Os dados enviados para a operação `Read` são temporariamente criptografados e armazenados em repouso por um curto período e, depois, excluídos. Isso permite que os aplicativos recuperem o texto extraído como parte da resposta do serviço.

### <a name="sample-json-output"></a>Saída JSON de exemplo

Confira o seguinte exemplo de uma resposta JSON bem-sucedida:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              }
            ]
          }
        ]
      }
    ]
  }
}
```

### <a name="handwritten-classification-for-text-lines-latin-languages-only"></a>Classificação manuscrita para linhas de texto (somente idiomas latinos)
A resposta da [API de versão prévia de Leitura 3.2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) inclui classificar se cada linha de texto tem um estilo manuscrito ou não, junto com uma pontuação de confiança. Esse recurso é compatível apenas com idiomas latinos. O exemplo a seguir mostra a classificação manuscrita para o texto na imagem.

:::image border type="content" source="../Images/ocr-handwriting-classification.png" alt-text="Exemplo de classificação de manuscrito de OCR":::

## <a name="next-steps"></a>Próximas etapas

Para usar a API REST, acesse a [Referência da API de Leitura](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005).