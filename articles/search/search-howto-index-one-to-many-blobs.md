---
title: Blobs de índice que contêm vários documentos
titleSuffix: Azure Cognitive Search
description: Rastreie BLOBs do Azure para conteúdo de texto usando o indexador de blob Pesquisa Cognitiva do Azure, em que cada blob pode produzir um ou mais documentos de índice de pesquisa.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ea22b3cff8a0303c4e6698db4090df0f5ed2153a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99430973"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Indexando BLOBs para produzir vários documentos de pesquisa

Por padrão, um indexador de blob tratará o conteúdo de um blob como um único documento de pesquisa. Se você quiser uma representação mais granular do blob em um índice de pesquisa, poderá definir valores de **parsingMode** para criar vários documentos de pesquisa de um blob. Os valores de **parsingMode** que resultam em muitos documentos de pesquisa incluem `delimitedText` (para [CSV](search-howto-index-csv-blobs.md)) e `jsonArray` ou `jsonLines` (para [JSON](search-howto-index-json-blobs.md)).

Quando você usa qualquer um desses modos de análise, os novos documentos de pesquisa que surgem devem ter chaves de documento exclusivas e um problema surge na determinação do local de origem desse valor. O blob pai tem pelo menos um valor exclusivo na forma de `metadata_storage_path property` , mas se ele contribuir com esse valor para mais de um documento de pesquisa, a chave não será mais exclusiva no índice.

Para resolver esse problema, o indexador de blob gera um `AzureSearch_DocumentKey` que identifica exclusivamente cada documento de pesquisa filho criado a partir do pai de blob único. Este artigo explica como esse recurso funciona.

## <a name="one-to-many-document-key"></a>Chave de documento de um para muitos

Cada documento que aparece em um índice de Pesquisa Cognitiva do Azure é identificado exclusivamente por uma chave de documento. 

Quando nenhum modo de análise for especificado, e se não houver nenhum [mapeamento de campo explícito](search-indexer-field-mappings.md) na definição do indexador para a chave do documento de pesquisa, o indexador de blob mapeará automaticamente o `metadata_storage_path property` como a chave do documento. Esse mapeamento garante que cada blob apareça como um documento de pesquisa distinto, e economiza você a etapa de ter que criar esse mapeamento de campo por conta própria (normalmente, somente os campos com nomes e tipos idênticos são mapeados automaticamente).

Ao usar qualquer um dos modos de análise listados acima, um blob é mapeado para "muitos" documentos de pesquisa, tornando uma chave de documento exclusivamente baseada em metadados de blob inadequados. Para superar essa restrição, o Azure Pesquisa Cognitiva é capaz de gerar uma chave de documento "um para muitos" para cada entidade individual extraída de um blob. Essa propriedade é denominada AzureSearch_DocumentKey e é adicionada a cada entidade individual extraída do blob. É garantido que o valor dessa propriedade seja exclusivo para cada entidade individual entre os BLOBs e as entidades aparecerão como documentos de pesquisa separados.

Por padrão, quando nenhum mapeamento de campo explícito para o campo de índice de chave é especificado, o `AzureSearch_DocumentKey` é mapeado para ele, usando a `base64Encode` função de mapeamento de campo.

## <a name="example"></a>Exemplo

Suponha que você tenha uma definição de índice com os seguintes campos:

+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

E seu contêiner de BLOBs tem BLOBs com a seguinte estrutura:

_Blob1.jsem_

```json
{ "temperature": 100, "pressure": 100, "timestamp": "2020-02-13T00:00:00Z" }
{ "temperature" : 33, "pressure" : 30, "timestamp": "2020-02-14T00:00:00Z" }
```

_Blob2.jsem_

```json
{ "temperature": 1, "pressure": 1, "timestamp": "2019-01-12T00:00:00Z" }
{ "temperature" : 120, "pressure" : 3, "timestamp": "2017-05-11T00:00:00Z" }
```

Quando você cria um indexador e define o **parsingMode** como `jsonLines` -sem especificar nenhum mapeamento de campo explícito para o campo de chave, o mapeamento a seguir será aplicado implicitamente.

```http
{
    "sourceFieldName" : "AzureSearch_DocumentKey",
    "targetFieldName": "id",
    "mappingFunction": { "name" : "base64Encode" }
}
```

Essa configuração resultará em chaves de documento com ambigüidade, semelhantes à ilustração a seguir (ID codificada em base64 reduzida para fins de brevidade).

| ID | temperatura | pressão |  timestamp |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2020-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2020-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2019-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2017-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapeamento de campo personalizado para campo de chave de índice

Supondo a mesma definição de índice do exemplo anterior, suponha que seu contêiner de BLOBs tenha BLOBs com a seguinte estrutura:

_Blob1.jsem_

```json
recordid, temperature, pressure, timestamp
1, 100, 100,"2019-02-13T00:00:00Z" 
2, 33, 30,"2019-02-14T00:00:00Z" 
```

_Blob2.jsem_

```json
recordid, temperature, pressure, timestamp
1, 1, 1,"2018-01-12T00:00:00Z" 
2, 120, 3,"2013-05-11T00:00:00Z" 
```

Quando você cria um indexador com `delimitedText` **parsingMode**, pode parecer natural configurar uma função de mapeamento de campo para o campo de chave da seguinte maneira:

```http
{
    "sourceFieldName" : "recordid",
    "targetFieldName": "id"
}
```

No entanto, esse mapeamento _não_ resultará em quatro documentos exibidos no índice, pois o `recordid` campo não é exclusivo _entre BLOBs_. Portanto, é recomendável que você use o mapeamento de campo implícito aplicado da `AzureSearch_DocumentKey` propriedade ao campo de índice de chave para os modos de análise "um para muitos".

Se você quiser configurar um mapeamento de campo explícito, certifique-se de que _SourceField_ seja distinto para cada entidade individual **em todos os BLOBs**.

> [!NOTE]
> A abordagem usada pelo `AzureSearch_DocumentKey` de assegurar a exclusividade por entidade extraída está sujeita a alterações e, portanto, você não deve depender de seu valor para as necessidades do seu aplicativo.

## <a name="next-steps"></a>Próximas etapas

Se você ainda não estiver familiarizado com a estrutura básica e o fluxo de trabalho da indexação de BLOBs, examine a [indexação do armazenamento de BLOBs do Azure com o azure pesquisa cognitiva](search-howto-index-json-blobs.md) primeiro. Para obter mais informações sobre os modos de análise para diferentes tipos de conteúdo de BLOB, examine os artigos a seguir.

> [!div class="nextstepaction"]
> [Indexando BLOBs CSV](search-howto-index-csv-blobs.md) 
>  [Indexando BLOBs JSON](search-howto-index-json-blobs.md)
