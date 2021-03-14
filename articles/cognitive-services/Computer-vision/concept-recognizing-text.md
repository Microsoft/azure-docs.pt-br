---
title: OCR (reconhecimento óptico de caracteres)-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados ao OCR (reconhecimento óptico de caracteres) de imagens e documentos com texto impresso e manuscrito usando o API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: e0247560afa8229f4fa5c25ec7dfbbca4f7defb2
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486091"
---
# <a name="optical-character-recognition-ocr"></a>OCR (reconhecimento óptico de caracteres)

O API da Pesquisa Visual Computacional do Azure inclui recursos de OCR (reconhecimento óptico de caracteres) que extraem texto impresso ou manuscrito de imagens. Você pode extrair texto de imagens, como fotos de chapas de licença ou contêineres com números de série, bem como de documentos-faturas, faturas, relatórios financeiros, artigos e muito mais.

## <a name="read-api"></a>API de leitura 

A [API de leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) do pesquisa Visual computacional é a tecnologia de OCR mais recente do Azure ([saiba o que há de novo](./whats-new.md)) que extrai o texto impresso (em vários idiomas), texto manuscrito (somente em inglês), dígitos e símbolos de moeda de imagens e documentos PDF de várias páginas. Ele é otimizado para extrair texto de imagens com texto pesado e documentos PDF de várias páginas com idiomas mistos. Ele dá suporte à detecção de texto impresso e manuscrito na mesma imagem ou documento.

![Como o OCR converte imagens e documentos em uma saída estruturada com texto extraído](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>Requisitos de entrada
A chamada de **leitura** leva imagens e documentos como sua entrada. Eles têm os seguintes requisitos:

* Formatos de arquivo com suporte: JPEG, PNG, BMP, PDF e TIFF
* Para arquivos PDF e TIFF, até 2000 páginas (apenas duas primeiras páginas para a camada gratuita) são processadas.
* O tamanho do arquivo deve ser menor que 50 MB (4 MB para a camada gratuita) e dimensões, pelo menos, 50 x 50 pixels e no máximo 10000 x 10000 pixels. 
* As dimensões de PDF devem ter no máximo 17 x 17 polegadas, correspondendo a tamanhos de papel ofício ou a3 e menores.

> [!NOTE]
> **Entrada de idioma** 
>
> A [chamada de leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) tem um parâmetro de solicitação opcional para o idioma. A leitura dá suporte à identificação automática de idioma e a documentos multilíngues, portanto, forneça apenas um código de idioma se desejar forçar o processamento do documento como aquele idioma específico.

## <a name="ocr-demo-examples"></a>Demonstração de OCR (exemplos)

![Demonstrações de OCR](./Images/ocr-demo.gif)

## <a name="step-1-the-read-operation"></a>Etapa 1: a operação de leitura

A [chamada de leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) da API de leitura usa um documento de imagem ou PDF como entrada e extrai o texto de forma assíncrona. A chamada retorna com um campo de cabeçalho de resposta chamado `Operation-Location` . O `Operation-Location` valor é uma URL que contém a ID da operação a ser usada na próxima etapa.

|Cabeçalho de resposta| URL do resultado |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Billing** 
>
> A página de [preços de pesquisa Visual computacional](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) inclui o tipo de preço para leitura. Cada imagem ou página analisada é uma transação. Se você chamar a operação com um documento PDF ou TIFF contendo 100 páginas, a operação de leitura o contará como 100 transações e você será cobrado por 100 transações. Se você fez 50 chamadas para a operação e cada chamada enviou um documento com 100 páginas, você será cobrado por 50 X 100 = 5000 transações.

## <a name="step-2-the-get-read-results-operation"></a>Etapa 2: a operação obter resultados da leitura

A segunda etapa é chamar a operação [obter resultados de leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750) . Essa operação usa como entrada a ID da operação que foi criada pela operação de leitura. Ele retorna uma resposta JSON que contém um campo de **status** com os seguintes valores possíveis. Você chama essa operação iterativamente até que ela retorne com o valor **Succeeded** . Use um intervalo de 1 a 2 segundos para evitar exceder a taxa de solicitações por segundo (RPS).

|Campo| Type | Valores possíveis |
|:-----|:----:|:----|
|status | string | não iniciado: a operação não foi iniciada. |
| |  | em execução: a operação está sendo processada. |
| |  | falha: a operação falhou. |
| |  | êxito: a operação foi bem-sucedida. |

> [!NOTE]
> A camada gratuita limita a taxa de solicitação a 20 chamadas por minuto. A camada paga permite 10 solicitações por segundo (RPS) que podem ser aumentadas mediante solicitação. Use o canal de suporte do Azure ou sua equipe de conta para solicitar uma taxa mais alta de solicitação por segundo (RPS).

Quando o campo **status** tiver o valor **êxito** , a resposta JSON conterá o conteúdo de texto extraído de sua imagem ou documento. A resposta JSON mantém os agrupamentos de linhas originais de palavras reconhecidas. Ele inclui as linhas de texto extraídas e suas coordenadas de caixa delimitadora. Cada linha de texto inclui todas as palavras extraídas com suas coordenadas e pontuações de confiança.

> [!NOTE]
> Os dados enviados para a `Read` operação são temporariamente criptografados e armazenados em repouso por uma duração curta e, em seguida, excluídos. Isso permite que seus aplicativos recuperem o texto extraído como parte da resposta do serviço.

## <a name="sample-json-output"></a>Saída JSON de exemplo

Consulte o exemplo a seguir de uma resposta JSON bem-sucedida:

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

## <a name="natural-reading-order-output-latin-only"></a>Saída da ordem de leitura natural (somente latino)
Com a [API de visualização de leitura 3,2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005), especifique a ordem na qual as linhas de texto são geradas com o `readingOrder` parâmetro de consulta. Use `natural` para uma saída de ordem de leitura mais amigável, conforme mostrado no exemplo a seguir. Esse recurso só tem suporte em idiomas latinos.

:::image border type="content" source="./Images/ocr-reading-order-example.png" alt-text="Exemplo de ordem de leitura de OCR":::

## <a name="handwritten-classification-for-text-lines-latin-only"></a>Classificação manuscrita para linhas de texto (somente latino)
A resposta da [API de visualização 3,2 de leitura](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) inclui a classificação se cada linha de texto é do estilo de manuscrito ou não, juntamente com uma pontuação de confiança. Esse recurso só tem suporte em idiomas latinos. O exemplo a seguir mostra a classificação manuscrita para o texto na imagem.

:::image border type="content" source="./Images/ocr-handwriting-classification.png" alt-text="Exemplo de classificação de manuscrito de OCR":::

## <a name="select-pages-or-page-ranges-for-text-extraction"></a>Selecionar página (s) ou intervalos de página para extração de texto
Com a [API de visualização de leitura 3,2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005), para grandes documentos de várias páginas, use o `pages` parâmetro de consulta para especificar números de página ou intervalos de página para extrair texto apenas dessas páginas. O exemplo a seguir mostra um documento com 10 páginas, com texto extraído para ambos os casos – todas as páginas (1-10) e páginas selecionadas (3-6).

:::image border type="content" source="./Images/ocr-select-pages.png" alt-text="Saída de páginas selecionadas":::

## <a name="supported-languages"></a>Idiomas com suporte
As APIs de leitura dão suporte a um total de 73 idiomas para o texto do estilo de impressão. Consulte a lista completa de [idiomas com suporte para OCR](./language-support.md#optical-character-recognition-ocr). O OCR de estilo manuscrito tem suporte exclusivo para inglês.

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>Usar a API de nuvem ou implantar o local
As APIs de nuvem de leitura 3. x são a opção preferida para a maioria dos clientes devido à facilidade de integração e produtividade rápida pronta para uso. O Azure e o serviço de Pesquisa Visual Computacional tratam das necessidades de escala, desempenho, segurança de dados e conformidade enquanto você se concentra em atender às necessidades dos seus clientes.

Para a implantação local, o contêiner do [Docker de leitura (versão prévia)](./computer-vision-how-to-install-containers.md) permite que você implante os novos recursos de OCR em seu próprio ambiente local. Contêineres são excelentes para especificar requisitos de segurança e governança de dados.

## <a name="ocr-api"></a>API DE OCR

A [API de OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d) usa um modelo de reconhecimento mais antigo, dá suporte apenas a imagens e é executada de forma síncrona, retornando imediatamente com o texto detectado. Consulte os [idiomas com suporte para OCR](./language-support.md#optical-character-recognition-ocr) e, em seguida, ler API.

> [!NOTE]
> As operações do computador vison 2,0 RecognizeText estão no processo de serem preteridas em favor da nova API de leitura abordada neste artigo. Os clientes existentes devem [fazer a transição para o usando operações de leitura](upgrade-api-versions.md).

## <a name="next-steps"></a>Próximas etapas

- Introdução à [API REST do pesquisa Visual computacional ou aos guias de início rápido da biblioteca do cliente](./quickstarts-sdk/client-library.md).
- Saiba mais sobre a [API REST de leitura 3,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005).
- Saiba mais sobre a [API REST de visualização pública 3,2 de leitura](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) com suporte para um total de 73 idiomas.
