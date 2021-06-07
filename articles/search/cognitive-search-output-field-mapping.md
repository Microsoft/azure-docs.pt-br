---
title: Mapear entrada para campos de saída
titleSuffix: Azure Cognitive Search
description: Extrair e enriquecer campos de dados de origem e mapear para campos de saída em um índice de Pesquisa Cognitiva do Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 58bb87d5af785d3cffd96f3bd02477f97ed967a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001296"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>Como mapear campos de ia-ricos para um índice pesquisável

![Estágios do indexador](./media/cognitive-search-output-field-mapping/indexer-stages-output-field-mapping.png "estágios do indexador")

Neste artigo, você aprenderá como mapear campos de entrada enriquecidos para campos de saída em um índice pesquisável. Uma vez que você [definiu um conjunto de qualificações](cognitive-search-defining-skillset.md), mapeie os campos de saída de qualquer habilidade diretamente a contribuição de valores para um determinado campo no índice de pesquisa.

Os mapeamentos de campo de saída são necessários para mover o conteúdo de documentos aprimorados para o índice.  O documento aprimorado é realmente uma árvore de informações e, embora haja suporte para tipos complexos no índice, às vezes você pode querer transformar as informações da árvore aprimorada em um tipo mais simples (por exemplo, uma matriz de cadeias de caracteres). Os mapeamentos de campo de saída permitem que você execute transformações de forma de dados por meio do nivelamento de informações. Os mapeamentos de campo de saída sempre ocorrem após a execução do Configurador de habilidades, embora seja possível que esse estágio seja executado mesmo se nenhum configurador de qualificações for definido.

Exemplos de mapeamentos de campo de saída:

* Como parte do seu qualificante, você extraiu os nomes das organizações mencionadas em cada uma das páginas do documento. Agora você deseja mapear cada um desses nomes de organização em um campo no índice do tipo EDM. Collection (EDM. String).

* Como parte do seu Skill, você produziu um novo nó chamado "Document/translated_text". Você gostaria de mapear as informações neste nó para um campo específico em seu índice.

* Você não tem um configurador de qualificações, mas está indexando um tipo complexo de um banco de dados Cosmos DB. Você gostaria de chegar a um nó nesse tipo complexo e mapeá-lo em um campo no índice.

> [!NOTE]
> Habilitamos recentemente a funcionalidade das funções de mapeamento em mapeamentos de campo de saída. Para obter mais detalhes sobre as funções de mapeamento, consulte [funções de mapeamento de campos](./search-indexer-field-mappings.md#field-mapping-functions)

## <a name="use-outputfieldmappings"></a>Use outputFieldMappings

Para mapear campos, adicione `outputFieldMappings` à sua definição do indexador, conforme mostrado abaixo:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
```

O corpo da solicitação é estruturado da seguinte maneira:

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions",
            "mappingFunction": {
                "name": "base64Decode"
            }
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```

Para cada mapeamento de campo de saída, defina o local dos dados na árvore de documentos enriquecedo (sourceFieldName) e o nome do campo como referenciado no índice (targetFieldName).

## <a name="flattening-information-from-complex-types"></a>Nivelamento de informações de tipos complexos 

O caminho em um sourceFieldName pode representar um elemento ou vários elementos. No exemplo acima, ```/document/content/sentiment``` representa um único valor numérico, enquanto ```/document/content/organizations/*/description``` representa várias descrições de organização. 

Em casos onde há vários elementos, são "simples" em uma matriz que contém cada um dos elementos. 

Mais concretamente, para o ```/document/content/organizations/*/description``` exemplo, os dados no campo *descrições* seriam uma matriz simples de descrições antes que ele obtém indexado:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

Esse é um princípio importante, então forneceremos outro exemplo. Imagine que você tenha uma matriz de tipos complexos como parte da árvore de enriquecimento. Digamos que haja um membro chamado customEntities que tenha uma matriz de tipos complexos, como o descrito abaixo.

```json
"document/customEntities": 
[
    {
        "name": "heart failure",
        "matches": [
            {
                "text": "heart failure",
                "offset": 10,
                "length": 12,
                "matchDistance": 0.0
            }
        ]
    },
    {
        "name": "morquio",
        "matches": [
            {
                "text": "morquio",
                "offset": 25,
                "length": 7,
                "matchDistance": 0.0
            }
        ]
    }
    //...
]
```

Vamos supor que o índice tenha um campo chamado ' doenças ' do tipo Collection (EDM. String), em que você gostaria de armazenar cada um dos nomes das entidades. 

Isso pode ser feito facilmente usando o símbolo " \* ", da seguinte maneira:

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

Essa operação simplesmente "achata" cada um dos nomes dos elementos customEntities em uma única matriz de cadeias de caracteres como esta:

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>Próximas etapas
Após mapear os campos enriquecidos para campos de pesquisa, defina os atributos de campo para cada um dos campos pesquisáveis [como parte da definição do índice](search-what-is-an-index.md).

Para obter mais informações sobre mapeamento de campo, consulte [mapeamentos de campo no Azure pesquisa cognitiva indexadores](search-indexer-field-mappings.md).