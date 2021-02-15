---
title: Habilidades cognitivas de reconhecimento de entidade
titleSuffix: Azure Cognitive Search
description: Extrair diferentes tipos de entidades de texto em um pipeline de enriquecimento no Azure Pesquisa Cognitiva.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: ddfca965ea32ca726df39f894ba45f9580225d9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542454"
---
#    <a name="entity-recognition-cognitive-skill"></a>Habilidades cognitivas de reconhecimento de entidade

A habilidade **Entity Recognition** extrai entidades de diferentes tipos do texto. Essa habilidade usa os modelos de machine learning fornecidos pela [Análise de Texto](../cognitive-services/text-analytics/overview.md) nos Serviços Cognitivos.

> [!NOTE]
> À medida que expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, você precisará [anexar um recurso de Serviços Cognitivos faturável](cognitive-search-attach-cognitive-services.md). As cobranças são geradas ao chamar APIs nos Serviços Cognitivos e para a extração de imagem, como parte do estágio de quebra de documento na Pesquisa Cognitiva do Azure. Não há encargos para extração de texto em documentos.
>
> A execução de habilidades integradas é cobrada nos [preços pagos conforme o uso dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes. O preço da extração de imagem é descrito na [página de preços da Pesquisa Cognitiva do Azure](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um registro deve ser de 50.000 caracteres conforme medido por [`String.Length`](/dotnet/api/system.string.length). Se você precisar interromper o backup de seus dados antes de enviá-la para o extrator de frases-chave, considere o uso de [habilidade de Texto Dividido](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas e são todos opcionais.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| `categories`    | Matriz de categorias que devem ser extraídas.  Os tipos possíveis de categoria: `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"`. Se nenhuma categoria for fornecida, todos os tipos são retornados.|
| `defaultLanguageCode` |    Código de idioma do texto de entrada. Há suporte para os seguintes idiomas: `ar, cs, da, de, en, es, fi, fr, hu, it, ja, ko, nl, no, pl, pt-BR, pt-PT, ru, sv, tr, zh-hans` . Nem todas as categorias de entidade têm suporte para todos os idiomas; consulte a observação abaixo.|
| `minimumPrecision` | Um valor entre 0 e 1. Se a pontuação de confiança (na `namedEntities` saída) for menor que esse valor, a entidade não será retornada. O padrão é 0. |
| `includeTypelessEntities` | Defina como `true` se você quiser reconhecer entidades bem conhecidas que não se ajustam às categorias atuais. As entidades reconhecidas são retornadas no `entities` campo saída complexa. Por exemplo, "Windows 10" é uma entidade conhecida (um produto), mas como "produtos" não é uma categoria com suporte, essa entidade seria incluída no campo de saída de entidades. O padrão é `false` |


## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada      | Descrição                   |
|---------------|-------------------------------|
| `languageCode`    | Opcional. O padrão é `"en"`.  |
| `text`          | O texto para analisar.          |

## <a name="skill-outputs"></a>Saídas de habilidades

> [!NOTE]
> Não há suporte para todas as categorias de entidade em todos os idiomas. Os `"Person"` `"Location"` tipos de categoria de entidade,, e `"Organization"` têm suporte para a lista completa de idiomas acima. Somente _de_, _en_, _es_, _fr_e _zh-Hans_ dão suporte à extração de `"Quantity"` `"Datetime"` tipos,, `"URL"` e `"Email"` . Para obter mais informações, consulte [suporte a idiomas e regiões para o API de análise de texto](../cognitive-services/text-analytics/language-support.md).  

| Nome de saída      | Descrição                   |
|---------------|-------------------------------|
| `persons`       | Uma matriz de cadeias de caracteres onde cada cadeia de caracteres representa o nome de uma pessoa. |
| `locations`  | Uma matriz de cadeias de caracteres onde cada cadeia de caracteres representa um local. |
| `organizations`  | Uma matriz de cadeias de caracteres onde cada cadeia de caracteres representa uma organização. |
| `quantities`  | Um array de strings onde cada cadeia de caracteres representa uma quantidade. |
| `dateTimes`  | Uma matriz de cadeia de caracteres onde cada cadeia de caracteres representa um valor DateTime (como aparece no texto). |
| `urls` | Uma matriz de cadeia de caracteres onde cada cadeia de caracteres representa um URL |
| `emails` | Uma matriz de cadeia de caracteres onde cada cadeia de caracteres representa um e-mail |
| `namedEntities` | Uma matriz de tipos complexos que contêm os seguintes campos: <ul><li>category</li> <li>valor (o nome real da entidade)</li><li>deslocamento (o local onde ele foi encontrado no texto)</li><li>confiança (maior valor significa que é mais uma entidade real)</li></ul> |
| `entities` | Uma matriz de tipos complexos que contém informações ricas sobre as entidades extraídas do texto, com os seguintes campos <ul><li> nome (o nome da entidade real. Isso representa um formulário "normalizado")</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (um link para a página da Wikipedia para a entidade)</li><li>bingId</li><li>tipo (a categoria da entidade reconhecida)</li><li>subType (disponível apenas para algumas categorias; oferece uma exibição mais granular do tipo de entidade)</li><li> correspondências (uma coleção complexa que contém)<ul><li>texto (texto bruto para a entidade)</li><li>deslocamento (o local onde ele foi encontrado)</li><li>comprimento (o comprimento do texto bruto de entidade)</li></ul></li></ul> |

##    <a name="sample-definition"></a>Definição de exemplo

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "minimumPrecision": 0.5,
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
      }
    ]
  }
```
##    <a name="sample-input"></a>Entrada de exemplo

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Saída de exemplo

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": 0.98
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
          }
        ]
      }
    }
  ]
}
```

Observe que os deslocamentos retornados para entidades na saída dessa habilidade são retornados diretamente da [API de análise de texto](../cognitive-services/text-analytics/overview.md), o que significa que, se você estiver usando-os para indexar na cadeia de caracteres original, deverá usar a classe [StringInfo](/dotnet/api/system.globalization.stringinfo) no .net para extrair o conteúdo correto.  [Mais detalhes podem ser encontrados aqui.](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="error-cases"></a>Casos de erro
Se o código do idioma do documento não for suportado, um erro será retornado e nenhuma entidade será extraída.

## <a name="see-also"></a>Confira também

+ [Habilidades internas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)