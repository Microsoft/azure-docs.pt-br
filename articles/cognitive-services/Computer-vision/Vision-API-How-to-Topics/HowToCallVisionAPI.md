---
title: Chamar a API da Análise de Imagens
titleSuffix: Azure Cognitive Services
description: Saiba como chamar a API de Análise de Imagem usando a API REST nos Serviços Cognitivos do Azure.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 25907319be5fc79cf4c7d6e46499bacce54cea77
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286429"
---
# <a name="call-the-image-analysis-api"></a>Chamar a API da Análise de Imagens

Este artigo demonstra como chamar a API de Análise de Imagem usando a API REST. Os exemplos foram escritos em C# com a biblioteca de clientes da API de Análise de Imagem e como chamadas HTTP POST ou GET. O artigo se concentra em:

- Como obter marcas, uma descrição e categorias
- Como obter informações específicas do domínio ou "celebridades"

Os exemplos neste artigo demonstram os seguintes recursos:

* Analisando uma imagem para retornar uma matriz de marcas e uma descrição
* Análise de uma imagem com um modelo específico a um domínio (especificamente, modelo "celebridades") para retornar o resultado correspondente no JSON

Os recursos oferecem as seguintes opções:

- **Opção 1**: Análise de Escopo – analisar apenas um modelo especificado
- **Opção 2**: Análise Avançada – analisar para fornecer mais detalhes usando a [taxonomia de 86 categorias](../Category-Taxonomy.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Depois de obter sua assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Crie um recurso da Pesquisa Visual Computacional"  target="_blank">crie um recurso da Pesquisa Visual Computacional </a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo ao serviço de Pesquisa Visual Computacional. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.
* Uma URL de imagem ou um caminho para uma imagem armazenada localmente
* Métodos de entrada compatíveis: binário da imagem bruta na forma de um aplicativo/fluxo de octeto ou uma URL da imagem
* Formatos de arquivo de imagem compatíveis: JPEG, PNG, GIF e BMP
* Tamanho do arquivo de imagem: 4 MB ou menos
* Dimensões da imagem: 50 &times; 50 pixels ou mais
  
## <a name="authorize-the-api-call"></a>Autorizar a chamada à API

Cada chamada à API de Análise de Imagem exige uma chave de assinatura. Essa chave deve ser passada por um parâmetro de cadeia de caracteres de consulta ou especificada no cabeçalho da solicitação.

Você pode passar a chave de assinatura seguindo qualquer um destes procedimentos:

* Transmita-a por uma cadeia de consulta, como neste exemplo:

  ```
  https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
  ```

* Especifique-a no cabeçalho de solicitação HTTP:

  ```
  ocp-apim-subscription-key: <Your subscription key>
  ```

* Quando você usa a biblioteca de cliente, passe a chave usando o construtor de ComputerVisionClient e especifique a região em uma propriedade do cliente:

    ```
    var visionClient = new ComputerVisionClient(new ApiKeyServiceClientCredentials("Your subscriptionKey"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com"
    }
    ```

## <a name="upload-an-image-to-the-image-analysis-service"></a>Carregar uma imagem no serviço de Análise de Imagem

A maneira básica de executar a chamada à API de Análise de Imagem é carregando uma imagem diretamente para retornar marcas, uma descrição e celebridades. Você faz isso enviando uma solicitação "POST" com a imagem binária no corpo HTTP junto com os dados lidos da imagem. O método de upload é o mesmo para todas as chamadas à API de Análise de Imagem. A única diferença são os parâmetros de consulta que você especifica. 

Para uma imagem especificada, obtenha marcas e uma descrição usando uma das seguintes opções:

### <a name="option-1-get-a-list-of-tags-and-a-description"></a>Opção 1: obter uma lista de marcas e uma descrição

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using System.IO;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

ImageAnalysis imageAnalysis;
var features = new VisualFeatureTypes[] { VisualFeatureTypes.Tags, VisualFeatureTypes.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageAnalysis = await visionClient.AnalyzeImageInStreamAsync(fs, features);
}
```

### <a name="option-2-get-a-list-of-tags-only-or-a-description-only"></a>Opção 2: obter somente uma lista de marcas ou somente uma descrição

Para somente marcas, execute:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/tag?subscription-key=<Your subscription key>
var tagResults = await visionClient.TagImageAsync("http://contoso.com/example.jpg");
```

Pra somente descrição, execute:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/describe?subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageDescription = await visionClient.DescribeImageInStreamAsync(fs);
}
```

## <a name="get-domain-specific-analysis-celebrities"></a>Obter análise específica de domínio (celebridades)

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Opção 1: Análise de escopo – analisar apenas um modelo especificado
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

Para essa opção, todos os outros parâmetros de consulta {visualFeatures, details} não são válidos. Se desejar ver todos os modelos com suporte, use:

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.1/models 
var models = await visionClient.ListModelsAsync();
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-86-categories-taxonomy"></a>Opção 2: Análise avançada – analisar para fornecer mais detalhes usando a taxonomia de 86 categorias

Para aplicativos nos quais você deseja obter análise de imagem genérica, além de detalhes de um ou mais modelos específicos a um domínio, expandimos a API v1 usando o parâmetro de consulta de modelos.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?details=celebrities
```

Ao invocar esse método, primeiro você chama o classificador de [86 categorias](../Category-Taxonomy.md). Se qualquer uma das categorias corresponde àquelas de um modelo conhecido/correspondente, uma segunda passagem de invocações do classificador ocorre. Por exemplo, se "details=all" ou "details" inclui "celebrities", você chama o modelo celebridades depois de chamar o classificador de 86 categorias. O resultado inclui a categoria pessoa. Em contraste com a Opção 1, esse método aumenta a latência para usuários interessados em celebridades.

Neste caso, todos os parâmetros de consulta v1 comportam-se da mesma maneira. Se você não especificar visualFeatures=categories, ele será habilitado implicitamente.

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>Recuperar e entender a saída JSON para análise

Aqui está um exemplo:

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

Campo | Type | Conteúdo
------|------|------|
Marcas  | `object` | O objeto de nível superior para uma matriz de marcas.
tags[].Name | `string`    | A palavra-chave do classificador de marcas.
tags[].Score    | `number`    | A pontuação de confiança, entre 0 e 1.
descrição     | `object`    | Objeto de nível superior de uma descrição.
description.tags[] |    `string`    | Lista de marcas.  Se houver confiança insuficiente na capacidade de produzir uma legenda, talvez as marcas sejam as únicas informações disponíveis para o chamador.
description.captions[].text    | `string`    | Uma frase que descreve a imagem.
description.captions[].confidence    | `number`    | A pontuação de confiança para a frase.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>Recuperar e compreender a saída JSON de modelos específicos de domínio

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Opção 1: Análise de escopo – analisar apenas um modelo especificado

A saída é uma matriz de marcas, conforme mostrado no exemplo a seguir:

```json
{  
  "result":[  
    {  
      "name":"golden retriever",
      "score":0.98
    },
    {  
      "name":"Labrador retriever",
      "score":0.78
    }
  ]
}
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-the-86-categories-taxonomy"></a>Opção 2: Análise avançada – analisar para fornecer mais detalhes usando a taxonomia de 86 categorias

Para modelos específicos de domínio usando a opção 2 (análise avançada), o tipo de retorno de categorias é estendido, conforme mostrado no exemplo a seguir:

```json
{  
  "requestId":"87e44580-925a-49c8-b661-d1c54d1b83b5",
  "metadata":{  
    "width":640,
    "height":430,
    "format":"Jpeg"
  },
  "result":{  
    "celebrities":[  
      {  
        "name":"Richard Nixon",
        "faceRectangle":{  
          "left":107,
          "top":98,
          "width":165,
          "height":165
        },
        "confidence":0.9999827
      }
    ]
  }
}
```

O campo de categorias é uma lista de uma ou mais das [86 categorias](../Category-Taxonomy.md) na taxonomia original. As categorias que terminam em um sublinhado correspondem a essa categoria e seus filhos (por exemplo, people_" ou people_group, para o modelo celebridades).

Campo    | Type    | Conteúdo
------|------|------|
Categorias | `object`    | Objeto de nível superior.
categories[].name     | `string`    | O nome da lista de taxonomia de 86 categorias.
categories[].score    | `number`    | A pontuação de confiança, entre 0 e 1.
categories[].detail     | `object?`      | (Opcional) O objeto de detalhe.

Se várias categorias forem correspondentes (por exemplo, o classificador de 86 categorias retorna uma pontuação para people_ e people_young se model=celebrities), os detalhes serão anexados à correspondência de nível mais geral ("people_", neste exemplo).

## <a name="error-responses"></a>Respostas de erro

Esses erros são idênticos aos de vision.analyze, com o erro adicional NotSupportedModel (HTTP 400), que pode ser retornado nos cenários da Opção 1 e da Opção 2. Para a Opção 2 (análise avançada), se qualquer um dos modelos especificados nos detalhes não for reconhecido, a API retornará um NotSupportedModel, mesmo que um ou mais deles seja válido. Para descobrir que modelos são compatíveis, chame listModels.

## <a name="next-steps"></a>Próximas etapas

Para usar a API REST, acesse a [Referência da API de Análise de Imagem](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b).
