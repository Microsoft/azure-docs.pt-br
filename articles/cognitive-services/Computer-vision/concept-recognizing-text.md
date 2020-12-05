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
ms.openlocfilehash: 37a989082b63dc101bb519fea1cc4ef16c76ae49
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621528"
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

### <a name="read-32-preview-allows-selecting-pages"></a>Ler 3,2 visualização permite selecionar página (s)
Com a [API de visualização de leitura 3,2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-1/operations/5d986960601faab4bf452005), para grandes documentos de várias páginas, você pode fornecer números de página ou intervalos de página específicos como um parâmetro de entrada para extrair texto apenas dessas páginas. Esse é um novo parâmetro de entrada além do parâmetro de idioma opcional.

> [!NOTE]
> **Entrada de idioma** 
>
> A [chamada de leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) tem um parâmetro de solicitação opcional para o idioma. Esse é o código de idioma BCP-47 do texto no documento. A leitura dá suporte à identificação automática de idioma e a documentos multilíngues, portanto, forneça apenas um código de idioma se desejar forçar o processamento do documento como aquele idioma específico.

## <a name="the-read-call"></a>A chamada de leitura

A [chamada de leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) da API de leitura usa um documento de imagem ou PDF como entrada e extrai o texto de forma assíncrona. A chamada retorna com um campo de cabeçalho de resposta chamado `Operation-Location` . O `Operation-Location` valor é uma URL que contém a ID da operação a ser usada na próxima etapa.

|Cabeçalho de resposta| URL do resultado |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Billing** 
>
> A página de [preços de pesquisa Visual computacional](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) inclui o tipo de preço para leitura. Cada imagem ou página analisada é uma transação. Se você chamar a operação com um documento PDF ou TIFF contendo 100 páginas, a operação de leitura o contará como 100 transações e você será cobrado por 100 transações. Se você fez 50 chamadas para a operação e cada chamada enviou um documento com 100 páginas, você será cobrado por 50 X 100 = 5000 transações.

## <a name="the-get-read-results-call"></a>A chamada obter resultados da leitura

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
> Os dados enviados para a `Read` operação são temporariamente criptografados e armazenados em repouso e excluídos dentro de 48 horas. Isso permite que seus aplicativos recuperem o texto extraído como parte da resposta do serviço.

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
### <a name="read-32-preview-adds-text-line-style-latin-languages-only"></a>Ler 3,2 visualização adiciona estilo de linha de texto (somente idiomas latinos)
A [API de visualização de leitura 3,2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-1/operations/5d986960601faab4bf452005) gera um objeto de **aparência** que classifica se cada linha de texto é um estilo de impressão ou de manuscrito, juntamente com uma pontuação de confiança. Esse recurso tem suporte apenas para idiomas latinos.

Comece a usar a [API REST do pesquisa Visual computacional ou os guias de início rápido da biblioteca de cliente](./quickstarts-sdk/client-library.md) para começar a integrar os recursos de OCR em seus aplicativos.

## <a name="supported-languages-for-print-text"></a>Idiomas com suporte para imprimir texto
A [API de leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) dá suporte à extração de texto impresso nos idiomas inglês, espanhol, alemão, francês, italiano, Português e holandês.

Consulte os [idiomas com suporte](./language-support.md#optical-character-recognition-ocr) para obter a lista completa de idiomas com suporte para OCR.

### <a name="read-32-preview-adds-simplified-chinese-and-japanese"></a>Leia 3,2 visualização adiciona chinês simplificado e japonês
A [Visualização pública da API de leitura 3,2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-1/operations/5d986960601faab4bf452005) adiciona suporte para chinês simplificado e japonês. Se o seu cenário exigir suporte a mais idiomas, consulte a seção [API de OCR](#ocr-api) . 

## <a name="supported-languages-for-handwritten-text"></a>Idiomas com suporte para texto manuscrito
Atualmente, a operação de leitura dá suporte à extração de texto manuscrito exclusivamente em inglês.

## <a name="use-the-rest-api-and-sdk"></a>Usar a API REST e o SDK
A [API REST de leitura 3. x](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) é a opção preferida para a maioria dos clientes devido à facilidade de integração e produtividade rápida pronta para uso. O Azure e o serviço de Pesquisa Visual Computacional tratam das necessidades de escala, desempenho, segurança de dados e conformidade enquanto você se concentra em atender às necessidades dos seus clientes.

## <a name="deploy-on-premise-with-docker-containers"></a>Implantar no local com contêineres do Docker
O [contêiner do Docker de leitura (versão prévia)](./computer-vision-how-to-install-containers.md) permite que você implante os novos recursos de OCR em seu próprio ambiente local. Contêineres são excelentes para especificar requisitos de segurança e governança de dados.

## <a name="example-outputs"></a>Saídas de exemplo

### <a name="text-from-images"></a>Texto de imagens

A seguinte saída da API de leitura mostra o texto extraído de uma imagem com diferentes ângulos, cores e fontes de texto.

![Uma imagem de várias palavras em diferentes cores e ângulos, com texto extraído listado](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>Texto de documentos

A API de leitura também pode colocar documentos em PDF como entrada.

![Um documento de nota fiscal, com texto extraído listado](./Images/text-from-pdf-example.png)

### <a name="handwritten-text"></a>Texto manuscrito

A operação de leitura extrai texto manuscrito de imagens (atualmente somente em inglês).

![Uma imagem de uma anotação manuscrita, com texto extraído listado](./Images/handwritten-example.png)

### <a name="printed-text"></a>Texto impresso

A operação de leitura pode extrair texto impresso em vários idiomas diferentes.

![Uma imagem de um padrão em espanhol, com texto extraído listado](./Images/supported-languages-example.png)

### <a name="mixed-language-documents"></a>Documentos em idiomas mistos

A API de leitura dá suporte a imagens e documentos que contêm várias linguagens diferentes, normalmente conhecidas como documentos de idioma misto. Ele funciona classificando cada linha de texto do documento no idioma detectado antes de extrair seu conteúdo de texto.

![Uma imagem de frases em várias linguagens, com o texto extraído listado](./Images/mixed-language-example.png)

## <a name="ocr-api"></a>API DE OCR

A [API de OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d) usa um modelo de reconhecimento mais antigo, dá suporte apenas a imagens e é executada de forma síncrona, retornando imediatamente com o texto detectado. Consulte os [idiomas com suporte para OCR](./language-support.md#optical-character-recognition-ocr) e, em seguida, ler API.

## <a name="data-privacy-and-security"></a>Segurança e privacidade de dados

Assim como acontece com todos os serviços cognitivas, os desenvolvedores que usam os serviços de leitura/OCR devem estar cientes das políticas da Microsoft nos dados do cliente. Consulte a página serviços cognitivas na [central de confiabilidade da Microsoft](https://www.microsoft.com/trust-center/product-overview) para saber mais.

> [!NOTE]
> As operações do computador vison 2,0 RecognizeText estão no processo de serem preteridas em favor da nova API de leitura abordada neste artigo. Os clientes existentes devem [fazer a transição para o usando operações de leitura](upgrade-api-versions.md).

## <a name="next-steps"></a>Próximas etapas

- Introdução à [API REST do pesquisa Visual computacional ou aos guias de início rápido da biblioteca do cliente](./quickstarts-sdk/client-library.md).
- Saiba mais sobre a [API REST de leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005).
- Saiba mais sobre a [API REST de visualização pública 3,2 de leitura](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-1/operations/5d986960601faab4bf452005) com suporte adicional para chinês simplificado e japonês.