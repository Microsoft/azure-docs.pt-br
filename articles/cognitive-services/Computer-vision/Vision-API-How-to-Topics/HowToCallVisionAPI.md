---
title: 'Exemplo: Chamar a API Analisar imagem – Pesquisa Visual Computacional'
titleSuffix: Azure Cognitive Services
description: Saiba como chamar a API da Pesquisa Visual Computacional usando o REST nos Serviços Cognitivos do Azure.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 03/21/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 35e6ad922ab54748165fcf8e273d93ee44bc42cc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564533"
---
# <a name="example-how-to-call-the-computer-vision-api"></a>Exemplo: Como chamar a API da Pesquisa Visual Computacional

Este guia demonstra como chamar a API da Pesquisa Visual Computacional usando o REST. Os exemplos são escritos em C# usando a biblioteca de clientes da API da Pesquisa Visual Computacional e como chamadas HTTP POST/GET. Nosso foco será:

- Como obter "Tags", "Descrição" e "Categorias".
- Como obter informações "específicas a um domínio" (celebridades).

## <a name="prerequisites"></a>Pré-requisitos

- URL da imagem ou do caminho para a imagem armazenada localmente.
- Suporte para métodos de entrada: Binário da imagem bruta na forma de um aplicativo/fluxo de octeto ou uma URL da imagem
- Formatos de imagem com suporte: JPEG, PNG, GIF, BMP
- Tamanho do arquivo de imagem: Menos de 4 MB
- Dimensões da imagem: Mais de 50 x 50 pixels
  
Nos exemplos abaixo, os seguintes recursos são demonstrados:

1. Análise de uma imagem e obtenção de uma matriz de tags e um descrição retornada.
2. Análise de uma imagem com um modelo específico a um domínio (especificamente, modelo de "celebridades") e obtenção do resultado correspondente no reajuste do JSON.

Os recursos são divididos em:

- **Opção um:** Análise de Escopo – analisar apenas um dado modelo
- **Opção dois:** Análise Avançada – analisar para fornecer mais detalhes com a [taxonomia de 86 categorias](../Category-Taxonomy.md)
  
## <a name="authorize-the-api-call"></a>Autorizar a chamada à API

Toda chamada à API da Pesquisa Visual Computacional requer uma chave de assinatura. Essa chave precisa ser passada por um parâmetro de cadeia de caracteres de consulta ou especificada no cabeçalho da solicitação.

Para obter uma chave de avaliação gratuita, veja [Experimentar os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Ou siga as instruções em [Criar uma conta dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar a Pesquisa Visual Computacional e obter sua chave.

1. Passando a chave de assinatura por meio de uma cadeia de consulta, veja abaixo como um exemplo da API da Pesquisa Visual Computacional:

```https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>```

1. A passagem da chave de assinatura também pode ser especificada no cabeçalho da solicitação HTTP:

```ocp-apim-subscription-key: <Your subscription key>```

1. Ao usar uma biblioteca de clientes, a chave de assinatura é passada por meio do construtor de VisionServiceClient:

```var visionClient = new VisionServiceClient("Your subscriptionKey");```

## <a name="upload-an-image-to-the-computer-vision-api-service-and-get-back-tags-descriptions-and-celebrities"></a>Carregar uma imagem para o serviço API da Pesquisa Visual Computacional e retornar as marcas, descrições e celebridades

A maneira básica de realizar a chamada à API da Pesquisa Visual Computacional é carregar uma imagem diretamente. Isso é feito enviando uma solicitação "POST" com o tipo de conteúdo application/octet-stream junto com os dados lidos da imagem. Para "Tags" e "Descrição", este método de upload será o mesmo para todas as chamadas à API da Pesquisa Visual Computacional. A única diferença será os parâmetros de consulta especificados pelo usuário. 

Veja como obter "Tags" e "Descrição" para uma determinada imagem:

**Opção um:** Obter a lista de "Marcas" e uma "Descrição"

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using Microsoft.ProjectOxford.Vision;
using Microsoft.ProjectOxford.Vision.Contract;
using System.IO;

AnalysisResult analysisResult;
var features = new VisualFeature[] { VisualFeature.Tags, VisualFeature.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.AnalyzeImageAsync(fs, features);
}
```

**Opção dois:** obter apenas a lista de "Tags" ou apenas a lista de "Descrição":

###### <a name="tags-only"></a>Somente marcas:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/tag&subscription-key=<Your subscription key>
var analysisResult = await visionClient.GetTagsAsync("http://contoso.com/example.jpg");
```

###### <a name="description-only"></a>Somente descrição:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/describe&subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.DescribeAsync(fs);
}
```

### <a name="get-domain-specific-analysis-celebrities"></a>Obter análise específica de domínio (celebridades)

**Opção um:** Análise de Escopo – analisar apenas um dado modelo
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

Para essa opção, todos os outros parâmetros de consulta {visualFeatures, details} não são válidos. Se desejar ver todos os modelos com suporte, use:

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.0/models 
var models = await visionClient.ListModelsAsync();
```

**Opção dois:** Análise Avançada – analisar para fornecer mais detalhes com a [taxonomia de 86 categorias](../Category-Taxonomy.md)

Para aplicativos nos quais você deseja obter análise de imagem genérica, além de detalhes de um ou mais modelos específicos a um domínio, expandimos a API v1 com o parâmetro de consulta de modelos.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?details=celebrities
```

Quando esse método é invocado, chamaremos o classificador de 86 categorias primeiro. Se qualquer uma das categorias corresponder àquelas de um modelo conhecido/correspondente, uma segunda passagem de invocações do classificador ocorrerá. Por exemplo, se "details=all" ou "details" incluir "celebridades", chamaremos o modelo de celebridades após o classificador de 86 categorias ser chamado, e o resultado incluirá a pessoa da categoria. Isso aumentará a latência dos usuários interessados em celebridades, em comparação com a Opção um.

Todos os parâmetros de consulta v1 se comportarão da mesma maneira nesse caso.  Se visualFeatures=categories não for especificado, será habilitado implicitamente.

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
Marcas  | `object` | Objeto de nível superior para matriz de tags
tags[].Name | `string`  | Palavra-chave do classificador de tags
tags[].Score    | `number`  | Pontuação de confiança, entre 0 e 1.
Descrição  | `object` | Objeto de nível superior de uma descrição.
description.tags[] |    `string`    | Lista de tags.  Se houver confiança insuficiente na capacidade de produzir uma legenda, talvez as tags sejam as únicas informações disponíveis para o chamador.
description.captions[].text | `string`  | Uma frase que descreve a imagem.
description.captions[].confidence   | `number`  | Confiança da frase.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>Recuperar e compreender a saída JSON de modelos específicos de domínio

**Opção um:** Análise de Escopo – analisar apenas um dado modelo

A saída será uma matriz de tags, como este exemplo:

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

**Opção dois:** Análise Avançada – analisar para fornecer mais detalhes com a taxonomia de 86 categorias

Para modelos específicos a um domínio que usam a Opção dois (análise avançada), o tipo de retorno das categorias é ampliado. Há um exemplo a seguir:

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

O campo de categorias é uma lista de uma ou mais das [86 categorias](../Category-Taxonomy.md) na taxonomia original. Observe também que as categorias que terminam com um sublinhado corresponderão à categoria e seus filhos (por exemplo, people_ assim como people_group, para o modelo de celebridades).

Campo   | Type  | Conteúdo
------|------|------|
Categorias | `object`   | Objeto de nível superior
categories[].name    | `string` | Nome da taxonomia de 86 categorias
categories[].score  | `number`  | Pontuação de confiança, entre 0 e 1
categories[].detail  | `object?`      | Objeto de detalhes opcional

Observe que, se várias categorias forem correspondentes (por exemplo, o classificador de 86 categorias retorna uma pontuação para people_ e people_young se model=celebrities), os detalhes serão anexados à correspondência de nível mais geral (people_, nesse exemplo).

## <a name="errors-responses"></a>Respostas de erros

Essas são idênticas a vision.analyze, com o erro adicional do erro NotSupportedModel (HTTP 400), que pode ser retornado nos cenários Opção um e Opção dois. Para a Opção dois (Análise avançada), se qualquer um dos modelos especificados nos detalhes não for reconhecido, a API retornará um NotSupportedModel, mesmo se um ou mais deles forem válidos.  Os usuários podem chamar listModels para descobrir para quais modelos há suporte.

## <a name="next-steps"></a>Próximas etapas

Para usar a API REST, acesse [Referência da API da Pesquisa Visual Computacional](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
